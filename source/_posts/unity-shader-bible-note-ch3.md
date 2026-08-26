---
title: Unity着色器圣经（Unity Shader Bible）笔记（3）
date: 2025/10/21
updated: 2025/10/23
category:
  - GameDev
  - Unity3D
tag:
  - Unity3D
  - 计算机图形学
  - GameDev
mathjax: false
---

[【翻译】Unity Shader Bible/Unity着色器圣经 全书目录](https://zhuanlan.zhihu.com/p/645676077)第三章的笔记。

<!-- more -->

### 3.0.1 | 顶点/片元着色器的结构

从一个最简单的 Unlit Shader 开始。

创建一个名为“USB_simple_color”的无光照着色器（Unlit Shader）。着色器创建完后，为方便编译 Unity 会自动添加一些GPU可以理解的默认代码。现在，打开 USB_simple_color 着色器，内部的代码应如下所示：

```shader
Shader "Unlit/USB_simple_color"  
{  
    Properties  
    {  
        _MainTex ("Texture", 2D) = "white" {}  
    }  
    SubShader  
    {  
        Tags { "RenderType"="Opaque" }  
        LOD 100  
  
        Pass  
        {  
            CGPROGRAM  
            #pragma vertex vert  
            #pragma fragment frag  
            // make fog work  
            #pragma multi_compile_fog  
  
            #include "UnityCG.cginc"  
  
            struct appdata  
            {  
                float4 vertex : POSITION;  
                float2 uv : TEXCOORD0;  
            };  
  
            struct v2f  
            {  
                float2 uv : TEXCOORD0;  
                UNITY_FOG_COORDS(1)  
                float4 vertex : SV_POSITION;  
            };  
  
            sampler2D _MainTex;  
            float4 _MainTex_ST;  
  
            v2f vert (appdata v)  
            {  
                v2f o;  
                o.vertex = UnityObjectToClipPos(v.vertex);  
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);  
                UNITY_TRANSFER_FOG(o,o.vertex);  
                return o;  
            }  
  
            fixed4 frag (v2f i) : SV_Target  
            {  
                // sample the texture  
                fixed4 col = tex2D(_MainTex, i.uv);  
                // apply fog  
                UNITY_APPLY_FOG(i.fogCoord, col);  
                return col;  
            }  
            ENDCG  
        }  
    }  
}
```

着色器的整体格式：
![](Pasted%20image%2020251031142425.png)

第一行所代表的是着色器在检查器中的路径（InspectorPath）与着色器的名字（shaderName）。接着是属性（Properties）语义块，包括了纹理、向量、颜色等多种不同类型的属性。再下一个语义块是子着色器（SubShader），最后是可选的回退（Fallback）。

“inspectorPath”指的是我们将要选择着色器并将其应用到材质的位置。此选择操作通过Unity的Inspector面板完成。

我们没有办法直接将着色器应用在模型上，而是需要通过创建材质来完成。刚才创建的无光照着色器“USB_simple_color”的默认检查其路径为“Unlit”，这意味着创建材质后，我们需要进入材质的检查器，搜索 Unlit 路径、应用“USB_simple_color”着色器到材质上，接着我们就可以将材质应用到模型上了。

在结构上，GPU 将从上到下线性地读取着色器程序，也就是说如果我们创建了一个自定义函数，但是错误地把它编写在要使用它的代码下方，GPU 将无法读取这个函数，从而造成错误。然而即使错误发生，回退也将分配一个不同的着色器，以便图形硬件可以继续编译着色器。

比如说这个代码是能过编译的。它先声明了函数定义然后调用：

```shader
// 1 . 声明自定义函数
float4 ourFunction() 
{ 
    // your code here … 
} 

// 2. 在这里面使用自定义函数 
fixed4 frag (v2f i) : SV_Target 
{ 
    // 在这里使用
    float4 f = ourFunction(); 
    return f; 
}
```

但这个代码就会报错，因为在调用前找不到函数的定义：

```text
// 2. 使用自定义函数
fixed4 frag (v2f i) : SV_Target 
{ 
    // we are using the function here 
    float4 f = ourFunction(); 
    return f; 
} 

// 1 . 声明自定义函数
float4 ourFunction() 
{ 
    // your code here … 
}
```

## 3.0.2 | ShaderLab着色器

大多数着色器都以“**Shader**”的声明开头，紧随其后的是它在 Unity 检查器中的路径与名字，例如：“shader inspector path/shader name”。

在 ShaderLab 声明式语言中，诸如子着色器（SubShader）、回退（Fallback）等语义块都是写在“Shader”声明的大括号里的。

```shader
Shader "InspectorPath/shaderName" 
{ 
    // ShaderLab代码写在这里面
}
```

先前创建的着色器“USB_simple_color”的路径与名字是“Unlit/USB_simple_color”，因此如果我们要将它赋给某个材质，就需要进入 Unity 检查器，查找“Unlit”路径，然后选择“USB_simple_color”。

着色器的路径与名字都可以根据需求进行更改：

```shader
// 默认情况
Shader "Unlit / USB_simple_color" 
{ 
    // ShaderLab代码写在这里面
} 

// 自定义路径 USB (Unity Shader Bible) 
Shader "USB / USB_simple_color" 
{ 
    // ShaderLab代码写在这里面
}
```

## 3.0.3 | ShaderLab的属性 (Property)

属性与一系列可以在 Unity 检查里修改的变量相对应，一共有八种有用的类型。我们使用这些属性来创建或修改着色器，无论是在动态层面还是在运行时。声明属性的语法如下所示：

```text
PropertyName ("display name", type) = defaultValue. 
```

- **属性名（"PropertyName"）** 指属性的名称（例如_MainTex）
- 展示名称（"display name"）对应的是属性们在 Unity 检查器中显示的名称（例如Texture）
- **类型（"type"）** 代表属性的类型（例如Color、Vector、2D等）
- **默认值（"defaultValue"）** 指的是该变量的默认值（例如我们可以给一个颜色属性设置白色(1,1,1,1)为默认值）。

![](Pasted%20image%2020251103181412.png)

注意 shader 代码行的末尾**不需要分号**。
之前创建的 shader 里，property 就是
```shader
Properties  
{  
    _MainTex ("Texture", 2D) = "white" {}  
}
```

## 3.0.4 | 数字与滑条类型

数字（number）与滑条（slider）两种类型的属性允许我们在着色器中添加并控制数值类型的变量。

以声名名为“\_Specular”的范围类型属性、名为“\_Factor”的浮点数类型属性与名为“\_Cid”的整型类型属性为例，声明数字与滑条类型的语法如下所示：

```text
// name ("display name", Range(min, max)) = defaultValue 
// name ("display name", Float) = defaultValue 
// name ("display name", Int) = defaultValue 
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        _Specular ("Specular", Range(0.0, 1.1)) = 0.3 
        _Factor ("Color Factor", Float) = 0.3 
        _Cid ("Color id", Int) = 2 
    } 
}
```

## 3.0.5 | 颜色与向量类型

这两种类型的属性允许我们在着色器中定义颜色（color）与向量（vector）。

以声名名为“\_Color”的颜色类型与名为“\_VPos”的向量类型为例，声明颜色与向量类型的语法如下所示：

```shader
// name ("display name", Color) = (R, G, B, A) 
// name ("display name", Vector) = (0, 0, 0, 1) 
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        _Color ("Tint", Color) = (1, 1, 1, 1) 
        _VPos ("Vertex Position", Vector) = (0, 0, 0, 1) 
    } 
}
```

## 3.0.6 | 纹理类型

这种类型的属性允许我们在着色器中使用纹理（texture）。

Texture 大致有三种：2D、Cube 和 3D。

关于 2D 属性：
如果想要在 3D 模型（例如角色模型）上使用纹理，有两步：
1. 为纹理创建一个 2D 属性；
2. 通过有两个输入（纹理与UV坐标）的“tex2D”函数将纹理映射到模型上。

关于 Cube 属性：
一个经常在游戏中用到的属性，就是立方体（Cube，意思是 Cubemap），这种纹理对生成反射贴图（reflection maps）来说非常有用。反射可以被应用到诸多场合，例如角色的盔甲、金属物体的表面等等。

关于 3D 属性：
除开上述两种，其他能找到的纹理类型均为 3D 类的。它们相较于上面两种使用的场景更少，因为它们是立体的，有额外的坐标轴和空间计算。

以声名名为“\_defaultColorTexture”的 2D 属性、名为“\_defaultColorTexture”的 Cube 属性与名为“\_defaultColorTexture”的 3D 属性为例，声明纹理的语法如下所示：

```text
// name ("display name", 2D) = "defaultColorTexture" 
// name ("display name", Cube) = "defaultColorTexture" 
// name ("display name", 3D) = "defaultColorTexture" 
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        _MainTex ("Texture", 2D) = "white" {} 
        _Reflection ("Reflection", Cube) = "black" {} 
        _3DTexture ("3D Texture", 3D) = "white" {} 
    } 
}
```

注意，当我们声明一个变量的时候，声明用的语言是 ShaderLab，但着色器程序是用 Cg 或 HLSL 写的。因为 Cg/HLSL 和 ShaderLab 是不同的语言，所以我们需要创建“**连接变量**”。

这些变量使用“uniform”进行全局声明，然而这一步可以跳过，因为程序知道它们是全局变量。所以如果我们想要在一个“.shader”文件中添加一个变量：
1. 在Shader Lab中声明它
2. 在Cg或HLSL中使用同名的全局变量
3. 在程序中使用它。

属性和连接变量的名字必须一致，这样代码才能正确调用。

```text
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        // 声明属性
        _MainTex ("Texture", 2D) = "white" {} 
        _Color ("Color", Color) = (1, 1, 1, 1) 
    } 
    SubShader 
    { 
        Pass 
        { 
            CGPROGRAM 
            … 
            // 在这里添加连接变量，注意名字与属性完全一致
            sampler2D _MainTex; 
            float4 _Color; 
            … 
            half4 frag (v2f i) : SV_Target 
            { 
                // use the variables 
                half4 col = tex2D(_MainTex, i.uv); 
                return col * _Color; 
            } 
            ENDCG 
        } 
    } 
}
```

## 3.0.7 | 自定义材质属性绘制器（Material Property Drawer）

在 ShaderLab 中还有一种叫做“绘制器（drawer）”的属性类型，这个类允许我们在 Unity 检查器中创建自定义属性，从而方便用户在着色器中进行条件编程。

通常情况下绘制器不会出现在着色器中，除非我们声明了它们（一般是出于个人需要）。目前一共有七种不同的绘制器：

- 开关（Toggle）
- 枚举（Enum）
- 关键词枚举（KeywordEnum）
- 指数滑条（PowerSlider）
- 整数范围（IntRange）
- 空间（Space）
- 标题（Header）

每一种绘制器都有特定的功能，需要单独声明。

我们可以在运行时不更换材质的前提下，通过为着色器选择不同的状态来得到不同的效果。我们通常将这些绘制器与两种着色器变体一起使用（ `#pragma multi_compile` 与 `#pragma shader_feature`）

![](Pasted%20image%2020251103190829.png)

## 3.0.8 | MPD Toggle

在 ShaderLab 中我们无法使用 bool 类型的属性，取而代之的是可以实现相同功能的开关（Toggle）。这种绘制器允许我们切换着色器的状态，这样我们就可以通过状态条件实现不同的效果。

要声明一个开关，我们需要先在一对中括号里添加 Toggle 字样，接着再声明它。虽然在声明时我们需要将属性类型设为浮点数，但开关的默认值必须是0或1的整数，其中 0 表示 "关闭"，1 表示 "打开"。

以声明一个默认状态为关闭（0）的开关为例，声明的语法如下所示：

```text
[Toggle] _PropertyName ("Display Name", Float) = 0
```

需要注意的一点是，要在代码中使用开关，我们需要写上 `#pragma shader_feature`。这是一种着色器变体，它的作用是根据不同的状态（开或关）产生不同的条件。

```hlsl
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    {
        _Color ("Color", Color) = (1, 1, 1, 1) 
        // declare drawer Toggle 
        [Toggle] _Enable ("Enable ?", Float) = 0 
    }
    SubShader 
    { 
        Pass 
        { 
            CGPROGRAM 
            … 
            // declare pragma 
            #pragma shader_feature _ENABLE_ON 
            … 
            float4 _Color; 
            … 
            half4 frag (v2f i) : SV_Target { 
                half4 col = tex2D(_MainTex, i.uv); 

                // generate conditions 
            #if _ENABLE_ON 
                return col; 
            #else 
                return col * _Color; 
            #endif 
            } 
            ENDCG 
        } 
    } 
}
```

在这个例子中，我们声明了一个叫做“**`_Enable`**”的开关属性，并在 `CGPROGRAM` 语义块中写上了  `shader_feature`。但和我们之前接触到的属性不同的是，**这个开关被声明成了“`_ENABLE_ON`”**。在 `shader_feature` 中添加的变体是**“常量”，它们都用大写字母书写**，后缀“**`_ON`**”表示开关的开启状态。在上述例子中，如果 `_Enable` 属性处于开启状态，我们将在片元着色器中返回默认纹理颜色；如果 `_Enable` 属性处于关闭状态，我们将 `_Color` 属性乘以它本身。

注意，`shader_feature` 并不能在一个项目中编译多个变体，这代表 Unity 不会在项目最终构建时加入我们未使用过的变体，我们无法在运行时从一种状态切换到另一种状态。为此，我们必须使用带有“**`multi_compile`**”变体的关键词枚举绘制器（下一节会讲）。

## 3.0.9 | MPD KeywordEnum（关键词枚举）

关键词枚举会在材质检查器中生成一个弹出式样式菜单。与开关不同的是，这种绘制器允许我们为着色器配置多达 9 种不同的状态。
在声明关键词枚举时，需要在一对中括号中添加 `KeywordEnum`，然后列出要使用的状态集。

```text
[KeywordEnum(StateOff, State01, etc...)] 
_PropertyName ("Display name", Float) = 0
```

上例列出了关键词枚举的状态，其中第一种状态对应默认状态（StateOff）。接着我们继续进行属性声明、在材质检查器中显示名称、数据类型，最后使用默认值进行初始化。

在代码中声明绘制器不仅可以使用 **shader_feature** 变体，还可以使用 **multi_compile** 变体，具体选择哪一个取决于我们希望在最终导出的项目中包含的变体数量。

上一节提过  `shader_feature` 只会导出材质检查器中选中的变体，然而，**`multi_compile` 可以导出着色器中所有能找到的变体（不管它们有没有被使用过）**。鉴于这个特性，`multi_compile` 是导出或编译那些运行过程中可能存在多种状态的最佳选择（例如超级马里奥中无敌星的状态）。

下例声明了一个叫做“`**_Options**`”的具有三种状态（关闭/红/蓝）的关键词枚举属性。接着，在 CGPROGRAM 语义块下的 multi_compile 变体中声明几种状态的常量。最后通过这些状态定义着色器主纹理的三种不同颜色的输出。

```text
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        // 声明绘制器 
        [KeywordEnum(Off, Red, Blue)] 
        _Options ("Color Options", Float) = 0 
    } 
    SubShader 
    { 
        Pass 
        { 
            CGPROGRAM 
            … 
            // 声明变体与状态
            #pragma multi_compile _OPTIONS_OFF _OPTIONS_RED _OPTIONS_BLUE 
            … 
            half4 frag (v2f i) : SV_Target 
            { 
                half4 col = tex2D(_MainTex, i.uv); 

            // 不同状态
            #if _OPTIONS_OFF 
                return col; 
            #elif _OPTIONS_RED 
                return col * float4(1, 0, 0, 1); 
            #elif _OPTIONS_BLUE 
                return col * float4(0, 0, 1, 1); 
            #endif 
            } 
            ENDCG 
        } 
    } 
}
```

## 3.1.0 | MPD Enum（枚举）

枚举（Enum）跟关键词枚举（KeywordEnum）很像，不同之处在于枚举可以定义一个“值/id”作为参数，并传递到着色器命令中使用，从而在 Inspector 中动态更改它的功能。

声明语法如下所示：

```text
[Enum(valor, id_00, valor, id_01, etc … )] 
_PropertyName ("Display Name", Float) = 0
```

枚举并不依赖着色器变体，它通过命令或函数进行声明。

下例声明了一个叫做“**`_Face`**”的**枚举**属性，并定义了三个参数：关闭/0、前/1、后/2，接着将参数应用到子着色器中的剔除（Cull）里，这样就可以在材质的检查器中更改剔除方向。剔除留到后续的3.2.1小节中再详细讲。

```text
Shader "InspectorPath/shaderName" 
{ 
    Properties 
    { 
        // declare drawer 
        [Enum(Off, 0, Front, 1, Back, 2)] 
        _Face ("Face Culling", Float) = 0
    } 
    SubShader 
    { 
        // we use the property as a command 
        Cull [_Face] 
        Pass { … } 
    } 
}
```

## 3.1.1 | MPD指数滑条与整数范围

这两种绘制器在处理数字范围和精度时非常有用。

指数滑条（PowerSlider）的声明语法如下所示：

```text
[PowerSlider(3.0)] _PropertyName ("Display name", Range (0.01, 1)) = 0.08
```

整数范围（IntRange）可以给Int值添加数值的范围。它的声明语法如下所示：

```text
[IntRange] _PropertyName ("Display name", Range (0, 255)) = 100
```

注意，如果我们想在着色器中使用这两种绘制器，则必须像声明常规属性一样在 CGPROGRAM 语义块中声明它们的连接变量。下例声明了一个叫做“**`_Brightness`**”的指数滑条和一个叫做“**`_Samples`**”的整数范围，接着我们在 CGPROGRAM 语义块中用相同的名字声明了它们的连接变量。：