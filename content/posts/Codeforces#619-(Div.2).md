---
title: "Codeforces#619 (Div.2)"
categories: [ "ACM" ]
tags: [ "Codeforces" ]
draft: false
slug: "26"
date: "2020-02-14 00:51:00"
---

## A. [Three Strings](https://codeforces.com/contest/1301/problem/A)

### 题意

给定三个长度为 $n$ 的字符串 $a$ , $b$ , $c$ ，遍历每个 $c$ 中每个字符 $c_i$，可以将其替换成 $a_i$ 或者 $b_i$ ，必须操作其中一个，问能否通过此操作使得字符串 $a$ , $b$ 相同。 


<!--more-->


### 思路

仔细思考一下，如果要使得最终两个字符串相同的话，必须字符串 $c$ 中出现的字符，在 $a$ 或者 $b$ 字符串出现过，如果每个位置都出现过，那么就是可以的，否则不行。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=105;
char a[maxn],b[maxn],c[maxn];
int t;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%s",a);
		scanf("%s",b);
		scanf("%s",c);
		int len=strlen(c);
		bool flag=true;
		for(int i=0;i<len;i++)
		if(c[i]!=a[i]&&c[i]!=b[i])
		{
			flag=false;
		}
		if(flag)
		{
			printf("YES\n");
		}
		else printf("NO\n");
	}
	return 0;
}
```

</br>

## B. [Motarack's Birthday](https://codeforces.com/contest/1301/problem/B)

### 题意

给定一个含有 $n$ 个整数的序列 $a$ ，其中有一些数丢失，问将丢失的数赋值为多少才能使得相邻两数之差的绝对值的最大值的最小。

### 思路

我们想一下首先不缺失的数相邻两数之差是一定，无论赋值前后都不影响。而如果两个相邻的数都缺失的话，那么他们之间差的绝对值一定是0，也不用去看。这样的话我们就看一下，不缺失和缺失两数之间差的绝对值如何能够最小。因为最终所有的缺失的数都是赋值为同一个数，所以我们考虑一下发现需要考虑一下 缺失和不缺失的数相邻的时候，不缺失的那个数的最大值和最小值，我们只需要取他们的和的平均，那么绝对值就可以最小了。所以最终我们就把缺失的值赋为两数均值，然后求一遍相邻数之差绝对值的最大值就好了。（好像这道题难点不是思路，而是实现起来有很多边界等乱七八糟的要自习考虑一下。）

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=100005;
const int inf=1000000000;
int n,t;
int a[maxn];
int minn,maxx,ans,anss;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		minn=inf,maxx=-inf,anss=-inf;
		scanf("%d",&n);
		for(int i=1;i<=n;i++)
		{
			scanf("%d",&a[i]);
			if(i>1&&a[i]==-1&&a[i-1]!=-1)
			{
				minn=min(minn,a[i-1]);
				maxx=max(maxx,a[i-1]);
			}
		}
		for(int i=1;i<=n;i++)
		{
			if(i<n&&a[i]==-1&&a[i+1]!=-1)
				{
					minn=min(minn,a[i+1]);
					maxx=max(maxx,a[i+1]);
				}	
		}
		ans=(minn+maxx)/2;
		for(int i=1;i<=n;i++)
		{
			if(a[i]==-1) a[i]=ans;
			if(i>1) anss=max(anss,abs(a[i]-a[i-1]));
		}
		printf("%d %d\n",anss,ans);
	}
	return 0;
} 
```

</br>

## C. [Ayoub's function](https://codeforces.com/contest/1301/problem/C)

### 题意

给定一个01字符串 $s$ ，其中含有 $m$ 个1，用 $f(s)$ 来表示字符串 $s$ 的有多少个字串其中含有1，求出符合条件的字符串 $s$ 中， $f(s)$ 的最大值是多少。

### 思路

这题正着想不太好想，含有1的子串可以有很多种情况，但是正难则反，我们可以求出不含1的字串有多少情况，也就是全0的字串有多少种情况，然后用所有情况减去这个就行。
首先可以发现字符串 $s$ 一共有  $\binom{n}{2}+n$  种连续子串，那么如果一些0是连续的，那么好比有连续 $l$ 个0的话，我们可以发现他是有  $\binom{l}{2}+l$  种情况的。这个字符串一共是含有 $n-m$ 个0的，现在我们思考一下如何摆放这 $(n-m)$ 个0，才能使得  $f(s)$ 最大。那么如果 $f(s)$ 要尽量大，也就是说全0对应的情况就要尽可能的少，所以我们需要将这 $n-m$ 尽可能的均分成 $m+1$  份，类似于排列组合的插空法，将他们插到其中，但是我们发现有很大的可能是不能均分的，也就是说可能会有余数，那么我们就把余数均匀的分给前面余数个空，这样其实每个多贡献了 $(n-m)/(m+1) +1 $ 个。所以答案也就不难写出来了。不过不要忘了开long long。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

typedef long long ll;
ll n,m;
ll t;
int main()
{
	scanf("%lld",&t);
	while(t--)
	{
		scanf("%lld%lld",&n,&m);
		ll sum=n*(n+1)/2;
		ll p=n-m;
		ll mod=p%(m+1);
		ll k=p/(m+1);
		printf("%lld\n",sum-(m+1)*k*(k+1)/2-(k+1)*mod);
	}
	return 0;
} 
```