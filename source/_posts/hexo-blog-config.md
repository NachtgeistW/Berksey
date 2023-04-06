---
title: hexo-blog-config
date: 2022-11-12 13:00:14
category: 
- blog
- hexo
tags:
- hexo
---

记录一些对博客做的个性化配置。
<!-- more -->

## RSS

先通过 npm 安装「hexo-generator-feed」插件。
接着在 `_config.yml` 里添加 feed 的配置。我用的是下列配置：

```yml
# RSS
feed:
  enable: true
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
  icon: icon.png
  autodiscovery: true
  template:
```

最后在 `next\_config.yml` 的 `menu` 里加上一行：

```yml
menu:
  #（中略）
  RSS: /atom.xml || fa fa-rss
```

## 接入 giscus

[giscus](https://github.com/giscus/giscus) 是一个新兴的评论系统，借助 GitHub Discussion 系统驱动。因为 giscus 比原来的评论系统 gitalk 更漂亮，同样支持 GitHub 账号评论，且允许按时间排序；以及想将 issues 归还给它应有的作用，因此将评论系统从 gitalk 迁移到 giscus。

## 页脚

起始年份是 2018。页脚 icon 改成了代码的 icon。
（18 年暑假在腾讯云上自建了博客，后来迁移到 GitHub Pages。）

```yml
footer:
  # Specify the date when the site was setup. If not defined, current year will be used.
  since: 2018

  # Icon between year and copyright info.
  icon:
    # Icon name in Font Awesome. See: https://fontawesome.com/icons
    name: fa fa-code
    # If you want to animate the icon, set it to true.
    animated: false
    # Change the color of icon, using Hex Code.
    color: "#999999"
```