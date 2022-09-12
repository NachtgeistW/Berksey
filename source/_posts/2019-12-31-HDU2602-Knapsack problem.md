---
title: HDU 2602 Bone Collector——0-1背包问题的多种解法
date: 2019/12/31
updated: 2019/12/31
category: 
- OJ
tag: 
- 动态规划
- 回溯
- 分支限界法
- BFS
- 剪枝
- 0-1背包
- Online Judge
---

**0-1 背包有三种解法你知道吗？**

题目：给定背包的体积 V，N 个骨头的体积和价值，求能装进背包的骨头的最大价值。

<!-- more -->

样例输入

>1
>
>10 10
>
>6 1 0 8 9 0 4 5 9 6
>
>6 9 7 4 3 7 0 8 1 3

样例输出

>30

这道题一看就是简单且非常经典的 0-1 背包问题。

为什么叫做 0-1 背包问题？在选择装入背包的物品时，每种物品只有两种选择：装入（一般记为 true，1）或者不装入（一般记为 false，0）。一个物品既不能多次装入背包，也不能只装它的一部分。是为 0-1 背包。

0-1 背包是 [NP 完全](https://zh.vikipedia.org/viki/NP%E5%AE%8C%E5%85%A8)问题。

## 动态规划

最适合 0-1 背包问题的解法是动态规划。

要使用动态规划解决问题，该问题必须具有最优子结构性质，且子结构具有递归关系（即子问题重叠）。0-1 背包满足上述这两个条件（证明见后文），可以使用动态规划。

形式化描述：给定 $V>0$，$v_i>0$，$p_i>0$ $(1\le i \le n)$，这个问题可以归类为特殊的整数规划问题：

$$
\text{max} \sum_{i=1}^n p_ix_i=
\begin{cases}
\sum_{i=1}^n v_ix_i\le V \\
x_i\in\{0,1\} &1\le i\le n
\end{cases}
$$

动态规划的核心在于找到递推式。设 $m(i,j)$ 是背包容量为 $j$，可选择物品为 $i,i+1,...,n$ 时 0-1 背包问题的最优值，即所给 0-1 背包问题的子问题
$$
\text{max} \sum_{k=i}^n p_kx_k=
\begin{cases}
\sum_{k=i}^n v_kx_k\le j \\
x_k\in\{0,1\} &1\le k\le n
\end{cases}
$$

的最优值为 $m(i,j)$。由 0-1 背包的最优子结构建立递归式如下：
$$
m(i,j)=
\begin{cases}
\text{max} \{m(i+1,j),m(i+1,j)+p_i\} & j \le v_i &\text{(1.1)}\\
m(i+1,j) & 0\le j < v_i &\text{(1.2)}
\end{cases} \tag{1}
$$
$$
m(n,j)
\begin{cases}
p_n & j \ge v_n &\text{(2.1)}\\
0 & 0 \le j < v_n &\text{(2.2)}
\end{cases} \tag{2}
$$

这个递推式很好理解。公式 1 是剩余容量为 j，选择第 i 到第 n-1 件物品时候的递推式。1.1 式是空间还够的时候的情况，二选一，选价值大的装入背包；1.2 式是空间不够的时候的情况，无论剩余物品的价值多大都不能再装了。公式 2 是剩余容量为 j，选择最后一件物品的情况。要么装入背包，要么空间不够，不装。

程序如下。(都不用判断是不是最后一件物品的)

```C++
#include<iostream>
#include<algorithm>
using namespace std;

int main()
{
    int t = 0;
    cin >> t;
    while (t--)
    {
        int n = 0, v = 0;
        cin >> n >> v;
        //体积，价值，背包[价值][体积]
        int arr_v[1005] = { 0 }, arr_p[1005] = { 0 }, dp[1005][1005] = { {0} };
        //注意下标是从 1 开始的
        for (int i = 1; i <= n; i++)
            cin >> arr_p[i];
        for (int i = 1; i <= n; i++)
            cin >> arr_v[i];

        //计算
        for (int i = 1; i <= n; i++)
        {
            for (int j = 0; j <= v; j++)
            {
                //表示第 i 个物品将放入大小为 j 的背包中
                if (arr_v[i] <= j)
                //j - arr_v[i]是当前体积减去第 i 个物体的体积
                //它会取上一行的解来和当前解进行比较
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - arr_v[i]] + arr_p[i]);
                //不够，放不进
                else
                    dp[i][j] = dp[i - 1][j];
            }
        }
        cout << dp[n][v] << '\n';
    }
}
```

## 动态规划的优化

时间就没法优化了。主要是优化空间的。动态规划会将解存储在一张表格中待查。在这道题中，存储背包的最优解用的是一个 1005×1005 的表格，这样实在是很浪费空间。

![dp](https://github.com/NachtgeistW/Berksey/blob/master/_posts/image/2020-01-05%20225026.jpg?raw=true)

先考虑如何实现空间的优化。存在一个循环 `i = 1..n` 计算二维数组 `dp[i][0..v]` 的所有值。那么，如果只用一个数组 `dp[0..v]`，能不能保证第 i 次循环结束后，`dp[j]` 中表示的就是我们定义的状态 `dp[i][j]` 呢？

注意到，求解 `dp[i][0..v]` 时二维数组只用到了 `dp[i-1][0..v]` 这一行。再注意到循环 j 时，dp的改变只依赖于上一行和左侧的状态。这样，只需在每次主循环中以 `j = v..0` 的顺序递推 `dp[j]`，就能保证计算 `dp[j]` 时 `dp[j-c[i]]` 保存的是状态 `dp[i-1][j-arr_v[i]]` 的值。伪代码如下：

```C++
for i = 1..n
    for j = v..0
        dp[j] = max{dp[j],dp[j-v[i]]+p[i]};
```

它依旧是从第一个物体开始计算。不同的是，它从体积上限开始放置物品，然后逐步递减。

**注意：这种解法只能由 v 到 0，不能反过来。如果反过来就会重复放置物品！**

代码如下：

```C++
#include<iostream>
#include<algorithm>
using namespace std;

int main()
{
    int t = 0;
    cin >> t;
    while (t--)
    {
        int n = 0, v = 0;
        int arr_v[1005] = { 0 }, arr_p[1005] = { 0 }, dp[1005] = { 0 };
        cin >> n >> v;
        for (int i = 1; i <= n; i++)
            cin >> arr_p[i];
        for (int i = 1; i <= n; i++)
            cin >> arr_v[i];
        for (int i = 1; i <= n; i++)
            for (int j = v; j >= arr_v[i]; j--)
                dp[j] = max(dp[j], dp[j - arr_v[i]] + arr_p[i]);
        cout << dp[v] << '\n';
    }
}
```

## 回溯法

因为对于每个物品无非就“装”与“不装”两种选择，所以这个问题的解空间树就是一棵子集树。

![子集树](https://images0.cnblogs.com/blog/594991/201506/041055090666128.png)

在搜索解空间树时，只要其左结点是可行的，就进入左子树搜索。只有右子树包含可行解时才进入右子树搜索，否则就剪掉右子树。设 `r` 为当前剩余物品的价值的总和，`cur_p` 为当前物品的价值，`best_p` 是当前最优价值。当 $r+cur\_p\le best\_p$ 时，就可以剪去右子树。

为了更好地计算上界，可以直接将物品的单位价值计算出来（这道题是单位体积价值），从大到小排序，然后按顺序考察。当需要计算上界的时候，将背包的剩余空间按物品的单位体积取出来，全部装入背包；如果还有剩物品，则把不能全装进去的下一个物品“假装”装一部分进去，把背包填满，以此计算上界。

下面是这道题的回溯法解法。由于 $N <= 1000 , V <= 1000$，所以一定会 TLE。

```C++
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
//T cases, the number of bones, the volume of his bag, best price, current price, current volume
int t = 0, n = 0, v = 0, best_p = 0, cur_p = 0, cur_v = 0;
struct bone
{
    int v;//体积
    int p;//价值
    double pv;//单位体积价值
};
bool sort_alg(const bone a, const bone b) { return a.pv > b.pv; }

//计算上界
int bound(vector<bone> vec, unsigned int i)
{
    int v_left = v - cur_v;//剩余容量
    int p = cur_p;//当前价值
    //按剩余价值从大到小的顺序把背包装满
    vhile (i <= vec.size() - 1 && vec[i].v <= v_left)
    {
        v_left -= vec[i].v;
        p += vec[i].p;
        i++;
    }
    //如果还有剩物品，则把不能全装进去的下一个物品“假装”装一部分进去，以此计算上界
    if (i <= vec.size() - 1)
        p += vec[i].p * v_left / vec[i].v;
    return p;
}

void backtrack(vector<bone> vec, const unsigned int i)
{
    if (i > vec.size() - 1)
    {
        best_p = cur_p;
        return;
    }
    //搜索左子树
    if (cur_v + vec[i].v <= v)
    {
        cur_p += vec[i].p;
        cur_v += vec[i].v;
        backtrack(vec, i + 1);//喜闻乐见的递归回溯搜索
        cur_p -= vec[i].p;
        cur_v -= vec[i].v;
    }
    //根据上界判断要不要搜索右子树
    if (bound(vec, i + 1) > best_p)
    {
        backtrack(vec, i + 1);
    }
}

int main()
{
    ios::sync_vith_stdio(false);
    cin >> t;
    int k = 1;
    vhile (k <= t)
    {
        //初始化
        cin >> n >> v;
        int temp_p, temp_v;
        vector<int>vec_v = { INT_MAX }, vec_p = { INT_MAX };
        vector<bone>vec;
        bone b;
        b.p = b.v = INT_MAX;
        b.pv = DBL_MAX;
        vec.push_back(b);
        //价值。注意下标是从 1 开始的
        for (int i = 1; i <= n; i++)
        {
            cin >> temp_p;
            vec_p.push_back(temp_p);
        }
        //体积
        for (int i = 1; i <= n; i++)
        {
            cin >> temp_v;
            vec_v.push_back(temp_v);
        }
        for (int i = 1; i <= n; i++)
        {
            if (vec_v[i] == 0) cur_p += vec_p[i];
            else
            {
                b.p = vec_p[i];
                b.v = vec_v[i];
                b.pv = (double)vec_p[i] / vec_v[i];
                vec.push_back(b);
            }
        }

        //求解
        std::sort(vec.begin(), vec.end(), sort_alg);
        if(v == 0) std::cout << cur_p;
        else {
            backtrack(vec, 1);
            std::cout << best_p;
        }
        if (k < n) std::cout << '\n';

        //归0
        best_p = 0, cur_p = 0, cur_v = 0;
        k++;
    }
}
```

## 参考资料

[HDU 2602 Bone Collector（01背包）](https://www.cnblogs.com/Su-Blog/archive/2012/08/28/2659872.html)
