---
title: 通过命令行构建 Unity 项目其二：iOS 篇
draft: true
date: 2023-11-27 19:52:10
tags:
---

讲一下如何通过命令行打 iOS 的包。其实与 Android 差不多，但是最后要多走一些 XCode 的流程。

<!-- more -->

注：iOS 的流程我最后因为权限不足没能完整走完。但我想应该是差不多的。

[Technical Note TN2339: Building from the Command Line with Xcode FAQ (apple.com)](https://developer.apple.com/library/archive/technotes/tn2339/_index.html)

马上遇到了一个问题：

``` bash
> xcodebuild -help
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```

好在很顺利地就找到了解决方案：[xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance · Issue #569 · nodejs/node-gyp (github.com)](https://github.com/nodejs/node-gyp/issues/569)

> 1. Install Xcode
> 2. Run `sudo xcode-select -s /Applications/Xcode.app/Contents/Developer`

[Xcode11 使用xcrun altool上传IPA包 - 简书 (jianshu.com)](https://www.jianshu.com/p/26b8dccb5f26)