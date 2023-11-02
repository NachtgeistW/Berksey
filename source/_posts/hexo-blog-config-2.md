---
title: Hexo 博客 + NexT 主题个性化配置（NexT 8 版）
draft: true
date: 2023-10-31 15:26:22
update: 2023-11-2 16:14:13
categories:
- Mist
- Blog
tags:
- Hexo
- NexT
---

记录将博客的 NexT 主题升级到 8 之后做的一系列更改。

<!-- more -->

首先得说，我的喜好是简洁为上，不喜欢把博客搞得花里胡哨的。所以我的设置不会对 NexT 本体做太多改动。

## 前置准备

在站点根目录下新建一个文件 `_config.next.yml`，然后将 `theme/next/_config.yml` 的内容全部复制到这个文件里。

为什么？

> 配置博客的传统方法的确是通过修改 `theme/next/_config.yml` 下的内容来完成的。但每次升级 NexT 时，所有的修改都会被覆盖。从 Hexo 5.0 开始，Hexo 引入了“替换主题配置（Alternate Theme Config）”。对于 NexT 这个主题来说，Hexo 在合并主题配置时，`_config.next.yml` 文件的优先级高于 `theme/next/_config.yml`，Hexo 会优先用 `_config.next.yml` 渲染主题。

## 基础配置

### 修改站点信息

在 `_config.yml` 的最上面就能看到。

```yml Hexo 设置文件
# Site
title: Berksey
subtitle: '98-74 Ocean Street, Sky Height'
description: ''
keywords:
author: NachtgeistW
language: 
  - zh-CN
  - en
timezone: 'Asia/Shanghai'
```

在这里填入网站、作者等信息。`language` 控制站点显示语言，可以填多个，默认语言是最上面的那个。要改成简体中文的话填 `zh-CN` 就好。

### 切换主题

在 `_config.next.yml` 中搜索 `scheme`，找到下列代码：

```yml NexT 设置文件
# Schemes
#scheme: Muse # 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
#scheme: Mist # Muse 的紧凑版本，整洁有序的单栏外观
scheme: Pisces # 双栏 Scheme，小家碧玉似的清新
#scheme: Gemini # 类似 Pisces
```

选择喜欢的一种样式去掉前面的 `#`，然后给其他主题前加上 `#`。

我以前用的是 Muse，现在有了多级菜单后觉得 Pisces 更适合，改成 Pisces 了。

### 启用夜间模式

该设置就在 `# Schemes` 下面。找到 `darkmode`，将其设置为 `true`。

```yml NexT 设置文件
# Dark Mode
darkmode: true
```

这个夜间模式是跟随系统设置的，不是启用后就一直保持开启状态了。我很喜欢这点。

![夜间模式](darkmode.png)

### 设置知识共享协议 Creative Commons

搜索 `creative_commons` 找到设置项。重点是选一个喜欢的协议填到 `license` 后面，各协议的区别可以看《[“知识共享”（CC协议）简单介绍](https://zhuanlan.zhihu.com/p/20641764)》。

剩下的选项中：

- `sidebar` 设置是否在侧边栏中显示 CC 协议。
- `size` 调整侧边栏 CC 协议的标志大小。
- `post` 设置是否在每篇文章的底部显示 CC 协议。
- `language` 设置的是点击 CC 协议后网站显示的语言，简体中文的设置是 `deed.zh-hans`。

```yml NexT 设置文件
creative_commons:
  # 可选值: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | cc-zero
  license: by-nc-sa
  # 可选值: big | small
  size: small
  sidebar: true
  post: true
  language: deed.zh-hans
```

## 导航栏配置

### 创建新的导航栏菜单、子菜单与三级菜单

![三级菜单](sub_menu.png)

想重点讲讲这个，因为我在这上面耗费了最多的时间。

什么是三级菜单？在 NexT 主题里，图里的“笔记”、“日常”等就是菜单，“读书”、“艺术”等就是子菜单（sub-menu），“书单”和“读书笔记”就是三级菜单（3-level menu）。

要做出跟上图一样的三级菜单，首先搜索 `menu` 项找到设置，然后为其添加下列设置：

```yml NexT 设置文件
menu:
  home: / || fa fa-home
  #（中略）
  日常:
    default: /essay/ || fa fa-icons
    读书: 
      default: /books/ || fa fa-book
      书单: /list.html || fa fa-lines-leaning
      读书笔记: /notes.html || fa fa-feather-pointed
    艺术: /arts.html || fa fa-icons
    脑洞: /xmind.html || fa fa-lightbulb
    待办事项: /todo.html || fa fa-list-check
  about: /about/ || fa fa-user
  #（后略）
```

注意：

- 每个菜单的子菜单都必须有一个 `default` 页。这个页面在点击菜单项时会显示。
- 顶级页面一定要写成 `/xxx.html`，不然顶上的菜单会消失！

```yml 错误的写法
menu:
  home: / || fa fa-home
  #（中略）
  日常:
    default: /essay/ || fa fa-icons
    读书: 
      default: /books/ || fa fa-book
      书单: /list || fa fa-lines-leaning
    艺术: /arts || fa fa-palette
  #（后略）
```

![消失的菜单栏](Snipaste_2023-11-01_15-09-21.png)

然后手动创建这些页面，注意文件名与文件夹结构必须与 `menu` 设置里 `||` 前的目录一致。“日常”这个根菜单可以直接用 hexo 命令 `hexo new page essay` 创建，执行后 Hexo 就会生成一个名为 `essay` 的文件夹，里面包含一个 `index.md` 文件。这个 `index.md` 文件的内容就是点击菜单项时显示的页面。其余的页面需要手动创建到 `essay` 里。比如说《读书》的目录是 books，所以要在 essay 文件夹里再建一个名为 `books` 的文件夹，并在 `books` 文件夹里新建一个 `index.md` 文件。

```txt
BERKSEY\SOURCE
└─essay
  │  arts.md
  │  index.md
  │  todo.md
  │  xmind.md
  │
  └─books
        index.md
        list.md
        notes.md
```

之后与普通文章一样，写好每个 md 文档的标题与内容。需要注意的是这些页面是无法使用 categories 与 tag 的，NexT 官方的说法是它们就是为了不需要设计 categories 和 tag 的页面而准备的。

### 显示归档、分类与标签的数量

在 `_config.next.yml` 中搜索 `menu_settings`，将 `badges` 改为 `true`。

```yml NexT 设置文件
menu_settings:
  icons: true
  badges: true
```

![badge](badge.png)

另外，你还可以用 `menu_settings` 关掉菜单的图标。

### Local Search 本地搜索博客内容

安装 `hexo-generator-searchdb` 插件：

```bash
npm install hexo-generator-searchdb --save 
```

在 `_config.next.yml` 中搜索 `local_search` ，将 `enable` 改为 `true`。

然后在 `_config.yml` 的末尾添加下列代码（这是为了防止炸导航栏的渲染）：

```Hexo 设置文件
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

什么都不用做，导航栏最下面就会自动出现“搜索”一项。

![local search](local_search.png)

## 侧边栏配置

### 配置头像

在 `_config.next.yml` 中搜索 `avatar`，将头像路径换成自己的头像的路径。我头像的路径是 `themes\next\source\images\avatar.jpg`。

`rounded` 设为 `true` 的话头像会变成圆形；`rotated` 设为 `true` 的话鼠标移到头像上时头像会旋转（方形也会。转起来时很草）。

```yml NexT 设置文件
avatar:
  url: /images/avatar.jpg
  rounded: false
  rotated: false
```

### 添加社交链接

在 `_config.next.yml` 中搜索 `social`。然后将链接修改为对应的链接。

它下面的选项 `social_icons` 里，`enable` 是显示图标；`icons_only` 是隐藏文字只显示图标。

```yml NexT 设置文件
social:
  GitHub: https://github.com/NachtgeistW || fab fa-github
  #E-Mail: mailto:yourname@gmail.com || fa fa-envelope
  Weibo: https://weibo.com/u/7312904644 || fab fa-weibo
  Twitter: https://twitter.com/Nightwheel_C || fab fa-twitter
  #Skype: skype:yourname?call|chat || fab fa-skype

social_icons:
  enable: true
  icons_only: false
  transition: true
```

想加自己其他社交平台的链接也是可以的，只要按 `{显示的文字}: {链接} || fab {font awesome的图标名字}` 写就行了。比如说我在#Skype底下再添一行哔哩哔哩的链接，我只要添加一行：`哔哩哔哩: https://space.bilibili.com/12345678 || fab fa-bilibli` 就行了。

## 底部配置

### 设置由 Hexo & NexT.Pisces 强力驱动

在 `_config.next.yml` 中搜索 `powered`，改为 `false` 就能隐藏“由 Hexo & NexT.Pisces 强力驱动”。

```yml NexT 设置文件
  powered: false
```

我反正没关。

### 其他

```yml NexT 设置文件
# 在底部显示语言切换组件。只有在主题里设置了多语言才会真正启用。
language_switcher: false

footer:
  since: 2018 # 建站时间。不填默认今年。

  icon:
    name: fa fa-code # Font Awesome的图片名字。见: https://fontawesome.com/icons
    animated: false # 闪烁图标
    color: "#999999" # 图标颜色。用 Hex 码配置

  copyright: # 不填默认显示作者。填 `false` 隐藏。

  beian: # 备案信息等
    enable: false
```

## 内容配置

![back2top with scroll percent](back2top.png)

### 显示文章字数统计及阅读时长

首先安装 [hexo-word-counter](https://github.com/next-theme/hexo-word-counter) 插件。

```bash
npm install hexo-word-counter --save
```

然后在 `_config.next.yml` 中搜索 `symbols_count_time`，把两个设置都设为 `true`。

```yml NexT 设置文件
symbols_count_time:
  separated_meta: true
  item_text_total: true
```

在 `_config.yml` 末尾添加下列配置：

```yml Hexo 设置文件
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
  exclude_codeblock: false
  wpm: 275
  suffix: "mins."
```

### 文章底部显示 tag

在 `_config.next.yml` 中搜索 `tag_icon`，把它设为 `true`。

```yml NexT 设置文件
tag_icon: true
```

![tag icon](tag_icon.png)

### 打赏

在 `_config.next.yml` 中搜索 `reward_settings` ，将 `enable` 改为 `true`。然后在它下面一点的地方找到 `reward`，把需要开启的打赏选项前面的 `#` 删掉。最后调整链接为自己的图片链接，就可以了。我把图片放到了跟头像同一个路径下。

```yml NexT 设置文件
reward_settings:
  # If true, a donate button will be displayed in every article by default.
  enable: true
  animation: false

reward:
  wechatpay: /images/wechatpay.png
  alipay: /images/alipay.jpg
  #paypal: /images/paypal.png
  #bitcoin: /images/bitcoin.png
```

### 代码块主题配置

NexT 现在提供了非常多的代码主题色可供选择，亮暗双色都可以单独配置。全部主题见 [NexT Highlight Theme Preview](https://theme-next.js.org/highlight/)。

首先选择一个代码渲染引擎。在 `_config.next.yml` 中分别搜索 `highlight` 和 `prismjs`，选择其中一个，将它的 `enable` 设为 `true`，另一个设为 `false`。不改的话默认是 `highlight`。

```yml NexT 设置文件
highlight:
  enable: false
  ...
prismjs:
  enable: true
  ...
```

然后就可以按引擎挑主题了。选到自己喜欢的主题后，在 `_config.next.yml` 中搜索 `codeblock`，然后按引擎修改 `light` 和 `dark` 项的设置。我选了 Highlight 引擎的 one-light 和 onedark 主题，所以我的设置如下：

```yml NexT 设置文件
codeblock:
  # Code Highlight theme
  # All available themes: https://theme-next.js.org/highlight/
  theme:
    light: base16/one-light
    dark: base16/onedark
```

### 设置代码块复制按钮

在上一项下面找到 `copy_button`，将 `enable` 改为 `true`。

另外它有三种样式 default、flat、mac 供选择。我选择了 flat。

```yml NexT 设置文件
codeblock:
  ...
  copy_button:
    enable: true
    # Available values: default | flat | mac
    style: flat
```

### 折叠代码块

在上一项下面找到 `fold`，将 `enable` 改为 `true`。

它的另一个设置项 `height` 意思是，代码块超过该高度就会折叠。我的设置是 400。

```yml NexT 设置文件
codeblock:
  ...
  fold:
    enable: true
    height: 400
```

![折叠代码块](code_block_fold.png)

### 浏览页面显示返回顶部按钮和当前浏览进度

在 `_config.next.yml` 中搜索 `back2top`，将 `enable` 和 `scrollpercent` 都改为 `true`。

```yml NexT 设置文件
back2top:
  enable: true
  # Back to top in sidebar.
  sidebar: false
  # Scroll percent label in b2t button.
  scrollpercent: true
```

### 顶部显示浏览进度

在 `_config.next.yml` 中搜索 `reading_progress` ，将 `enable` 改为 `true`。

![浏览进度](reading_progress.png)

### 文章加密访问

或许有些私密的文章，我不想让其他人看见，所以需要加密这些文章。

我用 [hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt/blob/master/ReadMe.zh.md) 来完成这件事。

首先，通过 npm 安装插件：

```bash
npm install --save hexo-blog-encrypt
```

然后再在文章头添加 `password` 字段：

``` markdown
---
title: Hello World
date: 2016-03-30 21:18:02
password: hello
---
```

这样就可以了。

而且这个插件可以作用于任何页面。比如我就加密了我的待办事项一页。

![文章加密](blog-encrypt.png)

### 数学公式渲染

NexT 支持使用两个库渲染复杂数学公式：mathjax 和 KaTeX。不过它们略吃性能，默认是关闭的。

在 `_config.next.yml` 中搜索 `math` ，选择其中之一，将 `enable` 改为 `true`。

```yml NexT 设置文件
math:
  every_page: false

  mathjax:
    enable: true
    # Available values: none | ams | all
    tags: none

  katex:
    enable: false
    # See: https://github.com/KaTeX/KaTeX/tree/master/contrib/copy-tex
    copy_tex: false
```

然后配置它的启用方式。它有一个 `every_page` 选项，设为 `true` 的话就会在每一页都加载 mathjax / katex。如果你的博客几乎每一篇文章都包含大量复杂数学公式的话就可以将其开启。但我的博客没有，所以我将其设为 `false`，取而代之的是在需要启用的页面的文章头添加 `mathjax: true` 。比如《[HDU 1007 Quoit Design（套圈设计）](/HDU1007-Quoit-Design)》一文里我就开启了 mathjax。

```markdown
---
title: HDU 1007 Quoit Design（套圈设计）
date: 2019/12/24
...
mathjax: true
---
```

![mathjax](mathjax_render.png)

### 图片缩放

MarkDown 渲出来的图片默认是不能点的，想看大图只能右键在新标签页中打开。NexT 提供了两个图片缩放插件：fancybox 和 mediumzoon。不过这两个插件互相冲突，任选其中一个开启就可以了。

在 `_config.next.yml` 中搜索 `fancybox`，`mediumzoon` 就在它下面。选择其中之一，将其改为 `true`。

## 其他配置

### 生成 RSS2

以前我的博客支持 RSS，但是生成的是 RSS1。某一天在访问他人博客时发现 RSS2 更方便他人订阅，于是决定换成 RSS2。

如以前一样通过 npm 安装「hexo-generator-feed」插件：`npm install hexo-generator-feed --save`。

接着在 `_config.yml` 里添加 feed 的配置。我用的是下列配置：

```yml Hexo 设置文件
# RSS
feed:
  enable: true
  type:
    - atom
    - rss2
  path:
    - atom.xml
    - rss2.xml
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

最后在 `_config.next.yml` 的 `menu` 里加上一行：

```yml NexT 设置文件
menu:
  #（中略）
  RSS: /rss2.xml || fa fa-rss
```

![RSS2 feed](Snipaste_2023-10-31_15-33-30.png)

