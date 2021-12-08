---
title: "Codeforces#620 (Div.2)"
categories: [ "ACM" ]
tags: [ "Codeforces" ]
draft: false
slug: "30"
date: "2020-02-19 01:02:00"
---

## A. [Two Rabbits](https://codeforces.com/contest/1304/problem/A)

### 题意

​	&nbsp;&nbsp;&nbsp;两个兔子分别位于 $(x,0)$ 和 $(y,0)$  ，两个人对头蹦，前者往前蹦 $a$ ，后者往前蹦 $b$ ，问两人是否能恰好相遇。


<!--more-->


### 思路

​	&nbsp;&nbsp;&nbsp;算一下两个人直接得距离 $s$ ，每次两者距离减少 $a+b$ ，看 $s$ 是否能被 $a+b$ 整除，如果可以就能够相遇。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

int t;
int x,y,a,b;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d%d%d%d",&x,&y,&a,&b);
		int p=a+b;
		int f=y-x;
		if(f%p==0)
		{
			printf("%d\n",f/p);
		}
		else printf("-1\n");
	}
	return 0;
}
```

</br>

## B. [Longest Palindrome](https://codeforces.com/contest/1304/problem/B)

### 题意

​	&nbsp;&nbsp;&nbsp;给出 $n$ 段长度为 $m$ 的字符串，从中挑选出一些组成最长的回文字串，输出这个回文子串的长度和内容，如果有多种情况输出一种即可。如果没有符合的，就输出0 。$(1\le n\le 100,1\le m\le 50)$

### 思路

​	&nbsp;&nbsp;&nbsp;因为这个数据规模非常小，所以 $O(n^2m)$ 也是可以接受的（说实话我不是很会算，大概是个差不多的式子），那么我们可以选择暴力匹配，用每个字符串去匹配后面的，如果是两个互为回文串，那么就把其中任何一个计入到sub字符串中，然后用一个 vis 数组来标记他们两个已经被使用过了，最终一个字符串匹配完后面所有的发现没有合适的，那么就考虑他自己是不是一个回文串，如果是一个回文串，单独标记它是放在中间。最后我们的sub是存放了一半回文串。
​	&nbsp;&nbsp;&nbsp;统计答案的时候，先将sub加到答案ans中，检查一下是否中间有合适的回文串，如果有的话也加到ans里面，最后讲sub逆序一下，加到ans里面。最终输出答案的时候，看一下ans是不是空串，如果是空串，就输出0，否则输出长度和ans。（string是真的好用！！！）

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=105;
int n,m;
string ans,sub,mid;
bool ifmid;
string str[maxn];
bool vis[maxn];
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++) cin>>str[i];
	for(int i=1;i<=n;i++)
	{
		for(int j=i+1;j<=n;j++)
		{
			if(vis[j]) continue;
			bool flag=true;
			for(int k=0;k<m;k++)
			{
				if(str[i][k]==str[j][m-k-1]) 
				{
					continue;
				}
				else
				{
					flag=false;
					break;
				}
			}
			if(flag)
			{
				vis[i]=vis[j]=true;
				sub+=str[i];
			}
		}
		if(!vis[i])
		{
			bool flag=true;
			for(int j=0;j<m;j++)
			{
				if(str[i][j]!=str[i][m-1-j])
				{
					flag=false;
					break;
				}
			}
			if(flag)
			{
				mid=str[i];
				ifmid=true;
			}
		}
	}
	ans+=sub;
	reverse(sub.begin(),sub.end());
	if(ifmid) ans+=mid;
	ans+=sub;
	if(!ans.empty())
	{
		cout<<ans.length()<<endl<<ans;
	}
	else cout<<0;
	return 0;
} 
```

</br>

## C. [Air Conditioner](https://codeforces.com/contest/1304/problem/C)

### 题意

​	&nbsp;&nbsp;&nbsp;餐厅老板有一个空调，餐厅初始温度为 $m$ ，会陆续来 $n$ 个顾客。餐厅老板每分钟可以控制空调的温度+1，-1，或者是不变。这 $n$ 个顾客会按来的时间顺序给出，每个人有一个感到舒适的温度范围，如果空调的温度在这个范围里面，那么顾客就会满意。问餐厅老板是否可以达到让每个人都满意。

### 思路

​	&nbsp;&nbsp;&nbsp;首先我们先思考从餐厅开始到第一个客人来临的时候，假设第一个人来临的时间是 $t$ ，舒适区间为 $[l,r]$，那么我们可以很容易发现只要 $[l,r]$ 与 $[m-t,m+t]$  有交集，那么就是可以满足第一个客人条件。如果第二个和第一人的时间差为 $\Delta t$  ，那么这个时候要计算可达到的舒适区间就是在上次交集的区间上左右变化 $\Delta t$ ，为什么是交集呢。我一开始想错了。。一直写成并集，然后一直调不出来。但实际上不是这样的，我们可以理解为只有交集那部分才是符合上个顾客要求的，如果超出那个范围，就不能够满足上个顾客要求。所以挨个顾客扫一遍就行了。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

int q,n,m;
int last,l,h,t;
int nowl,nowr,delt;
int lef,righ;
bool flag;
int main()
{
	scanf("%d",&q);
	while(q--)
	{
		scanf("%d%d",&n,&m);
		lef=righ=m;
		last=0;
		flag=true;
		for(int i=1;i<=n;i++)
		{
			scanf("%d%d%d",&t,&l,&h);
			delt=t-last;
			nowl=lef-delt;
			nowr=righ+delt;
			last=t;
			if(nowl>h||nowr<l) flag=false;
			else 
			{
				lef=max(nowl,l);
				righ=min(nowr,h);
			}
		}
		if(flag)
		{
			printf("YES\n");
		}
		else printf("NO\n");
	}
}
```