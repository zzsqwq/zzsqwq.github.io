---
title: "基础线性dp例题"
categories: [ "dp" ]
tags: [ "Luogu","dp" ]
draft: false
slug: "18"
date: "2020-02-06 00:09:00"
---

### 前言

---

   某位大佬曾经说过，dp不会没问题，想不到状态转移方程没问题，多做题就会了。所以，我打算多刷点dp题。那么，先从基础刷起吧。



### 1. [P1091 合唱队形](https://www.luogu.com.cn/problem/P1091)

---

#### 题意

   已知序列 $a$ 有 $n$ 个数，通过取出其中一些数可以使他满足严格的先增再减序列，问最少取出几个。

---


<!--more-->


#### 思路

   很显然想要求最少取出几个，我们就看严格先增再减的序列的最长长度即可。我们可以用 $g[i]$ 来存储到 $a[i]$ 为止的最长递增子序列的长度，然后用 $l[i]$ 来存储从 $a[i]$ 到序列末尾最长的递减子序列的长度。处理 $g[i]$ 从前往后扫，处理 $l[i]$ 需要从后往前扫。处理完 $f$ 和 $g$ 数组那么就从左到右扫一遍，$ans=max(ans,g[i]+l[i]-1)$ 。答案即是 $n-ans$ 。 

---

#### 代码实现

```cpp
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<iostream>
#include<cstring>

using namespace std;

const int maxn=105;
int n;
int a[maxn],g[maxn],l[maxn];
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&a[i]);
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=0;j<i;j++)
		{
			if(a[i]>a[j]) g[i]=max(g[i],g[j]+1);
		}
	}
	for(int i=n;i>=1;i--)
	{
		for(int j=n+1;j>i;j--)
		{
			if(a[i]>a[j]) l[i]=max(l[i],l[j]+1);
		}
	}
	int maxout=0;
	for(int i=1;i<=n;i++)
	{
		maxout=max(maxout,g[i]+l[i]-1);
	}
	printf("%d",n-maxout);
	return 0;
} 
```

---



### 2. [P1280 尼克的任务](https://www.luogu.com.cn/problem/P1280)

---

#### 题意

   尼克的一个工作日为 $n$ 分钟，从第一分钟开始到第 $n$ 分钟结束。当尼克到达单位后他就开始干活。如果在同一时刻有多个任务需要完成，尼克可以任选其中的一个来做，而其余的则由他的同事完成，反之如果只有一个任务，则该任务必需由尼克去完成，假如某些任务开始时刻尼克正在工作，则这些任务也由尼克的同事完成。如果某任务于第 $p$ 分钟开始，持续时间为 $t$ 分钟，则该任务将在第 $p+t-1$ 分钟结束。（实在不会总结题意，就直接复制过来了）

---

#### 思路

   我们可以设 $f[i]$ 为时间从 $i\sim n$ 所能获得最长空闲时间，最终 $f[1]$ 对应的就是答案。假设在这个 $i$ 分钟有 $k[i]$ 个任务可以，那么我们可以分以下情况转移

+ $k[i]=0$  ， 那么  $f[i]=f[i+1]+1$ 
+ $k[i]\not=0$  ，那么可以循环 $1 \sim k[i]$ 遍历这个时间点开始的任务，$f[i]=max(f[i],f[i+k[i].t])$ 

   思路是这样的，但是我们记录 $k[i].t$ 并不好记录，因此我们可以先将任务开始时间按降序排序，用一个一个变量 $cnt$ 来代表已经取到第几个任务了，那么这样一直取下去，最终就能够遍历所有的任务。

---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=10005;
struct task
{
	int l,r;
}t[maxn];
int cmp(struct task a,struct task b)
{
	return a.l>b.l;
}
int dp[maxn];
int p[maxn];
int cnt=1,n,k; 
int main()
{
	scanf("%d%d",&n,&k);
	for(int i=1;i<=k;i++)
	{
		scanf("%d%d",&t[i].l,&t[i].r);
		p[t[i].l]++;
	}
	sort(t+1,t+1+k,cmp);
	for(int i=n;i>=1;i--)
	{
		if(!p[i])
		{
			dp[i]=dp[i+1]+1;
		}
		else 
		{
			for(int j=1;j<=p[i];j++)
			{
				dp[i]=max(dp[i],dp[i+t[cnt].r]);
				cnt++;
			}
		}
	}
	printf("%d\n",dp[1]);
	return 0;
}
```

---



   我太懒了····就写了两道，明天继续加油吧。。