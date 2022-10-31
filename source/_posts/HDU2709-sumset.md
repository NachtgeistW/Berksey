---
title: HDU2709 Sumsets（和集）
date: 2019/12/3
updated: 2019/12/3
category: 
- algorithm
tag: 
- algorithm
- 动态规划
- 递推
- HDU
---

题目：给出一个整数 n，将其分解成 $2^i$ 相加的形式，求共有多少种分法。
<!-- more -->
例：7：

1) 1+1+1+1+1+1+1

2) 1+1+1+1+1+2

3) 1+1+1+2+2

4) 1+1+1+4

5) 1+2+2+2

6) 1+2+4

Input

A single line with a single integer, N.

Output

The number of ways to represent N as the indicated sum. Due to the potential huge size of this number, print only last 9 digits (in base 10 representation).

Sample Input

7

Sample Output

6

## 思路

找到前面几个数：1 2 2 4 4 6 6 10 10 14 14 20 20 26 26 36 36 46 46 60 60 74 74 94 94 114 114…

用一个数组 `a[]` 来存储方案数量。观察到，偶数的分解方式里，n 拆开以后要是有 1 肯定至少有两个 1，所以 $n - 1 - 1$ 即为 $n - 2$，有 $a[n - 2]$ 种解法；没有 1 的话，就是对 $a[\frac{n}{2}]$ 的方案数中的每一个数乘以 2, 有 $a[\frac{n}{2}]$ 的方案数。所以 $a[n] = a[n - 2] + [\frac{n}{2}]$。

而除 1 外，其他的数字里，奇数的方案数量都与它前面的偶数一致，即 $a[n] = a[n - 1]$。

题目要求保留末尾 9 位数字，所以求出来之后要取 1,000,000,000 的余数。

## 代码

```C++
#include <iostream>
using namespace std;

int a[1000001];
int main()
{
    ios::sync_with_stdio(false);
    a[1] = 1;
    a[2] = 2;
    int i = 3;
    while (i <= 1000000)
    {
        a[i] = a[i - 1];
        i++;
        a[i] = (a[i - 2] + a[i >> 1]) % 1000000000;
        i++;
    }
    int n;
    while(cin >> n)
        cout << a[n] << '\n';
    return 0;
}
```

算是 DP？有人说是简单的计数 DP，不过我也不太确定。
