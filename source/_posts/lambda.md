---
title: 盐酸的C++基础知识小讲堂——lambda表达式入门
date: 2019/12/22
updated: 2019/12/22
category: 
- Computer Science
- CPP
tag: 
- CPP
- lambda表达式
- Python
---
真实基础知识，真实讲堂。

以下笔记摘自 D.N.Code 群聊天记录。

如有错误请指出。

<!-- more -->

看维基百科的 Perm 算法的实现时发现了一些不能理解的东西。

```C++
explicit perm(const int l = 0, function<void(vector<int>&)> fun = [](vector<int>&) {}) : 
len(l), used(l, -1), position(l), action(std::move(fun)) {}

...

perm p(len, [&](vector<int>& vec)
  {
   for (int i = 0; i < len; i++)
    cout << vec[i] << " ";
   cout << endl;
  });
```

这一堆东西是什么？？？

> 建议去看一下lambda表达式（  
——盐酸

之后 jy 提问“`[&](vector<int>& vec)` 是什么，为什么要用，要怎么用”。由此引出盐酸的 C++ 基础小知识讲座之 lambda 表达式。

## lambda 表达式

### 什么是 lambda 表达式

lambda 表达式是 C++11 起加入的新功能。它构造[闭包](https://zh.wikipedia.org/wiki/%E9%97%AD%E5%8C%85_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6))，能够捕获作用域中的变量的无名函数对象。换句话说，可以将 lambda 表达式理解成**一个没有名字的内联函数**。

一个完整的 lambda 表达式长这样：

```C++
[ 捕获 ] <模板形参>(可选)(C++20) ( 形参 ) 说明符(可选) 异常说明 attr -> return requires(可选)(C++20) { 函数体 }
```

还有以下写法：

```C++
[ 捕获 ] ( 形参 ) -> return { 函数体 }
[ 捕获 ] ( 形参 ) { 函数体 }
[ 捕获 ] { 函数体 }
```

不管忽略什么，`[]` 和函数体都永远不可少。其中， `[]` 是用来捕获不属于 lambda 表达式的变量的，即外部变量。如果留空，就代表 lambda 表达式不捕获外部变量。可以在 `[]` 里加变量名、 `&` 或者 `=`，这样就能捕获外部变量了。

假设有一个外部变量名为 `a`。`[a]` 意为使用**值捕获**的方式捕获它，`[&a]` 意为使用**引用捕获**的方式捕获它。跟函数的值传递和引用传递一样。

加 `&` 或者 `=` 的两种做法叫做隐式捕获，由编译器自己去推断要捕获的变量。`[&]` 的意思是，如果编译器发现 lambda 函数体里面有编译器不认识的东西，那编译器就到外面那层去找有没有叫这个名字的，如果有就**引用捕获**它。`[=]` 则是把引用捕获改成**值捕获**。

如，

```C++
std::vector<int> c = {1, 2, 3, 4, 5, 6, 7};
std::for_each(c.begin(), c.end(), [](int i){ std::cout << i << ' '; });
//1 2 3 4 5 6 7 
```

这就定义了一个匿名函数，参数是 `int i`，作用是输出 `i`+空格。这个 lambda 表达式的 `[]` 是空的，说明它不捕获外部变量。

而

```C++
void print_plus_x(const std::vector<int>& v, const int x)
{
   std::for_each(v.begin(), v.end(),
        [x](const int i){ std::cout << i + x << ' '; });
}
//2 3 4 5 6 7 8 
```

则显式捕获了外部变量 `x`。

```C++
void print_plus_x(const std::vector<int>& v, const int x)
{
   std::for_each(v.begin(), v.end(),
        [&](const int i){ std::cout << i + x << ' '; });
}
//2 3 4 5 6 7 8 
```

把 `[x]` 改成 `[&]` 之后运行，结果与写成 `[x]` 一致。这就是引用隐式捕获。

另外需要注意的是，当混合使用显式捕获和隐式捕获的时候，捕获列表的第一个元素必须是一个 `&` 或者 `=`。

### 捕获 (capture) 是什么？x 和 i 的区别在哪

> capture 的意思就是把这个函数体外面的东西捕获进来使用。  
> ——盐酸

x 和 i 的区别在于，i 是 lambda 表达式自己的参数；而 x 不属于 lambda 表达式，它是外部变量，是 lambda 表达式所在函数定义的局部变量。

### 为什么要用 lambda 表达式

> 所以，你懒得对这个“事情”定义一个专门的函数，然后就 inline 定义一个函数。  
> ——jy

> 比如说你懒得定义一个函数名的时候。因为这玩意就用一遍，也很简单，所以一个 lambda 比较简洁。  
> ——夜轮

> 比如你想搞一个函数，输入一个 vector 和一个 x，输出 vector 里每个元素 + x，那你这里 for_each 的第三个参数就是那玩意。因为 x 不是参数，i 才是那个参数，for_each 想要的函数是接受一个参数的，他只往函数里面传那个 i 进去，但是你又想在函数体里面用到 x，那就把 x 抓进函数体里面。  
>——盐酸

> 对于那种只在一两个地方使用的简单操作，lambda 表达式是最有用的。如果要在很多地方使用相同的操作，或者一个操作需要很多语句才能完成，通常建议使用函数。  
> ——《C++ Primer Plus》

### 像这种函数能定义返回值吗

可以的。~~甚至可以加个模板（~~。只要在函数体里写上 `return` 就有了。如果需要定义 lambda 表达式的返回值的话，还得把 `-> return` 这个地方写好。这叫做尾置返回类型，是 C++11 里和 lambda 表达式里一起引入的新写法。普通函数也可以用，但 lambda 表达式要定义返回类型的话，必须用尾置返回类型。还是举例说明吧。

```C++
std::for_each(c.begin(), c.end(), [](int i){ std::cout << i << ' '; });
```

这里不用写返回值，是因为编译器看 lambda 表达式没 return，推断出来返回值类型是 `void`。如果改成这样：

```C++
std::for_each(c.begin(), c.end(), [](int i){
    std::cout << i << ' ';
    return i;
    });
```

`return i`，编译器就能看出来返回值类型是 `int`，因为 `i` 是个 `int`，lambda 表达式就会返回整数 `i`。另外，写了 `->return type` 但是不写 `return` 语句的话，编译器会不给过编译。

---

## 对 Perm 函数的完整解释

请移步至[Perm 算法](https://nachtgeistw.github.io/Berksey/algorithm/2019/12/24/prem-algorithm/)。

---

## 延伸

### 盐酸讲 high 了的延伸部分

> C++ 的 lambda 表达式跟其他很多语言不同之处在于 C++ 的 lambda 是 0 overhead 的（（（就是无额外开销
>
> 大部分语言的lambda都是类似这个 `&`（并且自带 type erasure，这里就有 runtime overhead 了）。为什么会有 overhead 呢，type erasure 必定带来 overhead，因为 type erasure 的目的是把所有返回值是 `Res` 而参数是 `Arg...` 的东西都擦除成同一个类型。这就必定需要到堆上申请空间，因为捕获的变量不同所需要的空间也就不同，然而同一个类型在栈上的空间是相等的。  
>
> py 那个 lambda 严重缩水，那个叫 list comprehension（  
> py那个函数定义本身就是个 closure
>
> ```Python
> def plus(x, y):
>    def plus_x(v):
>        return v + x
>    return plus_x(y)
> ```
>
> 比较心把但是应该懂我意思，就是 py 的函数自动捕获外面的变量（
>
> py 所有函数都捕获外部变量，C++ 那个例子如果你中括号里没有 x 的话是会编译错误的（（（（  
> 他就是原地定义了一个匿名的类，那个 lambda 是这个类的一个对象

注：

- type erasure：类型擦除，指在编译期明确去掉所编程序（某部分）的类型系统
- runtime overhead：运行时开销
- list comprehension：递推式构造列表

### 锦鳞的一个问题

> 我记得 lambda 表达式不完全等于匿名函数，那区别在哪（（

[C++基础知识小讲堂(3)——函数对象与Lambda表达式](https://chlorie.github.io/ChloroBlog/posts/2019-12-23/0-cpp-basics-3.html)

盐酸学长已经在这篇 blog 里说了。请移步阅读。

### 对《C++ Primer Plus》的一个疑问

《C++ Primer Plus》里举了个强制指定 lambda 表达式返回值的例子：

> ```C++
> transform(vec.begin(), vec.end(), vec.begin(), [](int i)
>    {
>       if (i < 0) return -1;
>       return 1;
>    });
>```
>
> 编译器推断这个版本的 lambda 表达式返回类型为 `void`，但它返回了一个 `int` 类型的值。应该在 `(int i)` 后面加上 `-> int`。

在 Visual Studio 里试了一下不加的情况。程序完全能正常跑，IDE 识别出来了返回类型为 `int`。迷惑。
