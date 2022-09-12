---
title: HDU 1007 Quoit Design（套圈设计）
date: 2019/12/24
updated: 2019/12/24
category: 
- OJ
tag: 
- C++
- algorithm
- 排列
- 分治与递归
---
## 题目

求点对最近距离。

<!-- more -->

Problem Description

Assume that all the toys are points on a plane. A point is encircled by the ring if the distance between the point and the center of the ring is strictly less than the radius of the ring. If two toys are placed at the same point, the radius of the ring is considered to be 0.

Input

The input consists of several test cases. For each case, the first line contains an integer N (2 <= N <= 100,000), the total number of toys in the field. Then N lines follow, each contains a pair of (x, y) which are the coordinates of a toy. The input is terminated by N = 0.

Output

For each test case, print in one line the radius of the ring required by the Cyberground manager, accurate up to 2 decimal places.

Sample Input

```
2
0 0
1 1
2
1 1
1 1
3
-1.5 0
0 0
0 1.5
0
```

Sample Output

```
0.71
0.00
0.75
```

## 思路

按照《算法导论》，这个算法的时间复杂度应该是 $T(n) = 2T(n/2) + O(n)$，也就是 $O(nlgn)$ 吧。

思路是这样。先定义两个数组 `px` 和 `py`。`px` 里的元素按x坐标单增排序，`py` 里的元素按y坐标单增排序。但是又不能在每个递归里排序，这样时间复杂度就飞到 $T(n) = 2T(n/2) + O(nlgn)$，也就是 $O(nlg^2n)$ 了。那就要做优化。

对于每一次递归，因为每次都会给定左右端点 `left` 和 `right`，所以能把这两点的中点 `mid` 找出来。求 `mid` 左边和右边的最短距离好找，主要是在 $[mid-res, mid+res]$ 里的点的距离难找。这里的 res 是 1 ~ mid 和 mid ~ n 中较小的那个。

>如果说最近点对中的两点都在 1 ~ mid 集合中，或者 mid + 1 ~ n 集合中，则 res 就是最小距离了。但是还有可能的是最近点对中的两点分属这两个集合，所以必须先检测一下这种情况是否会存在，若存在，则把这个最近点对的距离记录下来，去更新 res。这样我们就可以得到最小的距离 res 了。
>
>假设有一个点q，坐标是 $(xq, yq)$。可以证明在以 q 为底边中点，长为 2d，宽为 d 的矩形区域内不会有超过 6 个点（详细证明见下）。利用这个结论我们就可以来继续优化比较的过程了。刚刚我们是用 1 号点去和 2 到 p 号的点求一下距离，我们知道以 1 号点构造图中矩形内，不会有超过 6 个点存在。但是我们又不能直接从 1 号求到 6 号，因为这里的 p 个点是按 y 坐标排序而不是按距离排序的，有可能在 y 坐标上，前 10 个点距离 1 号点都很近，但是前 6 个点的 x 坐标很远，而第 10 个点的 x 坐标和 1 号点的 x 坐标很近，这样第 10 个点到 1 号点的距离反而更近。
>
>那么我们怎么利用这个结论呢？应该这样：假设 1 号点和 2 到 p 号点比较，由于 y 坐标排序的缘故，假设第 p 个点的 y 坐标距离 1 号点的 y 坐标大于当前能求出的最小值，那么这点以及这点后的所有点距离 1 号点的距离必然大于当前已经获得的最小值，所以直接不用比较后面的了。

## 代码

```C++
#include <cmath>
#include <algorithm>
#include <iostream>

#define MAXN 100005
using namespace std;
struct point
{
    double x, y;
};
//py是用来存放符合条件的点的x坐标的
struct point p[MAXN], * px[MAXN], * py[MAXN];

double get_dis(point* p1, point* p2)
{
    return sqrt((p1->x - p2->x) * (p1->x - p2->x) + (p1->y - p2->y) * (p1->y - p2->y));
}

bool compare_x(point* p1, point* p2) { return p1->x < p2->x; }
bool compare_y(point* p1, point* p2) { return p1->y < p2->y; }

//核心代码
double closest(const int left, const int right)
{
    //如果是相邻的两个点
    if (left + 1 == right)
        return get_dis(px[left], px[right]);

    //如果是相隔一个点的两个点
    if (left + 2 == right)
        return min(get_dis(px[left], px[left + 1]),
            min(get_dis(px[left + 1], px[right]),
                get_dis(px[left], px[right])));

    //相隔多于两个点的通常情况
    const int mid = (left + right) / 2;
    double res = min(closest(left, mid), closest(mid + 1, right));//折半递归求解
    int i, count = 0;
    //把x坐标在px[mid].x-ans~px[mid].x+ans范围内的点取出来
    for (i = left; i <= right; i++)
    {
        //如果x在[mid-ans, mid+ans]内
        if (px[i]->x >= px[mid]->x - res && px[i]->x <= px[mid]->x + res)
            //将符合条件的点放到py里
            py[count++] = px[i];
    }
    sort(py, py + count, compare_y);//按y坐标排序
    //遍历py数组找最小的y坐标差
    for (i = 0; i < count; i++)
    {
        for (int j = i + 1; j < count; j++)//py数组中的点是按照y坐标升序的
        {
            //如果光是y坐标的距离就已经比res大了，那就直接跳过去找下一个
            if (py[j]->y - py[i]->y >= res)
                break;
            res = min(res, get_dis(py[i], py[j]));
        }
    }
    return res;
}
int main()
{
    int n;
    while (scanf("%d", &n) != EOF)
    {
        if (n == 0)
            break;
        for (int i = 0; i < n; i++)
        {
            scanf("%lf%lf", &p[i].x, &p[i].y);
            px[i] = &p[i];
        }
        //先按x坐标的大小排好序
        sort(px, px + n, compare_x);
        const double distance = closest(0, n - 1);
        printf("%.2lf\n", distance / 2);
    }
    return 0;
}
```

## 对文中所用结论的证明

> 可以证明在以 q 为底边中点，长为 2d，宽为 d 的矩形区域内不会有超过 6 个点。

以下为《算法导论》证明部分的自译。

假设算法已经找到了 res。接下来要找 $[mid-res, mid+res]$ 里两点间的最小值。

假设在几层递归里，最近的点对是 $p_l\in P_L$ 和 $p_L\in P_R$（$P_L$ 为 $[res/2]$ 的左半边，$P_R$ 为 $[res/2]$ 的右半边)。因此 $p_L$ 和 $p_R$ 之间的距离 $res1$ 必定小于 $right - left$。而且，$p_L$ 和 $p_R$ 垂直方向距离在 $res$ 个单位之内。
。所以就像图 33.11(a) 所示，它们落在以线 $l$ 为中心的 $res \times 2res$ 矩阵内。

接下来将证明在 $res \times 2res$ 矩阵内至多有 **8** 个点。先考虑左边的半个 $res \times res$ 矩阵。由于P_L内的所有点至少相隔 res 个单位，因此此正方形内最多有 4 个点。图 33.11(b) 展示了它们是怎么分布的。同样，右边半个矩阵也是同样的分布。所以一个矩阵内最多有 8 个点（注意，由于线 $l$ 上的点可能位于 $P_L$ 或 $P_R$ 中，因此 $l$ 上最多可能有 4 个点。如果存在两对重合点，使得每一对都包含 $P_L$ 中的一个点和 $P_R$ 中的一个点，则可以达到此限制，一对位于 $l$ 与矩形顶部的交点，另一对位于 $l$ 与矩形底部相交的位置。）。

既然已知“在 $res \times 2res$ 矩阵内至多有 8 个点”，那就知道为什么只需检查数组 `py` 接下来的 7 个点。事实上是 6 个，因为程序是从第 2 个点开始的。
