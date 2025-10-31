---
title: Unity 着色器圣经（Unity Shader Bible）笔记（2）
date: 2025/10/23
updated: 2025/10/23
category:
  - GameDev
  - Unity3D
tag:
  - Unity3D
  - 计算机图形学
  - GameDev
---

[【翻译】Unity Shader Bible/Unity着色器圣经 全书目录](https://zhuanlan.zhihu.com/p/645676077)第二章的笔记。

<!-- more -->

## 2.0.1 | 什么是着色器(shader)？

着色器是一种带有“.shader”后缀（如 color.shader）的**程序**，经过编译它们可以在项目中实现有趣的图像效果。着色器的内部包含了一系列的数学计算与指令，可以对屏幕上模型覆盖区域内的每个像素的颜色进行处理。

着色器允许我们根据多边形物体的属性通过坐标系统绘制元素。这个程序在GPU上执行，因为GPU 是由数千个小型、高效的内核所组成的，非常适合解决并行任务，而 CPU 则是专门为处理串行任务而设计的。

Unity 有三种与着色器有关的文件类型。第一种是扩展名直接为“.shader”的文件，该类型的程序能够在不同类型的渲染管线中编译。

第二种是扩展名为".shadergraph "的文件，该类型的程序只能在通用渲染管线（URP）或高清渲染管线（HDRP）中编译。除此之外，还有一些扩展名为".hlsl "的文件，里面包含了我们自定义的着色器函数。这些函数通常以 "自定义函数 "节点的形式出现在shader graph中。

还有一种扩展名为".cginc "的程序。（现在只需要知道）：
- “.cginc ”与“.shader ”程序中的 CGPROGRAM 关联
- “.hlsl ”与“.shadergraph ”程序中的 HLSLPROGRAM 关联

![Fig. 2.0.1a. Unity中不同shader的图标](Pasted%20image%2020251023132018.png)

## 2.0.2 | 编程语言介绍

Unity 中有三种与 shader 相关的编程语言：
- HLSL（高级着色器语言 - Microsoft）
- Cg（用于图形的C - NVIDIA）
- ShaderLab（声明式语言 - Unity）

由 Cg 编写的着色器仍可被编译，但在最新版本的Unity中已不再使用。

Cg 是一种被设计用于在大多数 GPU 上编译的高级编程语言，由 NVIDIA 与微软合作开发，语法与 HLSL 非常相似。之所以可以使用 Cg 语言编写着色器，是因为它可以被编译成 HLSL 或 GLSL（OpenGL 着色器语言），这样可以加快并优化为游戏创建材质的过程。

当我们创建着色器时，我们的代码编写在一个名为 CGPROGRAM 的字段中。Unity 目前正在努力提升 Cg 和 HLSL 的兼容性，因为 HLSL 是 2019 版后的 Unity 版本所使用的官方着色器编程语言， CGPROGRAM 和 ENDCG 很可能很快就会被 HLSLPROGRAM 和 ENDHLSL 所取代。

Unity 中除 shader graph 和计算着色器外的所有着色器都是用一种名为 ShaderLab 的声明式语言编写的。这种语言的语法允许我们在 Unity 检查器中查看着色器内的属性。而且可以实时修改这些属性的值，调整着色器以获得理想的效果。

在 ShaderLab 中，我们可以人为定义多个属性和命令，其中包括**回退（Fallback）** 代码块，用来在着色器编译产生错误时返回一个不同的着色器，从而使图形硬件继续工作。

**子着色器（SubShader）** 是 ShaderLab 中的另一种代码块，它允许我们声明命令并生成pass。使用 Cg/HLSL 编写的着色器可以包含多个 SubShader 或 pass ；但在可编程渲染管线（SRP）的环境下，每个着色器只能包含一个 pass 。

## 2.0.3 | 着色器的种类

练习的版本要求为 2019 往上，模板为 3D Built-in Rrender Pineline。

着色器的类型不止一种，包括：

- 标准表面着色器（Standard Surface Shader）
- 无光照着色器（Unlit Shader）
- 屏幕特效着色器（Image Effect Shader）
- 计算着色器（Compute Shader）
- 光线追踪着色器（Ray Tracing Shader）

![Fig. 2.0.3a. 资产 / 创建 / 着色器 路径](Pasted%20image%2020251030181930.png)

可用的着色器可能会根据创建项目时使用的 Unity 版本而有所不同。另一个可能影响列表中着色器数量的是 Shader Graph。除此之外，如果项目使用了通用渲染管线（Universal RP）或高清渲染管线（High Definition RP），可能包含 Shader Graph 包，也会增加可创建着色器的数量。

## 2.0.4 | 标准表面着色器

标准表面着色器的特点是代码完善适合编写，可与基本光照模型交互，并且仅适用于**内置渲染管线（Built-in RP）**。如果我们想创建一个与光线交互的着色器，我们有两种选择：
1. 使用 “无光照着色器”并添加数学函数，以便在材质上进行光照渲染。
2. 使用 “标准表面着色器”，自带基本的光照模型，在某些情况下包括反照率、镜面反射和漫反射。

## 2.0.5 | 无光照着色器

“光照”一词指的是物体的材质会被光照的影响，“无光照”则正好相反。无光照着色器（Unlit Shader）指的是三原色模型，通常是我们用来制作效果的基础结构。这类着色器非常适合低端硬件，代码中没有任何优化，因此我们可以看到它的完整结构，并根据需要进行修改。无光照着色器的主要特点是在内置渲染管线（Built-in RP）和可编程渲染管线（SRP）中都可以运行。