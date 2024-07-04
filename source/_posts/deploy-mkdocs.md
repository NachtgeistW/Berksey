---
title: 配合 Action 在 GitHub Pages 上部署 MkDocs 的坑
date: 2022/05/13
updated: 2022/05/13
category: 
- GitHub
tag: 
- GitHub Action
- GitHub Pages
- MkDocs
---

主要讲一下那些没写在 Material for MkDocs 文档上有关部署的坑。

<!-- more -->

## 背景

最近在折腾 CeVIO 用户指南的中文翻译。因为哔哩哔哩的专栏编辑体验实在是差过头了，所以打算跟博客一样部署到安心信赖的老朋友 GitHub Pages 上。

主题一开始选了别的，后来因为没法完全满足我的需求，查了一下 OI Wiki 之前用的 MkDocs，发现能够部署到 GitHub Pages 上，于是迁了过去。

## Action 拿不到访问权限

### 问题

一切都调好了：建好并将 deploy 分支设成了 `gh-pages` 分支；写好的网页推到了 `master` 分支。然后，依照 [Material for MkDocs 文档给出的方案](https://squidfunk.github.io/mkdocs-material/publishing-your-site/#github-pages)准备实际部署到 GitHub Pages 上，结果问题就来了：

![ci summary](https://raw.githubusercontent.com/NachtgeistW/Berksey/1eb261e22a6304bd907cad175037a9b0c61f0178/_posts/image/2022-05-13_16-41-05.png)

怎么回事呢？是啊怎么回事呢？（

### 解决的过程

总之读了一下 log。注意到在出错的 `mkdocs gh-deploy --force` 这里有两行这样的日志：

```log
remote: Permission to CeVIO-User-Guide-Unofficial/CeVIO-AI.git denied to github-actions[bot].
fatal: unable to access 'https://github.com/CeVIO-User-Guide-Unofficial/CeVIO-AI/': The requested URL returned error: 403
```

嗯？bot 没有访问权限？

直接用第一行删掉项目名去谷歌搜了一下，搜出来一篇名为[《Permission denied to github-actions[bot] even though PAT has permission》](https://github.community/t/permission-denied-to-github-actions-bot-even-though-pat-has-permission/248028)的讨论帖。文里提到的情况跟我的很近。然后二楼给出了解决方法：

>I notice **you don’t set the token in actions/checkout**, and don’t opt-out of configuring the Authorization header with it either. The result is that your PAT is effectively ignored, the push works if the default GITHUB_TOKEN is allowed to push.
>
>I assume either that’s the difference between your repositories, or the one with the issue has a branch branch protection rule that gets in the way.

查看一下文档里给出的 yml 脚本：

```yml
name: ci 
on:
  push:
    branches:
      - master 
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: 3.x
      - run: pip install mkdocs-material 
      - run: mkdocs gh-deploy --force
```

确实没有设置给权限的 token。

然后又想到我那个跑了快两年的[自动更新 GitHub Profile 的 Action](https://github.com/athul/waka-readme)，于是摸到那里又看了一遍它的 README。

总之复习了一下 GitHub Token 和给 repo 设置 token 的流程。现在问题大概就好解决了。

进入 GitHub 的 setting，拉到最下面，选择 Developer settings，然后申一个新的 Personal access tokens，勾上 workflow 的权限（会自动一起勾上 repo 的全部权限，不过有这两个就已经够了）。等 GitHub 生成新的 PAT 后复制一下这个值。

![generate PAT](2022-05-13_17-12-10.png)

然后，进项目里的 Settings，把刚才创建的 PAT 加到它的 Actions secrets 里，起名叫 `GH_TOKEN`。

![add action secrets](2022-05-13_17-14-27.png)

最后稍微改一下 workflow 的 ci：

```yml
- uses: actions/checkout@v2
    with:
    token: ${{ secrets.GH_TOKEN }}
```

提交更改，盯着 Actions 看了好一会儿，绿色的对勾终于出现了。GitHub Pages 的 deployment 也终于成功。

好，问题解决。
