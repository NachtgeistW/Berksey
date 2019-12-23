---
title: Perm 算法
category: 
- algorithm
tag: 
- C++
- algorithm
- 排列
- 分治与递归
---

```C++
#include <iostream>
#include <utility>
#include <vector>
#include <algorithm>
#include <functional>
using namespace std;

struct perm {
	int len;
	vector<int> used, position;
	function<void(vector<int>&)> action;
	explicit perm(const int l = 0, function<void(vector<int>&)> fun = [](vector<int>&) {}) : len(l), used(l, -1), position(l), action(
		std::move(fun)) {}
	void run(const int now = -1)
	{
		if (now == len - 1)
		{
			action(position);
			return;
		}
		const int next = now + 1;
		for (int i = 0; i < len; i++)
		{
			if (used[i] == -1)
			{
				used[i] = next;
				position[next] = i;
				run(next);
				used[i] = -1;
			}
		}
	}
};

int main() {
	ios::sync_with_stdio(false), cin.tie(nullptr);
	int len = 3;
	perm p(len, [&](vector<int>& vec)
		{
			for (int i = 0; i < len; i++)
				cout << vec[i] << " ";
			cout << endl;
		});
	p.run();
	return 0;
}

```

> 类模板 std::function 是通用多态函数封装器。  
std::function 的实例能存储、复制及调用任何可调用 (Callable) 目标——函数、 lambda 表达式、 bind 表达式或其他函数对象，还有指向成员函数指针和指向数据成员指针。  
—— cppreference

> 就是一个构造函数两个参数，一个 int 一个回调函数。  
`function<void(vector<int>&)> a = [](vector<int>&) {})` 是回调函数。  
fun 的默认值是一个符合这个 signature 的空函数。  
`std::function<Res(Arg...)>` 就是一个可以保存任何“参数类型是 Arg... 返回类型是 Res 的可以调用的东西的类”。  
`[](vector<int>&){}` 是一个参数为 `vector<int>&` 的空函数。`len(l), used(l, -1), position(l), action(std::move(fun))` 就是构造函数初始值列表。  
这里先值传递 function 再 move 进去是构造函数常用的一种方法。  
——盐酸

