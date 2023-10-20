---
title: Godot 学习笔记
date: 2023-10-17
tags:
---

默认语言为 C#，引擎是 Godot 4。

## 为变量添加了 `[Export]` 但检查器里找不到它

`[Export]` 相当于 Unity 里的 `[SerializeField]`。但是这东西在脚本里加了之后，得手动 Build Project 一次，变量才会出现在检查器里。如果为变量添加了 `[Export]` 但它没出现，先试试重新 build 一下项目吧。