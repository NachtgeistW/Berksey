---
title: 使用Python + GitHub Actions定期爬取CeVIO文档并存到GitHub上
description: 要我天天盯着几十个页面的更改真的麻烦死了，不如直接交给程序解决吧！而且早就该这么做了。
date: 2023-10-17
tags: 
- Python, 
- GitHub
- GitHub Actions
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

### 文档内容构成

只要访问过官网就能知道，CeVIO AI 用户指南的链接是

```plain
https://cevio.jp/guide/cevio_ai/
```

页面内容是 HTML。

其中

```html
<body>
    <!-- ▼ヘッダー▼ -->
    <div class="header">
        <img src="http://cevio.jp/guide/cevio_ai/image/header_image_title.png">
    </div> <!-- ▲ヘッダー▲ -->

    <main class="main">

        <!-- ▼ナビゲーションメニュー▼ -->
        <!-- ▼ナビゲーションメニュー▼ -->
        <nav class="left_nav">
        </nav>
        <!-- ▲ナビゲーションメニュー▲ --> <!-- ▲ナビゲーションメニュー▲ -->

        <div class="window">
            <!-- ▼最新情報▼ -->
            <div class="content">
            </div>
            <!-- ▲最新情報▲ -->
        </div>
    </main>


    <div class="footer">
    </div>
    <!-- ▲フッター▲ -->
</body>
```

### 使用 Python 爬取

## 参考资料

- [浅尝Github Actions——自动化定期保存知乎热榜](https://zhuanlan.zhihu.com/p/463667802)