---
title: 笔记 from《【A.I.VOICE生技术部】第1回：NEUTRINO入门 讲师：くろ州＝多刀流P》
description: 96 州的亲妈喂饭级 NEUTRINO 101 课程
date: 2022-10-30 16:17:01
updated: 2022-11-1
category: 
- 调声
- NEUTRINO
tags:
- NEUTRINO
---

请配合《第１回 A.I.VOICE 生放送 技術部》（[YouTube 本家](https://youtu.be/38Xt4uvxwnM)；[哔哩哔哩搬运](https://www.bilibili.com/video/BV15t4y1j7f1/)）观看。

<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
  <iframe src="https://www.youtube-nocookie.com/embed/38Xt4uvxwnM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>

<!-- <div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="//player.bilibili.com/player.html?bvid=BV15t4y1j7f1&high_quality=1"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div> -->

---

**目录**

- [第一部分 准备](#第一部分-准备)
  - [下载](#下载)
    - [＜NEUTRINO 本体＞](#neutrino-本体)
    - [＜NEUTRINO 琴葉 茜・葵＞](#neutrino-琴葉-茜葵)
    - [＜NEUTRINO 調声支援ツール（NEUTRINO 调声支援工具）＞](#neutrino-調声支援ツールneutrino-调声支援工具)
    - [＜乐曲数据＞](#乐曲数据)
  - [配置](#配置)
    - [放置文件](#放置文件)
    - [配置 NEUTRINO 调声支援工具](#配置-neutrino-调声支援工具)
  - [检查配置](#检查配置)
- [第二部分 填入乐谱](#第二部分-填入乐谱)
  - [填入音符](#填入音符)
  - [0:46:45 填入歌词](#04645-填入歌词)
  - [通过其他方式获取乐谱](#通过其他方式获取乐谱)
    - [0:49:50 UtaFormatix](#04950-utaformatix)
    - [0:50:41 CeVIO](#05041-cevio)
  - [0:55:29 合成声音](#05529-合成声音)
- [1:08:07 第三部分 学习操作](#10807-第三部分-学习操作)
  - [SCORE 页的操作](#score-页的操作)
    - [1:12:44 粘合工具](#11244-粘合工具)
    - [撤销、重做与历史记录](#撤销重做与历史记录)
    - [1:22:01 音符的右键菜单：设置与取消换气](#12201-音符的右键菜单设置与取消换气)
    - [1:29:04 修改歌词](#12904-修改歌词)
  - [1:32:38 TIMING 页的操作](#13238-timing-页的操作)
  - [1:45:15 PITCH 页的操作](#14515-pitch-页的操作)
  - [2:05:20 DYNAMICS 页的操作](#20520-dynamics-页的操作)
- [第四部分 调声](#第四部分-调声)
  - [2:13:09 修改乐谱的填词，调整有违和感的地方](#21309-修改乐谱的填词调整有违和感的地方)
  - [2:33:09 调整 TIMING，从更细节的地方调整发音](#23309-调整-timing从更细节的地方调整发音)
  - [2:37:40 调整 PITCH](#23740-调整-pitch)

---

## 第一部分 准备

### 下载

#### ＜NEUTRINO 本体＞

从 NEUTRINO 官方网站下载最新版：[NEUTRINO - NEURAL SINGING SYNTHESIZER](https://studio-neutrino.com)

- NEUTRINO 和它的大部分声库都通过 Google Drive 配布。光引擎大小就接近 2G。请自备梯子。

#### ＜NEUTRINO 琴葉 茜・葵＞

从 A.I.VOICE 支持网站下载最新版：[A.I.VOICE™  ユーザーサポートサイト](https://aivoice.jp/member/)
※必须预先注册 A.I.VOICE 琴葉 茜・葵

- 没有买的话，用 AI 切蒲英、Merrow 等 NEUTRINO 的其他声库也能跟完这个教程，但不同声库的效果可能会有所不同。

下载方式：

1. 进入 A.I.VOICE 的支持网站，在右侧登录账号（如果没有账号，请点下面的「新規登録」注册一个账号）；
   ![login to aivoice support website](aivoice-support-website.png)
2. 点击「製品情報」，检查账号中是否有「A.I.VOICE 琴葉 茜・葵」；
   ![check product info](aivoice-check-product-info.png)
3. 点击「ダウンロード」，跳转到下载页面；
4. 点击特典切换到特典页，然后找到最新的「琴葉 茜（NEUTRINO-Library）」与「琴葉 葵（NEUTRINO-Library）」，点击链接下载。
   ![download library in aivoice download page](aivoice-download-page.png)
   
   <!-- - 注：但是现在 A.I.VOICE 的特典页有 bug，点击链接后，地址栏会一闪而过下载链接，然后**直接回退到特典页，而且并不会启动下载**。如果一直没法复制到下载链接，可以试试通过下列两个链接直接下载：
    <https://aivoice.jp/member/downloads/extra/241>（琴葉 葵（NEUTRINO-Library）_v1.2.0.zip）
    <https://aivoice.jp/member/downloads/extra/242>（琴葉 茜（NEUTRINO-Library）_v1.2.0.zip）
   - 我知道你在想什么。没买的话点进去会报 404。老老实实去买 AIV 琴叶吧（ -->

#### ＜NEUTRINO 調声支援ツール（NEUTRINO 调声支援工具）＞

从 GitHub 下载最新版：[Releases · sigprogramming/tyouseisientool](https://github.com/sigprogramming/tyouseisientool/releases)

- 直播时最新的版本为 1.7.4.6。只需下载当前最新的版本即可，不需要用跟视频里一样的版本。
- 注意：不要下载版本号后跟 `-beta` 的 beta 版。beta 版有出 bug 的可能。

下载方式：

1. 进入 NEUTRINO 調声支援ツール的 Releases 页，往下滚动找到当前最新的版本；
2. 点击 Assets 旁边的箭头展开发布文件列表，点击 .zip 文件下载。

![how to download neutrino tuning support tool](download-neutrino-tuning-support-tool.png)

#### ＜乐曲数据＞

じゃむ。（作詞・作曲：みきとP）
从 A.I.VOICE 的网站下载：<https://aivoice.jp/tech/Jam-SongData.zip>

### 配置

#### 放置文件

解压下载好的所有文件。NEUTRINO 本体、NEUTRINO 调声支援工具和乐曲数据的位置随意，但为了方便操作，建议放在同一个文件夹下。

将 `琴葉 茜（NEUTRINO-Library）` 文件夹内的 `AKANE` 移动到 `\NEUTRINO\model` 文件下。对 `琴葉 葵（NEUTRINO-Library）` 文件夹内的 `AOI` 做同样的操作。

假设文件夹名为 `NeuKOTONOHA`。全部完成后，文件夹结构应该类似下列的 program structure（已省略部分本次讲座中未涉及的文件与文件夹）：

```powershell
$ tree NeuKOTONOHA
卷 Local Disk 的文件夹 PATH 列表
卷序列号为 0000000A C2BE:6ACA
C:\...\NEUKOTONOHA
├─Jam-SongData
│      jam Inst_2mix4816_BPM165.wav
│      jam Inst_mastered4816_BPM165.wav
│      readme.txt
│      TrackA.xml
│      TrackB.xml
│
├─NEUTRINO
│  ├─bin
│  ├─LICENSE
│  ├─model
│  │  ├─AKANE
│  │  ├─AOI
│  │  └─MERROW
│  ├─output
│  ├─score
│  │  ├─label
│  │  └─musicxml
│  └─settings
└─NEUTRINOTyouseiSienTool
    │  NEUTRINOTyouseiSienTool.exe
    ├─Backup
    ├─bin
    ├─ja-JP
    ├─log
    └─temp
```

#### 配置 NEUTRINO 调声支援工具

双击 NEUTRINOTyouseiSienTool.exe 运行。在如图所示的位置点击，打开设置页面。

![open neutrino tuning support tool](open-neutrino-tuning-support-tool.png)

点击右侧文件夹图标，指定 NEUTRINO 引擎所在的位置（即上述 program structure 显示的 NEUTRINO 文件夹所在路径）。

![set neutrino folder path](set-neutrino-folder-path.png)

调声工具支持英语或日语。如有需要可切换为另一种语言。

另外还可以在其他选项中修改其他设置。例如可以在 Transport 页中修改光标的移动方式，令其播放完后返回播放的起始处。

点击 OK 完成设置。

### 检查配置

将 `Jam-SongData` 下的 `TrackA` 或 `TrackB` 拖入调声工具中。现在应该能在乐曲的开头看见调号、拍号以及曲速，在第 14 小节附近看见已填好词的音符。但点击屏幕上的播放按钮或按空格播放时，是没有声音的，TIMING 等参数也处于不可编辑的状态。
点击 NEUTRINO 选项卡，选择 NEUTRINO Control Panel 选项，调出 NEUTRINO 控制面板。

![open neutrino control panel](open-neutrino-control-panel.png)

Parameter Estimation（参数估算）下唯一的选项 NEUTRINO 将会使用选择的模型（歌手），根据乐谱估算音高、声质、颤音，并生成中间文件以备合成。
Synthesis（合成）下提供了两种合成声音的选项：

- WORLD 选项会根据中间文件合成声音。运行完 NEUTRINO 后在调声界面的试听使用的音频即为该模式生成的音频。
- NSF 选项同样根据中间文件合成声音，不过它使用了神经网络，质量更高、更自然，但需要支持 CUDA 的 GPU 和至少 3GB 的显存，导出所需的时间也更长。

![neutrino control panel](neutrino-control-panel.png)

点击 NEUTRINO 选项，在 Model 中选择一位歌手（默认为 MERROW），然后点击 Run NEUTRINO 开始估算。

![parameter estimation neutrino](parameter-estimation-neutrino.png)


待 Run Process 窗口的进度条显示 Completed 时，点击 Close 关闭窗口，回到调声界面。此时调声工具应在左下角显示 Run WORLD 并自动开始运行 WORLD。等这行字消失后，于第 14 小节附近点击灰色的小节栏，将光标移至乐谱的开头，然后点击屏幕上的播放按钮，或按下空格，播放音频。若听到选择的歌手唱出《Jam》的歌词，说明 NEUTRINO 引擎和调声工具都已配置完成并处于正常运行的状态。

## 第二部分 填入乐谱

创建一个新的项目。随便起个名。

![create new project](create-new-project.png)

将光标移至开头，在小节栏上右击。右键菜单从上到下依次是曲速、节拍以及调号的添加选项。根据需要修改。调声工具会在**光标所在位置**插入修改后的设置。

![add tempo](add-tempo.png)

工具栏中有四个工具，从左到右依次为选择工具 (Select)、画笔工具 (Draw)、擦除工具 (Eraser) 与粘合工具（Glue，原意是胶水）。

- 选择工具（快捷键 <kbd>1</kbd>）用于选择已有的音符。
- 画笔工具（快捷键 <kbd>2</kbd>）用于画新的音符。
- 擦除工具（快捷键 <kbd>3</kbd>）用于擦除音符。
- 粘合工具（快捷键 <kbd>4</kbd>）用于将点击的音符与它的后一个音符合并为一个音符。

### 填入音符

切换到画笔，在钢琴窗上点击并拖动，画一些音符。

![draw scale](draw-scale.png)

画笔工具移动到音符内时可以通过单击并拖拽的方式移动音符；移动到音符边缘变为 ↔ 的形状时，可以通过单击并左右拖拽的方式调整音符长度。
调出 NEUTRINO 控制面板运行 NEUTRINO，就能听到效果。

<audio controls="test_aoi_scale">
  <source type="audio/wav" src="test_aoi_scale.wav"></source>
  <p>Your browser does not support the audio element.</p>
</audio>

音符重叠时，重叠的音符颜色会变浅。

![note overlap](note-overlap.png)

切换到 TIMING、PITCH 或 DYNAMICS 页，勾选旁边的复选框，可以叠加显示参数。

![show timing, pitch dynamics](show-timing-pitch-dynamics.png)

（中间这段时间大概是有人问为什么按照设置走了，但还是听不到 Akane 和 Aoi 的声音，只能听到钢琴声。两个人思考了一下，给出的答案大概是检查一下声库和引擎版本。）

### 0:46:45 填入歌词

在音符上单击修改单个音符的歌词。
使用选择工具批量选择待填入歌词的音符；或是选择一个音符，然后按住 <kbd>Shift</kbd> 键，然后选择另一个音符，来批量选择音符。
在音符上右键，然后选择 Pouring Lyrics（灌入歌词）。这将会从选择的音符开始填入歌词。

![pouring lyrics](pouring-lyrics.png)

例如，从第一个音符处填入「おねえちゃんかわい」，然后点击确定。

![pouring lyrics 2](pouring-lyrics-2.png)

重新生成后应该能听到这样的声音：

<audio controls="test_oneechankawaii">
  <source type="audio/wav" src="test_oneechankawaii.wav"></source>
  <p>Your browser does not support the audio element.</p>
</audio>

### 通过其他方式获取乐谱

#### 0:49:50 UtaFormatix

~~又名《科林，我的超人》~~

网址：[UtaFormatix](https://sdercolin.github.io/utaformatix3/)

选择待转换的工程文件，导入到网站中。

![utaformatix select file](utaformatix-select-file.png)

下拉，找到 MusicXml 一项，点击。

![utaformatix select format](utaformatix-select-format.png)

确认设置后点击下一步。

![utaformatix setting](utaformatix-setting.png)

点击导出，下载打包好的 .musicxml 文件。

![utaformatix export](utaformatix-export.png)

#### 0:50:41 CeVIO

> 96 州：你看，是鱼卡日桑（笑）

（96 州说出 CeVIO 的一瞬间我笑疯了)

使用「导出MusicXML」一项便可以把当前轨道导出为 .musicxml 文件。

![CeVIO](cevio.png)

（栗田一直在说缘兔可爱hhh）

CeVIO AI 与 CS 版本均有此功能。

更详细的介绍请参见《[MIDI / MusicXML - CeVIO AI 用户指南中文版](https://cevio-user-guide-unofficial.github.io/CeVIO-AI/songtrack/fileimport/)》。

### 0:55:29 合成声音

现在虽然在调声工具里生成了供试听的声音，但并未导出音频文件。

调出 NEUTRINO 控制面板，选择 WORLD 或者 NSF 中的一种并点击，就能导出音频文件。导出的文件位于 `NEUTRINO\output` 文件夹下。使用 NSF 模式导出的音频会带有一个 `_nsf` 的后缀。

就音色而言，相比 WORLD 而言，NFS 会生成更温柔的声音。有个推荐的导出方式是 Akane 使用 WORLD 模式导出，Aoi 使用 NSF 模式导出。

> 96 州：茜和葵推荐用 NSF。
> 栗田：啊，所以还是依声库而不同。
> （中间 96 州问了下能不能提其他声库，栗田说可以。）
> 96 州：再比如，切蒲英的话相对而言用 WORLD 会融合得更好；俊达萌用 NSF 有压倒性的优势。感觉音源不同的话性格也会各显不同。

---

中场休息：《じゃむ。》

<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;">
    <iframe src="//player.bilibili.com/player.html?bvid=BV1jg411X7yn&high_quality=1"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe>
</div>

---

## 1:08:07 第三部分 学习操作

> 96 州：选曲固然重要，但在曲子之外也要想象这两个歌姬在唱这首歌的时候会带着怎么样的情感。
> 栗田：也就是在唱某句的时候怎么样演绎会更好一点。

重新打开 TrackA，用 Akane 的 model 运行一遍 NEUTRINO。接下来的第三与第四部分都将让 Akane 来做示范。

### SCORE 页的操作

#### 1:12:44 粘合工具

首先第一句，あまがみしないで处的 `ない`，Akane 唱成了 na i。按日语的唱法来说应该唱成连贯的 nai。观察波形和音高可以注意到，Akane 在这里甚至刻意用了点力。这并不是我们想要的。

![score editing nai](score-editing-nai.png)

选择粘合工具（快捷键 <kbd>4</kbd>），点击 `な`，将后面的 `い` 与其合并。连续点击的话，会以点击的音符为首，一直合并后面的音符。
合并后的效果需要重新用 NEUTRINO 生成才能听到。重新生成后的波形和音高就变成了下图所示的样子。断连感消失了。

![score editing nai-2](score-editing-nai-2.png)

遗憾的是目前调声工具没有分割工具。要把合到一起的音符拆开——比如还是这个 `ない`——目前只能先缩减原音符的时长，用画笔工具画一个新音符，然后双击原音符，把歌词中的 `い` 复制到新音符中。可能以后会有吧（

#### 撤销、重做与历史记录

撤销的快捷键为 <kbd>Ctrl+Z</kbd>。重做的快捷键为 <kbd>Ctrl+Shift+Z</kbd>。在 Menu 中可以查看操作历史记录。

![editing history](edit-history.png)

#### 1:22:01 音符的右键菜单：设置与取消换气

在音符上右击可以调出音符的右键菜单。选中一个音符后再右击，可以使用右键菜单的所有功能。
请注意，选中某个音符时，不管在哪里按右键（即使是在别的音符上），调出的右键菜单也只会作用于选中的音符上。【请看96 州 1:22:43 起的错误示范（逃）】

![note right-click menu](note-right-click-menu.png)

设置换气（Set Breath）与取消换气（Unset Breath）用于手动指定歌手的换气。一般而言，歌手会自动在音符间的间隔之间换气，如果有想让歌手在音符中途换气的地方，就可以用这个功能来强制让歌手换气。
（NEUTRINO 不像 CeVIO 那样自动生成呼吸音，要添加呼吸音的话还是得用 exVOICE 或者百搭音。）

一个用法是将原本断开的音符拉长到与下一个音符相连，然后为其设置换气。与原本断开的停顿相比各有优势，根据需要来选择。
示例：
原本的乐谱：

![unset breath](unset-breath.png)

<audio controls="TrackA-unset-breath">
  <source type="audio/wav" src="TrackA-unset-breath.wav"></source>
  <p>Your browser does not support the audio element.</p>
</audio>

手动指定换气后：

![set breath](set-breath.png)

<audio controls="TrackA-set-breath">
  <source type="audio/wav" src="TrackA-set-breath.wav"></source>
  <p>Your browser does not support the audio element.</p>
</audio>

另外，使用粘合工具连接两个音符时，后一个音符的换气也会继承到合并后的音符中。

#### 1:29:04 修改歌词

你可能已经注意到了：一个音符内可以填入多个字符。

另外，灌词 (Pouring Lyrics) 功能强制一个音符对应一个字符。如果填入的字符数量超过了选择的音符的数量，则超出的字符会自动顺延到后面的音符上。

### 1:32:38 TIMING 页的操作

（正式开始在1:36:00，中间这几分钟两人聊上头了）

> 栗田：每次执行 NEUTRINO 的时候，其实都像是直接操作琴叶姐妹的 NEUTRINO 大脑来重新唱一遍。与此相对的，跟作曲比，我们的调声就像是拿鸡毛掸子扫灰一样。想象出来的话，就像是琴叶姐妹进了录音室录了一段歌之后，我们做了调整，让她们再唱的时候，之前的那个感觉就会消失掉。因此，至少在让她们唱过一遍之后再去调声。
> 96 州：对，因为在 SCORE 页制作乐谱让她们唱以及调整 TIMING 的过程就像是去录音；之后调整 PITCH 之类的过程就相当于是让她们去重唱，因此和调整之前的感觉会不同。让 NEUTRINO 的 Akane 唱的时候，基本上来说，一般来讲是先调整（SCORE 和 TIMING）到“好，挺完美的了，只要（对声音）稍加调整就完全能用了”的这种状态之后再去调整 PITCH 之类的内容。
> 栗田：懂了懂了。也就是说，不要把她们想成软件音源，而是“琴叶姐妹就在这里”，这种态度很重要。
> 96 州：她们活起来了。（笑）
> 栗田：（笑）活着呢！她们就在你身边唱歌呢！绝对别忘了！
> 96 州：所以说大家不要弄错了，其实就是琴叶姐妹在唱，不是什么软件在唱。
> 栗田：对于这个来说，就像是“诶，再为你唱一次吧”的感觉。
> 96 州：但是，如果之前已经调整过 PITCH 的话，她们会顶嘴的哦（大概是指编辑过的 PITCH 又变回编辑前的状态吧）。虽然不会真的顶嘴。
> 栗田：理解了。
> 96 州：所以说，虽然说了很多次了，对待 NEUTRINO 和人类的（方式）其实没有什么不同。如果能意识到这一点就很不错了。

调声工具左侧的参数排列其实就是推荐的调声步骤：在 TIMING 页调整发声时机→在 PITCH 页调整音高→在 DYNAMICS 页调整音量的动态。

由于修改 TIMING 会影响 NEUTRINO 对 PITCH 与 DYNAMICS 推算的结果，所以优先建议修改 TIMING。如果先前有 CeVIO 的调声经验的话应该对 TIMING 不陌生（可以直接跳过这节）。
NEUTRINO 使用的音素表请从它的 Google Drive 下载。

![phoneme](phoneme.png)

每条蓝线代表一个音素实际发声位置的开始。例如开头的 `a` 代表着「あ」开始发声的位置；`m` 和 `a` 分别代表了「ま」中「m」和「a」开始发声的位置。结合波形可以看得更清楚。
在蓝线上左右拖拽可以调整发音的起始位置。同样，需要调用 NEUTRINO 重新生成才能看到效果。注意到调声助手已经自动勾选了「Hold the timing (use the edited timing) 」【维持时间（使用已编辑的时间）】一项。

![phoneme](edited-timing-generate.png)

修改后的效果：

![phoneme](edited-timing-generate-2.png)

之前讲到的“手动指定换气”里，停顿的时间就可以用 TIMING 细调。

有个规则：想强调哪个音，就把它的辅音（比如「ま」的「m」）稍微往前拉长一点。
另外，比如说，像上图的「が」，它的「a」开始的位置并不是严格踩在音符「が」开始的位置，而是稍微往后一点。在日本那边有个词叫「後ノリ」，用来形容这种稍稍滞后于节奏的情况。与之相对应的词叫「前ノリ」，就是稍微前一点的情况。

TIMING 的调声风格因人而异。请根据你的喜好调整 TIMING。

### 1:45:15 PITCH 页的操作

> 96 州：这是最有意思的地方。真的，是最有意思的地方。

![tools for editing pitch](pitch-tools.png)

PITCH 包含了许多工具，从左到右依次为：

- 选择工具 (Select)：在这个窗口里没啥用（
- 颤音工具 (Vibrato)：为选择范围内的音高添加颤音。允许做更进一步的设置。
- 画笔工具 (Draw)：绘制音高。
- 擦除工具 (Eraser)：擦除选择范围内对音高的修改，将其复原回 NEUTRINO 默认的参数。
- 平滑工具 (Smooth)：使选择范围内的音高变为平滑的曲线。
- 升降工具 (UpDown)：上下移动选择范围内的音高。注意，选择范围边缘处音高的移动不是平行移动。
- 机械音工具 (Kerokero)：使选择范围内的音高直上直下连接，形如正弦方波。

对音高的修改无需使用 NEUTRINO 重新生成，直接按播放键就能听到效果。

颤音工具可以细调选择范围内的颤音深度 (Depth) 与频率 (Frequence) 。调整后，按 <kbd>Enter</kbd> 就能直接将颤音应用到音高上；或者按 <kbd>Esc</kbd> 放弃更改。
这个工具另带一个参数设置。点击工具栏旁边的齿轮，就能调节振幅 (Amplitude) 与频率 (Frequence) 的极值，以及频率的初始值。

![edit vibrato](pitch-vibrato-tool.png)

画笔工具可以直接在钢琴窗上绘制音高。原始音高会以褐色显示。

![draw pitch](pitch-draw-tool.png)

平滑工具可以将音高线变得更为平滑。鼠标向下拖动为平滑，拽得越下平滑度越高；向上拖动为回复原本的曲线。

![make the pitch smoother](pitch-smooth-tool.png)

升降工具可以上下移动音高。选择范围内的音高大部分为平行移动，但在选择范围的边缘处，音高会自动与范围外的音高连接。

![pitch down](pitch-updown-tool.png)

机械音工具可以将音高的连接方式变成直上直下的连接。这种音高线发出的声音会如同机器人般机械。名字来源于青蛙叫声的 SFX。

![make the pitch kerokero](pitch-kerokero-tool.png)

<audio controls="TrackA-kerokero-tool">
  <source type="audio/wav" src="TrackA-kerokero-tool.wav"></source>
  <p>Your browser does not support the audio element.</p>
</audio>

如果在机械音工具后接着再使用平滑工具，能得到非常平滑的音高曲线。再配合颤音工具，就能画出非常漂亮的音高转换。

![a beautiful pitch](pitch-kerokero-+-smooth-tool.png)

### 2:05:20 DYNAMICS 页的操作

这里可以调整音量。实际的音量显示为浅蓝色；原本的音量显示为深蓝色。
（过于简单甚至两句话就带过去了）

![dynamics](dynamics.png)

## 第四部分 调声

> 2:28:13 NEUTRINO 的特点
> 96 州：在 NEUTRINO上，长音的表现更加出色。音符越长，音符的数量越少，唱句表现得就更好（note が長く少ないほうが上手に構成してくれる）。

> 2:37:40 调整 PITCH
> 96 州：Akane 从各方面来说，会比 Aoi 更容易产生断连的感觉（原文シャクリー，是指抽泣或打嗝。但根据他的操作——他当时在画音高——和这句话的背景，感觉更像是音符之间唱得断断续续的意思）

### 2:13:09 修改乐谱的填词，调整有违和感的地方

音高不同的音符合并后，别忘了回 PITCH 页面重新画一下音高线。

2:13:19 めんどうくさいや的调整

Akane 这里唱着像 mendoukusa iya，有停顿感。96 州打算把这个 sa iya 处理一下，变成更平滑的 saiya。
处理方法是把 `さ` `い` 合成一个音符。但这样会带来音高的改变：`い` 原本的音高是 E4，跟 `さ` 一合并，就都变成 G#4 了，跟原来的音高不一样了，后面要去 PITCH 窗口画音高线把音高修正回来。

2:13:47 すき的元音无声化（清化）

这里唱得也有些不连贯。把 `す` 原先所在的音符删掉，拉长前面的 `や`，然后把 `き` 所在的音符的歌词改为 `す’き`。这样发音就从 suki 变成了 ski。

在 NEUTRINO 中，用于执行清化操作的符号为 `’`。
> ・母音脱落記号「’」を用いた場合、母音の音素が省略され子音の音素のみの発音となります。(例:♪いつ’ؙ  ♪か → ♪i ts / k a、♪ス’カ ♪イ → ♪s k a / i)
> ——NEUTRINO《ひらがな・音素一覧表》

从日语发音的角度解释：

> 在日语的普通话中，有几个原因在一定条件下只保留元音的口型和舌位而不发声。这就叫“元音的清化”。它有两个原则：
> ① 5个元音中“い”和“う”容易出现清化，其他则较少；
> ② 夹在“k（か行）”“s（さ行）”“t（た行）”“h（は行）”“p（ぱ行）”的中间元音，或是含有这些行辅音的音节出现在句尾时，其最后的元音容易发生清化的现象。
> ——《新中日交流标准日本语》

这是说话的情况。对唱歌来说，当这些字的时值较短（比如 32 分）时，像“キ”、“ク”这些，就可以直接全部无声化。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">短く瞬間的に発音する「キ・ク・シ・ス・チ・ツ」は母音を消して子音だけ鳴らすと自然に聴こえる<a href="https://twitter.com/hashtag/%E6%96%B0%E4%BA%BA%E3%83%9C%E3%82%AB%E3%83%ADP%E3%81%93%E3%82%8C%E3%81%A0%E3%81%91%E3%81%AF%E3%82%84%E3%81%A3%E3%81%A6%E3%81%8A%E3%81%91?src=hash&amp;ref_src=twsrc%5Etfw">#新人ボカロPこれだけはやっておけ</a></p>&mdash; ノイ (@n0yn0yn0y) <a href="https://twitter.com/n0yn0yn0y/status/1428342860542189572?ref_src=twsrc%5Etfw">August 19, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

2:17:39 其他行的无声化处理

不止 sa 行，其他行也能做无声化处理。
一个稍稍特殊的用法是 `たい`，Akane 经常会出现发 ta i 而不是 tai 的情况（栗田：就像一个个字蹦出来的那种感觉）。把 `たい` 换成たゆ，然后用 `’` 将元音无声化变成 `たゆ’`（tay）。`たゆ’` 的话，听起来就像是省略的 tay，就出来 tai 的感觉了。替换成 ya/yo 同理。
> 栗田：前面的あまがみしないで的ない也可以换成 nay，对吗
> 96 州：对的

2:20:22 きゅう的调整

Akane 在这里的发音很违和。为了更平滑，把 `きゅ` 和 `う` 合并后，直接删掉 `う`，只留 `きゅ` 的音。
毕竟 Akane 在唱きゅう的时候会唱成 kyu u，所以这时候就不需要う来体现长音了；取而代之的是拉长きゅ，用拉长音符的方式体现きゅう的长音效果。
> “きゅう是长音但是 Akane 发不好，所以干脆改成短音然后用拉长音符的方式让她发长一点”
> ——译者语

2:20:56 ちょ与きゅ间的调整

把 `ちょ` 拉长后后面加一个 `っ`。因为 `ちょ` 和 `きゅ` 中间有音符的空白部分，于是就拉长音符填补空白增加流畅度，但是ちょ不是长音所以加了促音符号补上停顿。

2:22:04 つ的调整

这段没什么大问题。但如果其他地方的つ发音有问题的话，可以选择把 `つ` 的元音无声化。这里 96 州建议可以试试与前面的 `に` 接起来变成 `につ’`，可能会更加顺滑（他自己没试过）。
在其他地方的话与后面的 `め` 接起来填成 `つ’め` 也是可以的。但这里如果让他本人来说哪个更好点儿的话，他更倾向于与前面的 `に` 接起来，因为这里的 `め` 正好处在音符的开始位置，填成 `つ’め` 的话感觉就不对了。音符的切入时机也是很重要的。[^1]

2:24:33 えんどれす (endless) 的调整

`え` 和 `ん` 合并。因为是日文歌，所以留着日式英语的发音。`ど` 不做处理也行。要让发音更像英文的话,就用 `えんど’れす’` 。
这里调完后还得去 TIMING 页里细调发音时机。

2:27:09 さいのはあて的调整

`さ` 和 `い` 合并。`は` 和 `あ` 合并后删掉 `あ`。

跟前面さい与きゅう的处理思路差不多。

2:27:30 こんな的调整

直接删掉 `ん`。这里听着太长了，把 `ん` 删去，拉长 `こ`，能够构成更不错的听感。而且因为な里已经有个 `n` 了，这种なにぬねの前还有ん的情况有时听着会有种音太多的感觉，这时把ん删了也是一种调声方法。[^2]

2:29:33 crazy nonsense music 的调整

删掉 `く` 原先的音符，与 `れい` 合到一起；`な` 和 `せ` 分别与后面的 `ん` 合并。`す` 可以选择留着原本的音（因为是日语歌所以留点日语发音也无所谓）。music 的 /zɪk/ 通过 `じく’` 来实现。
く’れい　じ　なん　せん　す　みゅ　じく’

2:30:44

> 栗田：怎么看你（操作）有种抽卡的感觉
> 96 州：是抽卡呢，因为现在这样（操作）虽然是在往正确的路上走，但是究竟正不正确还得等最后渲染合成之后才能知道。

（是抽卡呢（抽卡呢

2:31:05 が的调整

通常来说，想唱得平滑的话，应该把 `が` 拉长后后面加一个 `っ`。但 96 州这里选择放置不动，因为他想突出后边的い长音（Onega-i-)，而不是像平常说话的语调（Onegai）。这是音乐中的表现了，看个人选择。

2:32:08 ける的调整

两个合并然后删掉 `る` 的元音。这里是る的无声化。

### 2:33:09 调整 TIMING，从更细节的地方调整发音

这里基本就看个人喜好，没有什么音乐上的解说了。

### 2:37:40 调整 PITCH

96 州只是做了个很快速的示例（真调起来那这直播就要拖到 4h 了）

---

日语听译与协力

- 第四部分：天才美少女夏木爱琳
- 其他：RichadoWonosas、天堂麻弥

[^1]:我在 CeVIO 里让结月缘丽试了下，接起来后听感的确比较自然了点；`につ’` 的流畅度更高一点，而 `つ’め` 直接卡起来了。*效果因声库而异
[^2]:当然还是得看情况，比如同样也是这句话，把ん删去跟保留ん，结月缘丽唱起来的感觉就完全不一样
