---
title: GitHub CLI 用法解析与研究
category: 
- GitHub
tag: 
- GitHub
---

将 GitHub 带到控制台里。从此省去等待 GitHub UI 的加载时间。

<!-- more -->

前几天得知了 GitHub CLI 1.0 正式发布的消息，然后才知道 GitHub 官方开发这个也有一段时间了。简单看了一下那篇文章，感觉能省去非常多的打开与等待网页加载的时间，于是火速下载试用。~~之前拿给盐酸他们看的时候一个两个都说是时代的倒退，结果下载用上之后全都真香了。人类啊（~~

官网地址：[GitHub CLI | Take GitHub to the command line](https://cli.github.com/)

点页面上方的 Manual 可以跳到官方的使用手册。当然，装好之后直接敲 `gh --help` 也能看到一些简要说明。

## 安装

Windows 平台可以用 scoop 和 Chocolatey 在命令行里安装。直接在官网下载 msi 也可以，不过通过 msi 安装的话，每次更新都要手动再下一次，有点麻烦。

官方 repo 里有提到其他平台的安装方式。这里不多说了。

## 初始化

