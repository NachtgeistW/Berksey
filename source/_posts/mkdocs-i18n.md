---
title: 设置 MkDocs 文档的多语言支持
description: Setting up the multi-language support for MkDocs
date: 2022-11-21 13:59:29
category: 
- Mist
- CeVIO Docs
tags: 
- MkDocs
- CeVIO
---

记录为 CeVIO AI 用户指南添加多语言支持的过程。

## 插件

这里选择了 [mkdocs-static-i18n](https://github.com/ultrabug/mkdocs-static-i18n)。用的人比较多，而且也支持为每种语言设置一个文件夹，对于用户指南这种非常复杂的文档结构再合适不过了。

安装方法是直接使用 `pip install mkdocs-static-i18n`。

## 配置

因为我计划将不同的语言放到不同的文件夹下，所以直接开两个不同的文件夹。

```txt
C:.
├─css
│  └─stylesheets
├─en
│  ├─audio
│  ├─faq
│  ├─firstguide
│  ├─interface
│  ├─intro
│  ├─operation
│  ├─option
│  ├─others
│  ├─shortcutkey
│  ├─songtrack
│  └─talktrack
└─zh
    ├─audio
    ├─faq
    ├─firstguide
    ├─interface
    ├─intro
    ├─operation
    ├─option
    ├─others
    ├─shortcutkey
    ├─songtrack
    └─talktrack

```

接下来，

## 注意点

### 与插件 git-revision-date-localized 的冲突

在 mkdocs.yml 里设置时，注意 mkdocs-static-i18n 要定义在 git-revision-date-localized 之前，不然就会报如下的错误：

> Error: [git-revision-date-localized] should be defined after the i18n plugin in your mkdocs.yml file. This is because i18n adds a 'locale' variable to markdown pages that this plugin supports.
> （错误：在你的 mkdocs.yml 文件里 [git-revision-date-localized] 应定义于 i18n 插件之后。原因是 i18n 往 markdown 页面中添加了一个该插件支持的 'locale' 变量。

文档里用的定义顺序是：

```yaml
plugins:
  - i18n:
      default_language: !ENV [DEFAULT_LANGUAGE, "zh"]
      languages:
        zh: "中文（简体）"
        en: "English"
  - git-revision-date-localized:
      type: date
      enable_creation_date: true
```
