---
title: "dp练习"
categories: [ "dp" ]
tags: [ "Luogu","dp" ]
draft: false
slug: "29"
date: "2020-02-18 01:00:00"
---

## A. [矩阵取数游戏](https://www.luogu.com.cn/problem/P1005)

### 题意

​	给定一个 $n\times m$ 的矩阵，其中每个元素为非负整数。每次你可以从每行的行首或行末取一个元素，得到的分数为当前元素的值 $a_{ij}\times 2^k$  ，$k$ 为当前是第几次取该行上的元素。 问最大得分为多少。


<!--more-->


### 思路

​	首先我们发现，虽然答案问的是 $n$ 行，但是我们发现，不同行之间是不会相互影响的，我们只需要讨论一行的情况，然后对每行都处理一遍就可以了。
​	那么对于一行上的 $m$ 个元素，我们每一次取都有两种选择，一个是取行首，一个是取行尾。我们发现这是一个区间问题，对于区间 $[i,j]$ 的最优情况，我们可以从 $[i+1,j]$  和 $[i,j-1]$ 这两个的最优情况转移过来，因此我们可以定义 $f[i][j]$ 是从第 $i$ 个元素到第 $j$ 的元素得分的最大值，然后从上述两个状态转移过来。
​	还有一个问题就是我们的 $2^k$ 问题，我们考虑到大区间的最优值是从小区间转移来，也就是说小区间乘的指数高，大区间乘的指数低，我们又是从小区间推到大区间，转移一次就乘一次，最后肯定是大区间的少，小区间的多了。qwq因为这个题的次数可能很高，会爆long long，实在是不想写高精度，就用了__int128，第一次用，感觉还不错QAQ..

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=85;
int n,m;
__int128 map[maxn][maxn];
__int128 dp[maxn][maxn],ans;
void read(__int128 &x)
{
	x=0;
	char ch;
	ch=getchar();
	while(ch<'0'||ch<'0') ch=getchar();
	while(ch>='0'&&ch<='9')
	{
		x=x*10+ch-'0';
		ch=getchar();
	}
}
void out(__int128 x)
{
	if(x>=10) out(x/10);
	putchar(x%10+'0');
}
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++) read(map[i][j]);
	}
	for(int i=1;i<=n;i++)
	{
		for(int f=1;f<=m;f++)
		{
			for(int j=1;j<=m;j++) dp[f][j]=0;
		}
		for(int len=1;len<=m;len++)
		{
			for(int j=1;j+len-1<=m;j++)
			{
				int k=j+len-1;
				dp[j][k]=max(dp[j+1][k]*2+map[i][j]*2,dp[j][k-1]*2+map[i][k]*2);
			}
		}
		ans+=dp[1][m];
	}
	out(ans);
	return 0;
}
```

</br>

## B. [关路灯](https://www.luogu.com.cn/problem/P1220)

### 题意

​	在一条街道上有 $n$ 个路灯开着，他们可以看作分布在 $x$ 轴上，每个路灯有一定的坐标，也就是代表他们的位置。每个路灯也有一定的功率 $x$ ，代表一秒钟消耗多少电能。姜大佬初始位置在 $c$  ，他每天早晨出来关掉路灯。它可以先关左边的也可以先关右边的，他的行走速率是 $1m/s$ ，问最少消耗多少电能能关掉所有路灯。

### 思路

​	又是一道区间dp的题，那么我们可以思考一下，首先小姜老师肯定不能跳着关路灯，也就是说这个区间内中间的路灯都被关闭了，他会关一个区间的左端点或者右端点，而不会关这个区间外面的。那么我们就可以枚举区间长度来解决这个问题，从小的枚举到大的。
​	那么我们思考一下如何定义这个状态。我们可以定义关闭区间 $[i,j]$ 的路灯，最少消耗电能为 $f[i][j]$ ，如果需要转移状态的话，我们想到，对于一个方向，小姜老师可以继续往前走，关掉前面那盏灯，也可以返回去，关闭它屁股后面那盏灯。因为是有两个方向的，所以我们可以拓宽一维，用 $f[i][j][0/1]$ 来表示小姜老师现在是在往左走还是往右走，那么很明显 $f[i][j][0]$ 这个状态可以从 $f[i+1][j][0/1]$  来转移分别对应着小姜老师继续往前走关眼前的，和返回关屁股后的。相应的 $f[i][j][1]$ 就可以从 $f[i][j-1][0/1]$ ，转移的时候再加上消耗的电能即可！

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=55;
const int inf=100000000;
int n,c;
int lt[maxn]; //lantern pos
int en[maxn]; //energy 
int sum[maxn]; //prefix sum
int dp[maxn][maxn][2];
int calc(int i,int j)
{
	return sum[n]-(sum[j]-sum[i-1]);
}
int main()
{
	scanf("%d%d",&n,&c);
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=n;j++)
		{
			for(int k=0;k<=1;k++) dp[i][j][k]=inf;
		}
	}
	for(int i=1;i<=n;i++)
	{
		scanf("%d%d",&lt[i],&en[i]);
		sum[i]=en[i]+sum[i-1];
	}
	dp[c][c][0]=dp[c][c][1]=0;
	for(int len=2;len<=n;len++)
	{
		for(int i=1;i+len-1<=n;i++)
		{
			int j=i+len-1;
			dp[i][j][0]=min(dp[i+1][j][0]+(lt[i+1]-lt[i])*calc(i+1,j),dp[i+1][j][1]+(lt[j]-lt[i])*calc(i+1,j));
			dp[i][j][1]=min(dp[i][j-1][0]+(lt[j]-lt[i])*calc(i,j-1),dp[i][j-1][1]+(lt[j]-lt[j-1])*calc(i,j-1));
		}
	}
	printf("%d",min(dp[1][n][0],dp[1][n][1]));
	return 0;
}
```

</br>

## C. [小a和uim之大逃离](https://www.luogu.com.cn/problem/P1373)

### 题意

​	给定一个 $n\times m$ 的矩阵，每个格子中有 $0\sim k$ 滴魔液，小姜老师和它朋友可以从任意地点开始，一起向右或者向下走，他们每个人有一个容量为 $k$ 的瓶子，由小姜老师开始轮流收集地上的魔液，当收集魔液大于 $k$ 的时候，会对 $k+1$ 取模，问他们有多少种方法使得收集的魔液数量相同。

### 思路

​	很显然我们可以写一个5维dp，用 $f[i][j][p][l][0/1]$ 来表示，当到了坐标为 $(i,j)$ 的格子的时候，小姜老师拥有魔液 $p$ 滴，他的朋友拥有魔液 $l$ 滴，并且这一次是由小姜老师采集的/由他的朋友采集的。这样的话，感觉比较好些，但是。。空间占用非常的高，我们优化一下，因为问的是收集相等，所以只需要维护他们的差就可以了，也就是说，只需要将 $(p-l+k)\\%k$ 维护一下就可以了，因此循环枚举坐标以及他们的差，从低的向高的转移就可以了。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=805;
int map[maxn][maxn];
int dp[maxn][maxn][16][2];
const int mod=1e9+7;
int n,m,k;
long long ans;
int main()
{
//	freopen("test.in","r",stdin);
	scanf("%d%d%d",&n,&m,&k);
	k++;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
		{
			scanf("%d",&map[i][j]);
			dp[i][j][map[i][j]%k][0]=1;
		}
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
		{
			for(int t=1;t<=k;t++)
			{
				dp[i][j][t%k][0]+=dp[i-1][j][(t-map[i][j]+k)%k][1];dp[i][j][t%k][0]%=mod;
				dp[i][j][t%k][0]+=dp[i][j-1][(t-map[i][j]+k)%k][1];dp[i][j][t%k][0]%=mod;
				dp[i][j][t%k][1]+=dp[i-1][j][(t+map[i][j])%k][0];dp[i][j][t%k][1]%=mod;
				dp[i][j][t%k][1]+=dp[i][j-1][(t+map[i][j])%k][0];dp[i][j][t%k][1]%=mod;
			}
		}
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
		{
			ans+=dp[i][j][0][1]%mod;
			ans%=mod;
		}
	}
	printf("%lld",ans);
}
```