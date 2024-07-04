---
title: 工具软件
date: 2023-10-30 09:15:36
description: 工欲善其事，必先利其器。
---
运行一个路径里有空格的程序：用 `&`
eg. `&"C:\Program Files\Unity\Hub\Editor\2022.3.3c1\Editor\Unity.exe" -quit -projectPath "D:\Project\Plutono"`

[git cheat sheet](https://x.com/b0rk/status/1794057694375993704)
{% img /notes/tools/git-cheat-sheet.jpg 500 git-cheat-sheet %}

## 工具集

- Coz [GitHub - plasma-umass/coz: Coz: Causal Profiling](https://github.com/plasma-umass/coz?continueFlag=e0b2a757ac17556db6c1a53961f7d236)
	- 传统 perf 和 profile 一般告诉你哪里慢了，但是哪里导致慢还得苦苦分析。Coz 通过“虚拟加速代码”不停的探测哪行代码需要去优化，且告诉你优化后整体性能提升多少。据官方分析，通过 Coz 分析，Memcached 性能提高了 9%，SQLite 的性能提高了 25%，并将六个 PARSEC 应用程序的速度提高了 68%，而且这些优化需要修改的代码大都在 10 行以下