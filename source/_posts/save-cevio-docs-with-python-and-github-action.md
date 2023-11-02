---
title: 使用Python + GitHub Actions定期爬取CeVIO文档并存到GitHub上
category:
- Mist
- CeVIO Docs
tags:
- Python
- GitHub
- GitHub Action
- CeVIO
description: 要我天天盯着几十个页面的更改真的麻烦死了，不如直接交给程序解决吧！而且早就该这么做了。
date: 2023-10-17 00:00:00
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
        <nav class="left_nav">
        </nav>

        <div class="window">
        </div>
    </main>

    <div class="footer">
    </div>
</body>
```

其中，`<nav class="left_nav"></nav>` 这一块里有文档其他页面的链接，要依靠这个块进一步访问其他文档页面；`<div class="window"></div>` 是正文内容，也是我们要处理的对象。

检查一下，发现每个文档的结构基本都如上所示。那就可以开始爬取了。

思路是：拿到首页的内容->从首页的侧边栏拿到所有文档页的链接->依据链接划分子目录，保存文档的内容

### 使用 Python 爬取

CeVIO AI 的文档访问起来非常容易，甚至不需要构造 header 模拟浏览器，直接用 request 库 get 就行。

因为文档内容是日语，所以为了编码不出错，指定一下拿到的 response，指定为 `utf-8`。接下来，用 BeautifulSoup 库解析拿到的内容，解析器选 lxml，原因是它容错率较高且解析速度快，与原生的不相上下。

```python
response = requests.get(url)
soup = BeautifulSoup(response.text, 'lxml')
```

然后就是我最喜欢 BeautifulSoup 库的一个地方：它的 CSS 选择器。只需要用一个 `select()` 命令，就可以直接把目标 tag 作为一个 `list` 全部选出来。

CeVIO AI 文档的左侧导航栏在 `body > main > nav > p > a` 下，所以只需要

```python
nav_links = soup.select('body > main > nav > p > a')
```

就能全部选出来。

正文内容就一个，而且我们也不需要列表，因此改用 `select_one()`，以返回单个（第一个）元素。这里直接用类名查找：

```python
content = soup.select_one("[class~=window]")
```

直接拿到的正文是 HTML 格式，对普通人来说太难读了。因此我用另一个库 markdownify 将其直接转为 markdown 文件，简单有效地搞定可读性。

左侧导航栏里包含的所有文本，思路也差不多。首先用一个 `for` 循环来遍历所有的链接。在遍历时，先用正则拿到子文件夹的名字，如果目录里没有这个文件夹就新建它。然后就是跟首页一样的步骤：分析页面、保存正文、保存图片。

### 使用 git 提交更改

因为 GitHub Action 实际上是开了个机器跑脚本，而且这台机器自带 git 环境。因此直接编写 git 提交命令就可以了。

注意要先配置一下用户名和邮箱（毕竟每次运行 Action 的机器都是一台新机器）。

### 运行 GitHub Action

把 Python 代码整理为一个 .py 文件。把运行 py 文件的命令和提交更改的 git 命令整理到一个 bash 文件中，供 GitHub Action 运行。

```bash
python3 ./cevio_spider.py

year=`date +%Y `
month=`date +%m `
day=`date +%d `
now=$year-$month-$day

git config --global user.email ""
git config --global user.name "Crawler"

git add .
git commit -m "update $now"
```

`on` 设置为星期一、三、六下午 4 点定时运行，比较均摊时间，且这个时间点 CeVIO 的更新基本已经推了；同时保留一个手动运行的方式，方便测试。

```yml
on: 
  workflow_dispatch:
  schedule:
    - cron: '0 8 * * 1,3,6'
```

整体分为四步：设置 Python → 安装依赖 → 运行 bash 文件 → 推送到仓库。

不过最后一步推送的 action，GitHub 自己没有封装，所以我用的是一个第三方的高星 action [ad-m/github-push-action](https://github.com/ad-m/github-push-action)。注意的是要提前配置好 `github_token`。配置的过程之前也写过（见[《配合 Action 在 GitHub Pages 上部署 MkDocs 的坑》](/deploy-mkdocs)）。分支选择 `main` 就行了。

```yml
- name: GitHub Push
uses: ad-m/github-push-action@master
with: 
    github_token: ${{ secrets.GITHUB_TOKEN }}
    branch: main
```

## 实际运行

![运行效果](Snipaste_2023-10-27_17-38-34.png)

效果挺不错的，更改清晰可见。

唯一的问题是它在没有更改的时候会运行失败，然后 GitHub 就会给我发邮件；而成功的时候就不会（理所当然）。

![运行失败时的 action](Snipaste_2023-10-27_17-40-02.png)

这反而跟我想要的效果（有更改时发邮件，无更改时没有）反过来了。留待日后改进。

## 参考资料

- [浅尝Github Actions——自动化定期保存知乎热榜](https://zhuanlan.zhihu.com/p/463667802)