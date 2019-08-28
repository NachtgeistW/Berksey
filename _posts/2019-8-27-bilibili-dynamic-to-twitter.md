---
title: 哔哩哔哩动态同步到推特的方法
category: WhiteTech
tag:
- IFTTT
- Webhook
---
想法是利用 [RSSHub](https://rsshub.app/) 将 B 站动态变成可订阅的 RSS 源，然后经 integromat 区分文字动态和带图动态，并过滤掉投票、不带文字转发动态和不带文字分享视频，最后利用 IFTTT 完成同步。获取动态的方法受 [BilibiliEcho - 同步你的 Bilibili 动态到 Typecho！](https://www.pluvet.com/archives/bilibili-echo-publish.html)启发；进阶的同步方式参考了[微博同步至 Twitter，这里有更好的方式](https://sspai.com/post/51942)。

---

## 操作步骤

第一步**设置 IFTTT**、第二步**设置 Integromat** 的步骤与[微博同步至 Twitter，这里有更好的方式](https://sspai.com/post/51942)基本一致。你可以把 Weibo 字符全替换成 Dynamic，用来跟同步微博用的 Webhook 加以区分。

不一致的地方：

image text Weibo 处的筛选条件：下拉框选择的不是``Does not exist``，而是``Dose not contain``。下面的框内填入``no_image_card.png``。

only text Weibo 处的正则：``(Repost)|(转发动态)|(\/\/)|(轉發動態)|(分享视频)|(我参与了投票)``。这个正则除了会过滤掉文中提到的动态外，还会把投票和不带原创文字的视频分享过滤掉。

### 回到 IFTTT 再次设置

创建同步微博内容到 Integromat 的 Webhook 触发器：

1. if this：``RSS Feed``，选择 New Feed Item，在 Feed URL 里填 ``https://rsshub.app/bilibili/user/dynamic/UID``，这里的 UID 可以在个人界面找到；
   ![UID](https://github.com/NachtgeistW/Berksey/blob/master/_posts/image/2019-08-28-165359.jpg)
2. then that：Webhook，选择 Make a web request，URL 填写设置 Integromat 里面拿到的 Webhook 地址：``https://hook.integromat.com/xxxxxxxxxxxxxxxx``，Method 选择 ``POST``，Content Type 选择 ``application/x-www-form-urlencode``，Body 填写 ``text={{Text}}&image={{PhotoURL}}``；
3. 点击 Create Action，点击 Finish。

---
后话：我觉得每个人都该去给 RSSHub 点个 star。
