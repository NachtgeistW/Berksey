---
title: Godot
date: 2023-10-27 22:26:42
description: the best open-source game engine
---

收集一些关于 Godot 的资料与笔记。

默认语言为 C#，引擎是 Godot 4。

## Shader

{% img /notes/godot/godot-shader-cheat-sheet.png 500 godot-shader-cheat-sheet %}

## Physics Cheat Sheet

[Godot 3D Vector/Physics Cheat-Sheet: - Godot Forums](https://godotforums.org/d/18480-godot-3d-vector-physics-cheat-sheet)

[Sizes in 3D Enviroment (and relations to real-world sizes) - Godot Engine - Q&A](https://ask.godotengine.org/46858/sizes-in-3d-enviroment-and-relations-to-real-world-sizes)

## 为变量添加了 `[Export]` 但检查器里找不到它

`[Export]` 相当于 Unity 里的 `[SerializeField]`。但是这东西在脚本里加了之后，得手动 Build Project 一次，变量才会出现在检查器里。如果为变量添加了 `[Export]` 但它没出现，先试试重新 build 一下项目吧。

## 鼠标左键的点击事件会判断两次

只做 `InputEvent` 是不是 `InputEventMouseEvent` 的判断的话，执行 _Input 会有两条 Log。

```csharp
if (@event is InputEventMouseButton eventMouseButton)
{
    GD.Print("Mouse Click/Unclick at: ", eventMouseButton.Position);
}

/*
 * Log 输出：
 * Mouse Click/Unclick at: (459, 474)
 * Mouse Click/Unclick at: (459, 474)
 */
```

解决方法是再加一个 `IsPressed()` 的判定。

```csharp
if (@event is InputEventMouseButton eventMouseButton && eventMouseButton.IsPressed())
{
    GD.Print("Mouse Click/Unclick at: ", eventMouseButton.Position);
}
```

