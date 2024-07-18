---
title: 理一下 C# 和 Kotlin 的泛型
draft: false
date: 2024-07-18 15:05:59
tags:
---
> JVM 这泛型比不上模板一点，连 C# 的都不如
> ——残像


以下笔记摘自 D.N.Code 群聊天记录。

如有错误请指出。

<!-- more -->

## 来对比一下 Kotlin 和 C# 的代码
用 Kotlin 写一段代码：
```kotlin
interface T0<out T>

open class A

open class B : A()

open class C : T0<A>

open class D :
	C(),
	T0<B>
```

这段代码会在类 `D` 报一个错： `T0` 的类型参数 `T` 不一致，不能同时是 `A` 和 `B`。

```
Type parameter T of 'T0' has inconsistent values: A, B.
```

究其原因，类 `D` 同时试图实现 `T0<A>` 和 `T0<B>`，在 Kotlin 里这是不允许的。类 `C` 在定义里已经实现了 `T0<A>`，把 `T` 固定为了 `A`。然后 `D` 继承了 `C`，并试图再次实现 `T0<B>`，也即把 `T` 改为 `B`。

但把这段代码转写为 C# ：
```csharp
interface I<out T>
{
    T foo();
}

class A { }

class B : A { }

class C : I<A>
{
    public A foo() => throw new NotImplementedException();
}

class D : C, I<B>
{
    B I<B>.foo() => throw new NotImplementedException();
}
```

却是没有问题，可以正常过编译的。
如果把 `I<A>` 加回来，编译器还会直接告诉你“这个代码是多余的，因为 `D` 已经继承 `C` 了”。是的。C# 里可以同时实现 `T0<A>` 和 `T0<B>`。

![加回来](Pastedimage20240718160911.png)

## 原因
究其原因。
1. Kotlin 运行在 JVM 上。在 JVM 平台上，由于类型擦除（type erasure）（应该还有一些其他类型系统的限制）的存在，在编译时泛型类型信息会被移除，导致泛型接口实际上全都是 `I<object>`，就导致在运行时 JVM 无法知道泛型参数的具体类型，也就无法区分 `T0<A>` 和 `T0<B>`，因为它们在运行时看起来是一样的（都是 `T0<object>` 了）。强行要实现 ` T0<A>` 和 `T0<B>` 就会导致类型安全问题和冲突。
2. 在 C# 里，C# 的 CLR（Common Language Runtime）平台不使用类型擦除，而是保留泛型类型信息到运行时。而且 C# 允许多次继承同一个泛型接口，也允许让一个类同时实现多个同样的泛型接口。而且它们可以有不同的泛型类型参数。在这里，类 `D` 就已经实现了接口 `I<T>` 两次，泛型类型参数一个是 `A`，一个是 `B`。再加上 C# 本身支持显式接口实现，类 `D` 里的 `foo ()` 便是通过显示接口实现的方式避开了冲突。

也就是说：都怪 JVM！（并没有）

---
## 扩展：显式接口实现

类 D 里，`B I<B>.foo()` 还有一种能过编译的写法：写成 `public B foo()`。不过编译器会告诉你“它隐藏了 `C.foo()`”

![编译器提示隐藏了C.foo()](Pastedimage20240718160138.png)

但如果你用 `B I<B>.foo()` 的话就没有这个提示。

![编译器没有提示](Pastedimage20240718160549.png)

 `B I<B>.foo()` 其实就是 C# 的显式接口实现。它不会在类中引入新的成员，所以不会影响，也不会隐藏类的已有成员。
 但有一点要注意：虽然它实现了接口 `I<B>` 的 `foo` 方法，但它只能通过 `I<B>` 接口来调用，而不能通过类的实例直接调用。还是上面的例子，如果写一个
 ```cs
D d = new D();
d.foo();
```
 你会看到它调用的是类 `C` 里定义的 `foo()` ，而不是类 `D` 里定义的。

![调用的是类 C 里的 foo()](Pastedimage20240718170711.png)

但如果是
 ```cs
I<B> i = d;
i.foo();
```
 
 利用一下 C# 多态，即「对象可以用它所实现的接口类型来引用」的性质，把实例 `d` 赋值给接口类型 `I<B>` 的变量 `i`。这时候调用的就是类 `D` 里定义的 `foo()` 了。
 
 ![调用的是类 D 里的 foo()](Pastedimage20240718171304.png)
 
 `public B foo()` 就是最为人熟知的公共方法实现。这种方法创建的方法就属于类的公共成员，可以直接通过类的实例调用。不过，如果基类 `C` 中有一个同名的 `foo` 方法，定义同名的新方法就会隐藏基类的方法，除非显式使用 `new` 关键字。（应该都知道的）

两者还是有很大不同的。一个是类成员一个不是；一个从类实例访问一个从接口访问；一个会隐藏类成员一个不会。总之就是按需选择。

这俩能在同一个类里同时出现，就已经说明它俩不是同一个方法了。

![同时出现的 foo()](Pastedimage20240718172406.png)

## D.N.Code 里聊 high 了的部分

>残像：C# 没有任何问题，我甚至不用加 `out`
>（不过 Resharper 会说“The type parameter 'T' could be declared as covariant”），还是会推荐加

> 残像：不如赶紧让 kotlin 学一套 C++的模板，直接全 reified
> 残像：但是 kotlin 和 java 的 anonymous object 能解决很多问题，这是 C# 没有的
> 盐酸：全都 reified
> 盐酸：直接获得不如 C++的效率以及比 C++还烂的编译时长
> 残像：直接让盐酸爆改编译器，出门新语言叫 Silicon
> Trarizon：等会匿名对象是哪个东西
> 残像：
> ```kotlin
> Fun buildUI() = flowLayout {
> 
class TestScreen (parent: Screen) :
    DslScreen<FlowLayout>(
        parent,
        object : OwoBuilder<FlowLayout> {
            val builder = buildUI()
            override fun build() = builder.build()
            override val canBuild get() = builder.canBuild
        }
    )
>```
>Trarizon：哦这个，确实 c #比较麻烦 ，得自己写个类型挺麻烦的
>残像：不，C# 直接把接口放到类型上就没事了，也就不需要了
>盐酸：还是有用的，可以看成是 lambda 升级版
>Trarizon：c# 可以直接数个 lambda 直接构造 interface 就差不多，但是 java 这个语法实际上大部分时候我都嫌占用面积太大（