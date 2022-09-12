---
title: 从 Jekyll 迁移到 hexo 时的博客创建和更新日期问题
date: 2022/09/12
updated: 2022/09/12
category: 
- hexo
tag: 
- hexo
- Jekyll
- PowerShell
---
简单讲一下如何修正修改时间全部为 git 拉取时间的问题。

<!-- more -->

## 背景

决定从我的博客 Berksey 框架从 Jekyll 迁到 hexo 了。在转移时发现，因为本地的 Berksey 项目被我删掉了，所以从 GitHub 上重新拉取了一份，但这也导致本地文件的创建和修改日期全部变成了拉取时的那天。

## 解决的过程

参考了《[升级主题后，文章会刷新更新日期，时间为刚才的部署时间 #60](https://github.com/YunYouJun/hexo-theme-yun/issues/60)》后，将 `_config.yml` 里的 `updated_option:` 改为了 `date`。但并没有真正解决问题。

继续以“hexo 修改时间”为关键字搜索，搜到了《[Hexo Next主题显示文章更新时间](https://www.voidking.com/dev-hexo-next-update-time/)》一文。文中提供了两个信息：

- 在文章头部 YAML 定义中添加 `updated` 字段就会显示自定义更新时间。添加 `date` 字段会显示自定义创建时间。
- 用脚本批量解决这种问题。

另外，Jekyll 的 post 有个要求，就是文件名要以 `YY-MM-DD-title` 的格式命名。最后修改时间已经记不得了（虽然可以去翻 git 提交记录，但那也太烦了），那就直接把创建时间和最后修改时间都设置成文件名里标的时间好了。

虽然我的博文不多，算上这篇也只有 24 篇，但一个个改还是很麻烦；再加上我用的电脑是 Windows 设备，那么自然就该选择用 PowerShell 了。

## 方法

有两个方法可以解决。

### 直接改文件源数据

第一个想法是“既然你读的是文件源数据，那我去修改源数据不就可以了吗？”

那就可以这么做：

1. 获取当前路径下所有的 `.md` 文件；
2. 用正则表达式提取出它们的创建日期；
3. 将提取出的创建日期写入文件的创建日期和修改日期。

PowerShell 脚本如下：

```powershell
Get-ChildItem -Path *.md | foreach-object
{
    $_ -match '(?<year>\d{4})-(?<month>\d{1,2})-(?<day>\d{1,2})'; 
    $_.LastWriteTime = $Matches.month + '/' + $Matches.day + '/' + $Matches.year + ' 00:00:00'; 
    $_.CreationTime = $Matches.month + '/' + $Matches.day + '/' + $Matches.year + ' 00:00:00'
} 
```

子序列 `?<year>` 之类是用来提取数据的，提取出来的数据用 `$Matches` 就可以调用了。

### 在文件的 YAML 段添加 `date` 和 `update` 字段

但上面的办法治标不治本。以后再使用 git 拉取时文件日期还是会变成 git 拉取时的日期。所以还是得在文件头部手动写入 `date` 和 `update` 字段。

步骤大概是这样：

1. 获取当前路径下所有的 `.md` 文件；
2. 用正则表达式提取出它们的创建日期；
3. 获取文件的文本；
4. 因为文件的第一行是 `---`，第二行是标题，所以在文本的第二行后插入两个日期；
5. 把修改后的文本写入文件中。

PowerShell 脚本如下：

```powershell
Get-ChildItem -Path *.md | Foreach-object
{
    $_ -match '(?<year>\d{4})-(?<month>\d{1,2})-(?<day>\d{1,2})';
    $textToAdd = "`n" 
        + 'date: ' + $Matches.year + '/' + $Matches.month + '/' + $Matches.day + "`n" 
        + 'updated: ' + $Matches.year + '/' + $Matches.month + '/' + $Matches.day;
    $fileContent = Get-Content $_ -Encoding utf8;
    $fileContent[1] += $textToAdd;
    $fileContent | Set-Content $_ -Encoding utf8
}
```

``"`n"`` 是用来在 PowerShell 文本中换行的。使用 `Get-Content` 和 `Set-Content` 前都得指定一下编码，不然在写文件的时候有些地方会乱码。

这样就能批量完成了。