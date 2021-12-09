---
title: "CodeforcesER #81"
categories: [ "ACM" ]
tags: [ "Codeforces" ]
draft: false
slug: "8"
date: "2020-02-02 21:53:00"
---

###  A : [Display The Number](https://codeforces.com/contest/1295/problem/A)

#### 题意

​	   用一定数目的灯管，显示尽可能大的数

![A](https://s2.ax1x.com/2020/02/02/1te6Re.md.png)


<!--more-->

#### 思路

​	   因为位数多的肯定更大，所以肯定用尽量少的灯管搭建单个数字更好，最少的两个分别是两个灯管显示的1，以及三个灯管显示的7，所以就是尽可能的用1，如果最后剩余正好三个就显示7。这就转化成了判断奇数还是偶数的题，奇数就显示7111····，偶数就是1111···。注意要把7放在前面（我就踩坑了）。

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
using namespace std;
int t,n;
int cnt=0;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d",&n);
		if(n%2==0)
		{
			int p=n/2;
			for(int i=1;i<=p;i++) printf("1");
		}
		else
		{
			printf("7");
			n-=3;
			int p=n/2;
			for(int i=1;i<=p;i++) printf("1");
		}
		printf("\n");
	}
	return 0;
} 
```



***

### B : [Infinite Prefixes](https://codeforces.com/contest/1295/problem/B)

#### 题意

​	   给定一段01字符串 **s** 为循环节，得到无限循环的01字符串 **t**，求 **t** 中有多少前缀满足0个数-1个数等于期望值**x** （空前缀也算是一个前缀）

> 前缀：例如"abcd"的前缀包括 " ","a","ab","abc","abcd".

#### 思路  

首先我们先记录循环节 **s** 中每个位置对应的01个数差，记为$num_i，i\in[1,n]$ （ **s** 长度记为n） 

 +  首先我们可以发现当 **x=0** 的时候，空前缀也会有贡献，因此不能忽略空前缀。
 +  如果循环节 **s** 的01数相等，那么我们可以发现最后循环节一位$num_n$总为0，那么可以分两种情况来讨论
    + 如果循环节中存在大于等于1个前缀满足期望值x，那么就有无限个满足，因此输出-1
    + 如果循环节 **s** 中不存在满足期望值的前缀，那么 **t** 中也一定不存在

+ 再来看一般情况，如果一个前缀中包含多个循环节 **s** ,那么前面每个循环节对于最终01个数差的贡献总为$num_n$，因此我们可以用所期望的值 **x**，利用1~n 循环减去每一位的 $num_i$，如果所得是$num_n$的非负倍数，那么就是符合期望的，否则不是。(本来一直这里不太明白，后来发现对于循环节中的每一个位置，在后续循环的过程中，如果$num_n$不为0，那么这个位置每次对应的值总是唯一的)

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
using namespace std;
int num[100005];
int t,cnt,n,x;
bool flag=false;
char s[100005];
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		cnt=0;
		flag=false;
		scanf("%d%d",&n,&x);
		scanf("%s",s+1);
		for(int i=1;i<=n;i++)
		{
			if(s[i]=='0')
			{
				num[i]=num[i-1]+1;				
			}
			else num[i]=num[i-1]-1;
			if(num[i]==x) flag=true;
		}
		int p=num[n];
		if(p==0)
		{
			if(flag==true)
			{
				printf("-1\n");
				continue;
			}
			else
			{
				printf("0\n");
				continue;
			}
		}
		else 
		{
			for(int i=1;i<=n;i++)
			{
				int m=x-num[i];
				if(m%p==0&&m/p>=0) 
				{
					cnt++;
				}
			}
		}
		if(x==0) cnt++;
		printf("%d\n",cnt);
	}
}
```



****

### C : [Obtain The String](https://codeforces.com/contest/1295/problem/C)

#### 题意

​	   给定字符串 **s** 和 **t** ，每次从 **s** 中选取子序列放入起始为空串的 **z** 后，问最少需要多少次操作使得 **z=t**

#### 思路

​	   看了小姜老师的博客解法说是贪心，想了好一会，好像确实是可以贪心的···? 设置两个指针从 **s** 和 **t** 串的头部开始扫，对于 **t** 串中的每个字母，循环扫 **s** 串在其中找与它相同的，最终的答案就是扫 **s** 串的次数。(小姜老师说这个实质上就是每次尽可能找尽可能多的后缀，仔细想想确实是这样。)不过这么一直暴力扫下去肯定不是最优的方法，想办法去优化。还是借鉴大佬的想法用一个lens\*26的跳表，然后O(lent)扫一遍 **t** 即可.

> 跳表nxt的作用，用于寻找下一个所寻找字符在s中的位置。
>
> nxt\[x\]\[y\]用于指向从x位置开始下一个y的位置+1 （next在C++属于保留字，注意不要踩坑）

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cstring>
#include<iostream>
using namespace std;

const int maxn=100005;
char s[maxn],t[maxn];
int nxt[maxn][30];
int p,lens,lent;
int pos,ans; 
int main()
{
	scanf("%d",&p);
	while(p--)
	{
		ans=1;
		scanf("%s",s+1);
		scanf("%s",t);
		lent=strlen(t),lens=strlen(s+1);
//		printf("%d",lens);
		for(int c=0;c<26;c++)
		{
			nxt[lens+1][c]=-1;
			for(int i=lens;i>=1;i--)
			{
				if(s[i]-'a'==c)
				{
					nxt[i][c]=i+1;
				}
				else nxt[i][c]=nxt[i+1][c];
			}
		}
		pos=1;
		for(int i=0;i<lent;i++)
		{
			pos=nxt[pos][(int)t[i]-'a'];
			if(pos==-1)
			{
				ans++;
				pos=1;
				pos=nxt[pos][(int)t[i]-'a'];
				if(pos==-1)
				{
					ans=-1;
					break;
				}
			}
		}
		printf("%d\n",ans);
	}
}
```

***