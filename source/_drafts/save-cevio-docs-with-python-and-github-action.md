---
title: 使用Python + GitHub Actions定期爬取CeVIO文档并存到GitHub上
description: 要我天天盯着几十个页面的更改真的麻烦死了，不如直接交给程序解决吧！而且早就该这么做了。
date: 2023-10-17
category: 
- Python
tags: 
- Python
- GitHub
---

维护 CeVIO AI 用户文档的中文和英文翻译已有一年之久了。然而在这一年里，我的工作流总是：

CeVIO 官推发布版本更新公告 -> 我打开文档官网翻译最新情报 -> 与现有文档逐页对比变更并翻译

手动对比实在是费心费力。而且我作为一个社畜，不可能随时盯着 CeVIO 的官推看（而且它还天天发打折促销消息，吵死了）。于是作为一个程序员，很自然地我就希望能写一个程序帮我自动爬取更新，在发现有新变更的时候能标记这些变更的位置，而且还能通知我一声。

## 需求

- 爬取文档，提取出文档的正文部分和图片
- 将正文的的 HTML 处理成更易读的格式
- 每隔一段时间自动爬取一次
- 能够对比变更，并标注这些变更的位置
- 有新变更的时候通知我

## 选择工具

由于我以前有写 Python 的经验，因此爬取与数据处理的部分我第一反应就是使用 Python 完成。Python 的爬虫库发展完善： request 库十分成熟，省心省力；Beautiful Soup 处理数据处理得很漂亮。

但在定期运行方面我纠结了好一会儿。最优解大概是放在服务器上，然后开个 Scheduler 或者 APScheduler 库来跑定时任务。但我没有服务器。后面查了很多资料，然后发现 GitHub Actions 似乎是个还行的方案，而且 git 天然就适合做变更对比，搭配 GitHub 更能快速追踪变更。

大致的工具理好了，就动手开始写吧。

## 实现

### 确认文档内容构成

只要访问过官网就能知道，CeVIO AI 用户指南的链接是

```plain
https://cevio.jp/guide/cevio_ai/
```

页面内容是 HTML。

精简地提取一下首页的结构，大概是这样的：

```html
<body>
    <div class="header">
    </div>

    <main class="main">
        <!-- ▼ナビゲーションメニュー▼ -->
        <!-- ▼ナビゲーションメニュー▼ -->
        <nav class="left_nav">
        </nav>
        <!-- ▲ナビゲーションメニュー▲ -->
        <!-- ▲ナビゲーションメニュー▲ -->

        <div class="window">
        </div>
    </main>

    <div class="footer">
    </div>
    <!-- ▲フッター▲ -->
</body>
```

其中，`<nav class="left_nav"></nav>` 这一块里有文档其他页面的链接，要依靠这个块进一步访问其他文档页面；`<div class="window"></div>` 是正文内容，也是我们要处理的对象。

检查一下，发现每个文档的结构基本都如上所示。那就可以开始爬取了。

首先，先拿到首页的内容

### 使用 Python 爬取

CeVIO AI 的文档访问起来非常容易，甚至不需要构造 header 模拟浏览器，直接用 request 库 get 就行。

因为文档内容是日语，所以为了编码不出错，指定一下拿到的 response，指定为 `utf-8`。

接下来，用 BeautifulSoup 库解析拿到的内容。这里的解析器我用的是 lxml，原因是它在于解析“杂乱”或者包含错误语法的 HTML 代码的性能更优一些，而且它可以容忍并修正一些问题，例如未闭合的标签、未正确嵌套的标签，以及缺失的头（head）标签或正文（body）标签。

```python
response = requests.get(url)
soup = BeautifulSoup(response.text, 'lxml')
```

然后就是我最喜欢 BeautifulSoup 库的一个地方：它的 CSS 选择器。

只需要用一个 `select()` 命令，就可以直接把目标 tag 作为一个 `list` 全部选出来。CeVIO AI 文档的左侧导航栏在 `body > main > nav > p > a` 下，所以只需要

```python
soup.select('body > main > nav > p > a')
```

就能全部选出来。

正文内容就一个，而且我们也不需要列表，因此改用 `select_one()`，以返回单个元素。



## 参考资料

- [浅尝Github Actions——自动化定期保存知乎热榜](https://zhuanlan.zhihu.com/p/463667802)