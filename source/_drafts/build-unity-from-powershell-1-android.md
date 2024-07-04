---
title: 通过 PowerShell 构建 Unity 项目其一：Android 篇
draft: true
date: 2023-12-25 10:48:23
tags:
- Unity
- cli
- PowerShell
---

讲一下如何通过命令行工具（我选了 PowerShell）打 Android 的包。

<!-- more -->

## 用命令行打包的理由

为什么要使用命令行打包？当然是为了做自动化！自动化有下列好处：

- 打包的流程十分枯燥、单一，每次都是那几个指定动作，而且还特别耗时（每次都是半小时起步）。自动化打包可以将程序和策划从这流程中解放出来。
- 打包的步骤繁杂，稍有不慎就会漏掉几个关键步骤（我之前甚至写了个 checklist）.
- 可以针对测试包和正式包做不同操作
- 方便后续做 CI/CD。

## 选择 PowerShell 的理由

打包用的机器是 Mac。Mac 的 cli 是 Terminal，基于 UNIX 的，所以怎么看似乎都是用 shell 好。那为什么要用 PowerShell 呢？
- Powershell 可以在 Windows、Linux、Mac 等终端上运行。在