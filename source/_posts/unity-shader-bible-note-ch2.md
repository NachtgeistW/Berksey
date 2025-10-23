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