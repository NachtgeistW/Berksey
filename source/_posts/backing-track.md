---
title: 背景音频
date: 2021/10/12
updated: 2021/10/12
category: 
- GameDev
- 译文
tag: 
- 译文
- 音游
- Unity3D
---

在这一大节里我们要讨论的是背景音频的播放问题（剧透：这远不是一个简单的 `AudioSource.Play` 就搞定得了的）。这个看似简单的任务会成为一个超绝麻烦的东西，而数学在它面前看上去就像个简单的加减法。希望我能把这些东西讲明白（希望吧）。

<!-- more -->
---

翻译&校对：夜轮

1. 本文译自[《Backing track》](https://exceed7.com/native-audio/rhythm-game-crash-course/backing-track.html)，侵删；
2. Native Audio 是一款 Unity 插件，用于处理依赖即时反馈的音频（如 hitsound 和 key 音，文中的称呼是“响应型音频” (the response sound)）。本文来源于该插件的官方网站；
3. 限于译者水平，文中难免有错漏之处。有错请指出。

---

## 你的 receptor 在「时间」上

receptor 是向前移动的判定线，或者反过来，它不动，音符会落到它上面（由你决定）。在本文中，我假设 receptor 以**时间**为单位，随每帧的 delta time （时间增量）不断增加。出于这点，假设该 receptor 时间就是它的“位置”，这样就够了，不需要别的用来转换时间的位置了。这么做的好处是 delta time 可以直接加入到整个游戏的进程中。我不知道你的游戏是怎么表示玩家现在在曲目中的“位置”的，所以请自由地将我的定义转为你游戏里的对应概念，而不是直接全改成我说的单位。

它用于：

- 判定：在判定帧上，你可能需要比较当前的 receptor 时间和每个涉及到的 note 的时间，确定你需不需要给出 note 的分数和判定。
- 渲染：举例来说，如果你的 receptor 在 1.5s 的位置，而 note 在 2.25s。你可以写出类似“当 receptor 的时间为 2s 时，note 会变得更近”的渲染逻辑。（在“note 落到 receptor”类的音游里你感受到的大概就是这样，不过实际上是 receptor 向前走了）

我们并没有要求返回音频位置以应用到这个时间。（There is no asking audio position back to apply to this time. ）它完全基于 delta 时间。要求返回音频时间是不可靠的，你已经读过了：我们从扬声器里听到的东西很可能比这个音频 DSP 时间要早；而且音频 DSP 时间以一个奇怪的步骤更新，甚至在接下来的几行代码中就会改变，或者不变。

一个更好的做法是只在一个关键点上（也就是我们开始音乐的那一刻）确定游戏与这个怪异的音频时间保持同步，然后不再理它。如果缓冲区超限导致音频滞后，或不足导致游戏滞后、音频提前，那就这样吧。这总比将你的整个游戏建立在音频时间上好！让你的游戏基于 delta 时间运行。

## 你的任务：从「任何」receptor 时间开始游戏

显而易见，音乐是从头开始播放的。但别忘了，游戏能随时停止，然后继续。更进一步：谱面编辑器，能随时随地从任何地方开始“测试谱面”。“背景音频的播放”应该能处理「任何」的 receptor 位置。正常开始游戏只是意味着将 receptor 移动到负数的时间，然后启动我们的魔法函数——我们现在就来探究如何编写它。

## `PlayScheduled`：尊重这个方法

Unity 内置的 `PlayScheduled` 能做到一件连 Native Audio 都**做不到**的事情：精度。（不是延迟，也不是立即响应）你指定一个未来的时刻，它就会努力让音频从那个时刻开始播放（这跟你想指定任何时间不冲突）。

任何你能预测播放时间的音频（背景音频、节拍器等）都应该使用这个方法播放。

这个方法很特别，因为它是**独立于帧**的。如果你指定的时间恰好落在两帧之间，音频还是会在那个时间播放。这是什么魔法？帧是由 Unity 添加的一个抽象概念。通常在本地端，有更严格的回调或与帧无关的其他东西。（Usually at native side there is a tighter callback or something out of frame.）（如果你折腾过单片机或者 Arduino 之类的东西，你可能听说过“中断”这个东西。它可以让 CPU 立刻处理某个事件。）Unity 有一个特定于平台的代码，用于与本机音频调度方式交互。这个方法远比它看上去的更有用。

你写了个在 update 函数中等待在正确的时间播放的解决方案。naive。它**总是比你真——正想要的时间晚**，因为没有办法确保帧的时间正好落在那个点上。你可以假设在最佳情况下下一帧会比当前时间提前1/60到达。你能做到的最多也就这样了。

现在又加了一个问题：这个时间是未来的时间。它会怎么影响我们呢？

## 现在假设延迟是 0

为了不被绕晕，让我们假设音乐在 `PlayScheduled` 指定的时间立即、可靠地播放。

## 修正多出来的时间 (over-time)

让我们考虑一个简单的情况。你的 receptor 在 0s 的位置。你想从这里开始。你现在想听到的理论上是音频的哪个 bit 呢？

是音频文件的 0s！这就是说，如果下一帧将 receptor 的位置移到了 16.66ms，那时你想听到的应该是音频的第 16.66ms。假设歌曲从 receptor 的 0s 开始（没有偏移的情况下，偏移稍后再谈）。

但等下，我们要怎么在这帧就听到这个文件第 0s 的音频呢，我们只是收到了播放的命令 + 定了个“必须在未来播放”的规定啊？

考虑一下这个“战略”：

- 将音频时间设为 0s。
- 给音频安排从现在开始，于 16.66*2 ms 后（这是设定的未来时间）开始播放的计划（我觉得 2 帧够游戏准备音频了）。记住是以 DSP time 作为时间单位的。比如说，在运行这行代码的时候你询问 DSP time，它回复你说是 20000 ms。那你的“期望时间”就是 20033 ms。同时 `PlayScheduled` 接受的时间来自 DSP 时间表，所以你告诉它的值也是 20033 ms。
- 别忘了开始你的游戏！按计划游戏将在第 20033 ms 开始。
- 经历两次更新周期后，你查了一下 DSP time，它的值是：
  - 20033 ms（基本不可能）：运气真好！你现在听到的就是音频的 0s，receptor 也正好指在 0s 上。这一帧里你**不用**再给 receptor 加 delta time 了，因为它就是对的。下一帧你再来给 receptor 加独立于音频时间的 delta time，希望音频时间还会跟你加完 delta time 后的 receptor 的时间“相距虽远心相连”。至少，你开了个好头！
  - 20018 ms（别啊）：之前规划好的音频计划还没到点。但下一帧可能就开始了，然后代码就超过那个时间了！（就随便说个 20041 ms 吧）不过，因为安排好的音频还没开始播放，所以你还有最后一次机会，在它开始播放前运行任何代码。毫无疑问，你可以选择用 `StopScheduled` 终止计划然后重新安排时间。嘛这可能会导致无尽循环。所以我还是建议你就让这帧过去，下帧再说。
  - 20041 ms（别啊）：音频已经开始了，没法停了！解决方法是什么？对这帧来说，**自行加一个 delta time 给它**，这样它就能把多出来的时间填上（这里是 20041 - 20033 = 8ms）。记住，如果 DSP time 是准的（就第一种情况），在这帧里我们就**不**往 receptor 时间上添 delta time 了，毕竟它本来就是对的。意思就是说，在这帧里你的游戏会稍稍往前移一点，下一帧里我们就可以抱着同样的期望，像往常一样继续往上添 ~1/60s 的 delta time 了，就如我在第一种情况里解释的一般。

通过这个“战略”，就可以通过**修正多出来的时间**来规避“计划”仅适用于音频的事实。因为并没有“`ExecuteCodeScheduled`”这种东西，所以你想什么时候开始播放就在那个点开始吧。你可以骗一下你的方式，因为 receptor 也是**以时间为单位**的，当游戏循环给了你机会时，你强迫它向前移动，以配合这个机会。它是你唯一的机会，毕竟是你自己对终极方法 `PlayScheduled` 说的“20033 ms！”，虽然它现在稍微往前偏了一点，不过你知道偏得有多前。我把它叫做**现在！时刻**。

所有未来的帧都是 delta 时间和 DSP 时间的命数了。（而玩家的耳朵会听出来有没有漂移，如果玩家不试图 P 歌的话就更好了。）

## 那么从暂停中回到游戏呢

当按下暂停时，你并不只是暂停了 `AudioSource`，你暂停得十分彻底。在玩家按下继续的那一帧，我们要把我说的东西全部重做一遍，除非当玩家想重新开始时，你得事先定一下当前 receptor 位置上的音频播放位置 (play head time) 。

## 那么在歌曲之前开始呢

音频可能是从第 0s 的 receptor 时间开始的，但你给你的炫酷 UI 做了个入场动画，加了个 READY? 单词，当屏幕展示这个 READY? 时，在它后面，note 也落了下来（空的游玩区纯往下滚也行）。

很简单，只要根据你动画的时长，把 receptor 时间设成负的就行了。但是，有件事不太一样：当你从暂停中回到游戏时，你的音频时间已经定好了。现在，你**没法**把音频时间定成负的了！最小值少说也是 0s。

没错！取而代之的，你必须将这个“少掉的时间 (under-time)”**加到规划**方法 `PlayScheduled` 里，然后将音频拨到 0s——它能达到的最小值。

这就是说，现在的计划大大往前偏了。现在！时刻也大大往前偏了。游戏仍旧每帧都检查 DSP 时间……最后，应该在某一帧，返回的 DSP 时间稍稍超过了我们计划的时间。

不同之处在于，在此之前我们就为了让游戏在展示 READY? 的时候往前进行，**一直**在往上添 delta time 了。在你查出 DSP 大于你计划的 DSP 时间的这一帧，你最好就不要往你的 receptor 位置上加 delta time 了，而是将一个与你计划播放音频的 DSP 时间加上差值后**设成**你的 receptor 位置。设置意味着，不管原先的数值是多少（应该跟真实值蛮近的），它都会将其改写成你想要的值。

这会产生一些副作用：设备不好但眼尖的玩家会注意到，在歌曲开始播放的现在！时刻，游戏在这个点的前几帧会稍微往前或往后卡一下。如果这个点附近没有 note 的话就没什么影响。不过大部分歌曲在音频刚开始播放的时候，屏幕上都没什么 note。我觉得保证与音频的同步比在 receptor 上动点手脚更值。

现在所有情况都解决了！即使在游戏还说着 READY? 的时候你也能暂停并继续了，它会以恰当的方式等待音频真正到来的时刻，因为你已经教了它很多了。

## 偏移 (Offset)

上一节里，假设当 receptor 时间是 1s 的时候，你想听到的音频也是第 1s。

但情况不总是这样。在音频文件的开头经常会有一段无声段或者 intro 段，你要么把它往前挪一点要么往后挪，这样你的第一个音才会对上你放在第 2s 上的 note（冷知识：这是 4/4 拍 120 BPM 歌曲的第二小节，因为一拍需要 0.5s）。这就是每个音频文件的偏移，也有可能每张谱都适用。

所以，在决定应该做什么前，你只管加上这个时间就行。比如说某种情况下，歌曲从 0s 处精确地开始了，可能玩家在开始后 0.5s 左右的位置暂停了游戏（比如说被蚊子咬了），但这首歌设的偏移是 4s。那么，情况就变成了“在歌曲之前开始”，你就得跟前面说的那样，将少掉的时间 (under-time) 加到计划中。

负偏移也是如此。如果一首歌的偏移量是负的，那么即使在 0s，你也会听到**这歌的一小段**了。

偏移通过将歌曲往前或往后移动的方式，决定了“在这个 receptor 时间上，我们应该听到歌曲的哪一部分？”。正负偏移都会起效。也许你真的需要这首歌的 5s 才能到达 0 小节，这种情况下你得把歌往前移。（你也可以改一下谱面，这样 note 开始的时间就不是 0 小节，会稍微晚一点了。）

你得保证你“为了播放动画和 READY? 把时间拉到负数”的行为也把这个偏移算进去了。也就是说，起始时间的最小值不是 0s，而是偏移量。否则，一首真的把偏移拉得很大的歌即使显示完 READY? 后还会有一段空白段，真正开始的时间就太长了。[^note1]

## 玩家可调整的“校准”

有时它叫“输入校准 (input calibration)”。有时它叫“音频校准 (audio calibration)”。也有叫“调整延迟 (adjust latency)”的？

### 抵消延迟

请先认同一点：**负延迟完全没用**。延迟说白了就是音频比预期播放晚了。因此如果你说请把延迟调到 0.5s，那么所有的东西**相对于原定时间都会往前提**。因为我们制定的计划未来规则，本来它们就已经提前一点了，现在还会**再往前提** 0.5s，这样就能把延迟拉回预定的时间。

所以说“我想要 -0.5s 的延迟！”是没用的。

当从暂停中回到游戏时（这个情况已经考虑过了），你应该记得其中几帧里我们是不会动 receptor 时间的，因为我们在等现在！时刻。现在你在等来 DSP 时间超过指定的现在！时刻然后修好多出来的时间，让游戏继续后，必须**还得等**。想简单点，除了那些数字以外再添一个延迟补偿上去。（但是不要给准备赋给 `PlayScheduled` 的计划时间加，因为这样一来游戏就会在你定好的时间外再等一段时间了。）

在之前提到的例子中，我们等的是 20033ms。你在 DSP 时间为 20035ms 的时候迎来了这一帧。但你把延迟定成了 10ms。因此即使你知道音频已经如期播放了，这一帧现在也不是给 receptor 时间添加 delta 时间的正确时间，取而代之的是 DSP 时间读到 20055ms 的那帧，那时你就要执行将多出来的时间（overtime）从 20055 修正到 20045（而不是 20035）的任务了。

一个在修正过程中可能存在的问题是情况变极端了。想象一下玩家调延迟调得太过火，远超出他们需要的延迟的情况。当他们从暂停中回到游戏时，他们将看到“游戏未动，曲子先行”的情景！你可以多看几眼，因为它真的很怪。而玩家的目标是把延迟调到“东西动起来的同时歌曲也精确地放出来”的样子。

让 receptor 在游戏暂停时停下来解决了前面的那个问题，不过副产品就是它了。我们有办法解决这个问题吗？

- 添加一个继续的倒计时就能解决这个问题，只要你确定它能长过允许玩家调整的延迟上限的话（比如说把延迟上限锁定为：3s）。这样一来，这个固定的等待过程看上去就很自然了，因为你把它融进了倒计时里（而且真正数到 0 之前就能迎来预定计划了）。
- 把 receptor 往后移以补偿延迟。但是，这会允许玩家为了将 receptor 往后移而滥用反复暂停/继续的行为。有些游戏将其作为玩家的 QoL，比如 DJMax Respect，它不仅加了个倒计时，还能帮你把时间往回倒。你可以通过倒带屏蔽掉延迟。
- 游戏继续后就这么让 receptor 往前走。但是你可以放一小段无声的曲子！为什么？因为这段无声的曲子其实是留出我们的烂 Android 机将音乐灌到我们耳朵里的时间。计划已经到点了，但它还是没声。因此当音频终于也回来的时候，歌曲的那段也应在你按下暂停的那段后面[^note2]，因为你为了在补偿延迟的时候不会弄出奇怪的卡顿，已经用它们“支付”过了。你要做的是将音频位置故意调整到待补偿延迟更后面的位置，然后用 `PlayScheduled` 准确地回到游戏。（换句话说，每次暂停再继续后，为了在无声段时相遇，音频为了跳到后面而做了调整，因此停顿点之前的那一小段就再也听不到了。）在这种情况下，即使曲子在校准出错的情况下播放也不会有诡异的暂停了，不过调过头的延迟会让歌曲的无声段放得长一点。

我推荐最后一种方法，因为玩家大都倾向于在暂停后专注于移动 note（目押读谱而不是音押），这可能比在 note 还没有移动时音频就出人意料地回来了，或者让 receptor 自己把位置拉到不同的地方更好。

## 如何实现无声播放的方案

当你决定从**任何** receptor 时间开始播放后，请确定你应该听到的**不是现在**，而是在未来很长一段时间内的校准时间。

如果你的 receptor 在第 2000ms，你的延迟补偿是 0s，偏移是 500ms，你一般会说我现在想听到的是 1500ms。但取而代之的，你设置的音频等待时间不是 1500ms，而是 1533.33ms。**（核心的不同点就在这里了）**

接下来，试着询问帧中最早的“DSP now”，它与我们将要处理的 delta 时间最匹配（别忘了每次我们询问 DSP 时间的时候它都会变）。把 1533.33ms 音频安排到 DSP now + 33.33ms 开始播放。这一帧对 receptor 时间**不做任何处理**。这个“不做任何处理”只会在我们试图询问足够靠近真实帧时间的帧内最早的 DSP 时间时起效，所以我们已经做好在下一帧**做些什么**的准备了，同时期待着 delta 时间也和 DSP 时间携手往前。

下一帧，往 receptor 上加 delta 时间。（那意味着我们已经玩着游戏，打开判定和所有东西了。）回顾一下，我们检查 DSP 时间，直到它超过了 DSP + 33.33ms，来开工修正多的时间，但因为在这个方法下音频往前移了，所以我们现在就能马上移动了。在这帧里，它可能在 1516.66ms 左右，还**没到**计划的时间。（33.33ms 大约是 2 帧）。但我们已经在“无声播放”了！（即使没声音你也能击中或错过 note。）接着，当计划时间终于到来的时候，它占的位置是我们已经无声播放的位置。那个 33.33ms 完全消失了，就如它们在无声段以 0 音量播放，突然之间音量就恢复一般。

现在我们试一下往这个已经够乱的场景里再添一个 100ms 的延迟补偿。（别忘了负延迟完全没用）把音频时间设为 1**6**33.33ms，但**仍**给它预定一个在 DSP + 33.33ms 处开始播放的计划（跟之前说的一样，33.33ms 是留给 `PlaySheduled` 准备的时间）。现在，当到达计划的原定时间点时，因为 Android 的破延迟，音频来晚了，我们在没有声音的情况下放歌放得比以前还久了一点，但当声音终于放出来后你再看下，现在的位置并不是 1533.33ms 而是 1633.33ms。没错，因为延迟问题音频确实比计划中的来晚了，但为了应对这情况，音频位置早就往前挪了。計画通り。

这里顺带说一个很重要的点。不管是什么数（数 = 延迟补偿，receptor 时间，偏移），预定时间似乎都应该是 **fixed** DSP 时间 + 33.33ms。因为我们把其他所有的问题都转变成了这个往前走的音频时间（并扛下静音段）了，预定所需的似乎只有为了让 `PlaySheduled` 生效而必不可少的未来时间。（请告诉我你看到这里的时候点头说你看懂了，不然的话请你喝点葡萄糖然后试着把这篇文章再读一遍吧。）等下我就会把它对但不完全对的地方给你演一遍（哈哈），不过注意到它其实挺有用的。

附加内容：在计划预定中的那个时刻，我们已经让 receptor 跑起来了，这个你已经注意到了。但你还是有机会混一步“修正多出来的时间”，方法是一直请求 DSP 时间，求到 DSP 时间超过了预定时间，然后就能跟之前一样挪一下 receptor 时间了。（不同之处在于：在这一时刻之前我们已经在把 receptor 时间往前挪了，反过来说是在等待转发的音频时间；在这一挪的时刻，因为延迟，我们还什么都没听到。）如果你比较喜欢让 receptor 顺滑地往前走，或者担心玩家发现 receptor 时间被改写掉了（会影响渲染），你可以选择跳过这一步。与之前的操作的不同之处在于，之前我们并没有让 receptor 跑起来，可以悄无声息地掩盖“修正”的过程。现在你该做个交易了：你会只在这一个确切的时候修正它吗？交易的连续性由设备的破烂程度决定。如果你不去修的话，烂手机确实也能在预定时间顺畅运行，不过因为你不去修，游戏时间之后就会漂移了。

让我们考虑一下校正结果说“我想要听到 *负数* ms”的情况。假如 receptor 现在在 200ms，但偏移量是 500ms！结果就是现在我想听到的音频时间是 -300ms，一个毫无疑问并没有用的数字。真实情况是音频往还未到来的时间偏移得太多了，偏移到甚至连 200ms 都不是音频的开始；或者反过来，receptor 的时间往负的时间靠太多了。所以再说一遍，不要在音频开始时计算所有偏移量。

如果我们继续算下去的话，我们要把音频的等待时间设成 -300ms + 33.33 + 延迟。它还是负的……或许只要把延迟设得很大的话它就不是负的了？我的观点是，负值必须从 `receptor time - offset + 33.33 (minimum future time) + latency` 里算出来。如果你用这个公式算出了正数，那你就可以用以前的做法了。问题在于时间是负数的时候是没法等待音频时间的。它必须去别的地方。

这个“别的地方”就是我先前让你记住的“对但不完全对”的东西。预定时间已经修正为 DSP now + 33.33ms 了！你还是能假设你在音频前面，对吗？那件你还要做的事情就是，把这个负的时间也加到预定计划中。预定时间现在就变成了 `DSP Now + 33.33ms + (negative time * -1)`。没错，你把它拽到这里并马上把它加到了计划中。这样一来，准备好的音频时间就能在 0s 等待，而不是无理的负数时间了。还有个问题，当那个值是正数的时候，`DSP now + 33.33ms` 就无值可加了。（因为它们变成了准备好的音频时间。）你用一个 `if` 来分别处理负数时间和正数时间就行了，别试着过度概括事物而把自己脑袋烧了。

我相信你已经写好了一个算法，`无论是从哪个` receptor 时间开始播放音频——正数的，负数的，歌曲之前的，曲子中间的，甚至曲子放完后的——它都能应付！（曲子放完后的时间里，因为预准备时间在最后面，你也没把 `AudioSource` 设成循环播放，所以你什么都听不到。）恭喜！

以防万一，当从暂停恢复时，我们得放一些延后 (delay) 时间，以防止玩家在还未到计划时间时就又按了暂停按钮，不然的话你就有可能在暂停页面里听到歌曲了。或者你可能要花点力气来取消这个计划。

## 其他种类的“校正”

还没完呢！还有一点！

在许多游戏里，当你进入设置页的时候，你会看到它允许你按 +- 号加减某个未知单元里的值来“校准”游戏。“我把校准值设为 -0.5/0.5”好像都起效了。发生什么了？不是说好了负延迟完全没用吗？

游戏没有直截了当地说出校准单位是什么，或者只是在让你按下 +- 的同时向玩家显示一个移动条的游戏测试区，都是有原因的，因为这个话题真的很烧脑。(你看光是为了放一首破歌，这个页面就已经变得多长了。）

一是，游戏可能把这个值加/减到了音乐的**偏移**上，但有时它会给人一种延迟不会变的感觉。所以正值负值都是有意义的。只用了这个操作没做其他技巧的游戏中，游戏可能只有在从头重新启动时才会出现补偿。可能游戏试着把正数校准值转换成了我们一直在折腾的延迟补偿，把负数校准值转换成了偏移量。

当玩家不相信开发者给出的延迟的时候，调整歌曲的延迟很有用；一些玩家说“这歌早了”，一些玩家说“这歌晚了”的时候也有用。此外，当你的偏移出错时，提供偏移校准或许是一张不错的安全网。

二是，它也有可能是 **输入/判定** 校准值。它们偏移会导致即使 receptor 时间就在你判定输入那一帧所在的 note 时间上，你也得不到 perfect。这个校准值用来帮助在输入延迟上表现不佳的设备。玩家肯定有一双完美眼睛，所以他会试着在 note 真正与 receptor 重叠的时候触摸屏幕。(就 receptor 时间与 note 时间相同的那一帧，别忘了 receptor 时间是如何影响渲染的）。但当然，输入并不会在这一确切的帧内到达。它可能会晚一帧甚至好几帧。如果你有很好的眼力，那么你会看到音符从 receptor 上移过，因为输入需要时间到达，在某一帧之前，你终于可以检测到输入并使音符消失。不过嘛，如果你按“在 note 与 receptor 重叠的那一帧上才摸得到”设计游戏，那就总是**太晚了**。虽然我认为这是正确的设计——因为这样说比解释“嘛，但你的输入其实是后来才到达代码的lol”要容易得多——但你不能让 note 在手指肉碰到屏幕的那一帧消失。它们不可避免地会移动一下。

输入延迟为 0s 的设备是不存在的。所以你应该在默认情况下进行一些输入校准，这样的话，如果你想给那些触摸到与 receptor 重叠的 note 的玩家打满分，那么 receptor 下的 note 才是真正的 perfect。这就使得游戏以玩家为中心（优先考虑玩家看到的视觉）而不是以代码为中心。(代码才不关心输入需要多长时间到达。)

否则游戏就会变成玩家之间老生常谈的“嘿，在这个游戏中，你需要在那一行之前触摸一下，游戏就会给你个 perfect。”，这也很好，大概吧！因为玩家会明白，设备没有同样的性能，他没钱买在输入延迟上表现更好的设备是他的错，也会明白为什么他需要提前触摸。然后他就会一直这么干。

或者,你也可以通过设置一些默认的输入校准来平衡它，因为你知道输入延迟为 0s 的设备是不存在的。但有些手机仍然需要提前打，因为它们很便宜，你不想把输入校准包含进去。另外，延迟、音乐偏移和输入这三种校准，一口气全提供给玩家的话，有时就显得太多了，也难以理解。

## 结尾

终于啊！作为对忍受我这么久的奖励，这一部分实际上是对我自己的一个巨大的小黄鸭调试。如果我不把这些东西都打出来，我就永远无法超越我刚才给你介绍的思路中的前几段。就是这么……管用。

抱歉最后几个 topics 完全推翻了之前的推理，但如果不先弄清楚前面的东西，我也没法搞明白最后的那个解决方案了。完结！！撒花！！

## 注释

[^note1]: 译者注：如果你对这个没什么概念的话，你可以看一下 PJSK，它给每首歌都设定了 9s 的 offset。游戏从封面从正变斜的动画就开始了，但很多歌直到轨道完全出现后还有一大段空白段，如果开 AUTO 的话够右下角的 AUTO 闪一次到一次半。
[^note2]: 译者注：虽然原文写的是 ahead，但根据中文表述翻成了“后”。这一段的“后”都指的是时间上靠后的位置。比如说暂停的时间是2:33，那继续时就该把音频从2:33往后的地方开始放。（原文：Therefore when the music finally come back, that section of song should also be ahead of the section at the time you paused because you "pay" them for latency compensation without weird stop.）
