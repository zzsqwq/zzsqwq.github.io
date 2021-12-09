---
title: "树形dp例题"
categories: [ "dp" ]
tags: [ "Luogu","dp" ]
draft: false
slug: "31"
date: "2020-02-20 01:04:00"
---

## 前言

​	学OI的时候就做过树形dp的题，不过那时候全在划水。看了看题解还不太懂就直接照着题解写了，现在再回来看还是不会QAQ，所以就再看看然后自己写了一遍。


<!--more-->


</br>

## A. [没有上司的舞会](https://www.luogu.com.cn/problem/P1352)

### 题意

​	有 $N$ 个职员被邀请去参加公司的舞会，他们每个人对应着一个快乐指数 $h_i$ ，如果该职员来了就会为舞会增加$h_i$ 点快乐指数。这 $N$ 个职员之间有从属关系，也就是说他们的关系就像一棵以顶级上司为根的树，父结点就是子结点的直接上司。如果一个职员的**直接**上司来到了舞会，那么他本人就不会再来。问邀请哪些职员可获得最大的快乐指数，最大为多少。

### 思路

​	职员之间的关系是以树的形式给出的，所以先用链式前向星来存储数。存的时候我们用vis数组记录一下如何那个人有上司，就将他标记一下，最终没有标记的就是没有上司的，也就是顶级上司，也就是我们的根节点root。
​	树形dp，顾名思义，在树上进行dp，通过递归dfs，先算出子树的状态，再通过递归的回溯来合并。那么我们考虑一下设计状态，很明显一个人的状态有来和不来，两个情况。所以我们设计状态 $dp[i][0/1]$  来表示职员 **i** 来或者不来，我们用u来表示当前节点，用v来表示当前节点的子节点，那么状态转移如下：

+  $dp[u][0]=max(dp[v][0],dp[v][1]) $ （上司u没有来，那么下属v可以来，也可以不来，选一个大的策略）

+ $dp[u][1]=dp[v][0]+h[u]$  （上司u来了，下属v肯定不来）

​	最终的答案就是 $max(dp[root][0],dp[root][1])$ ，上司来和不来两种策略中的最大一种。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=6005;
int head[maxn];
struct edge
{
	int next;
	int to;
}e[maxn];
int cnt,n,l,k,vis[maxn],root;
int r[maxn];
int dp[maxn][2];
void add(int l,int k)
{
	e[++cnt].next=head[l];
	e[cnt].to=k;
	head[l]=cnt;
}
void dfs(int u)
{
	dp[u][0]=0;
	dp[u][1]=r[u];
	for(int i=head[u];i;i=e[i].next)
	{
		int v=e[i].to;
		dfs(v);
		dp[u][1]+=dp[v][0];
		dp[u][0]+=max(dp[v][1],dp[v][0]);
	}
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++) scanf("%d",&r[i]);
	for(int i=1;i<n;i++)
	{
		scanf("%d%d",&l,&k);
		add(k,l);
		vis[l]++;
	}
	scanf("%d%d",&l,&k);
	for(int i=1;i<=n;i++) 
	{ 
		if(!vis[i]) root=i;
	}
	dfs(root);
	printf("%d",max(dp[root][0],dp[root][1]));
	return 0;
} 
```

</br>

## B. [战略游戏](https://www.luogu.com.cn/problem/P2016)

### 题意

​	小姜老师要建立一个树，树节点两两之间通过道路连接，他要在这些节点上放一些小人，每个小人可监管与该节点相连的道路，问最少放置多少小人可以监管所有道路。（数据保证0为根节点）

### 思路

​	这个题跟上个题有点像，首先我们也是用链式前向星把树给存下来，不过这个题的输入跟上道题有点不一样，本质都是一样的。因为我们只需要dfs下去，然后向上回溯的时候合并，所以只需要存单向边即可。
​	对于一个节点我们也是有两种策略，选或者不选，那么我们也可以把状态写成  $dp[i][1/0]$ ，对应着 **i** 这个节点选或者不选，我们把当前节点看作u，子节点看作v，状态转移如下：

+  $dp[u][1]+=\Sigma (dp[v][0],dp[v][1]) +1$ （u选了，v选不选都可以，加上自身的1）

+  $dp[u][0]+=\Sigma dp[v][1]$    （如果u没有选，那么v一定要选，才能监管到u的道路）

​	很显然这个答案是  $min(dp[0][0],dp[0][1])$  

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=1505;
struct edge
{
	int next,to;
}e[maxn];
int u,v;
int head[maxn];
int dp[maxn][2];
int num;
int n,cnt;
void add(int u,int v)
{
	e[++cnt].next=head[u];
	e[cnt].to=v;
	head[u]=cnt;
}
void dfs(int u)
{
	dp[u][1]=1;
	for(int i=head[u];i;i=e[i].next)
	{
		int v=e[i].to;
		dfs(v);
		dp[u][1]+=min(dp[v][1],dp[v][0]);
		dp[u][0]+=dp[v][1];
	} 
}
int main()
{
	scanf("%d",&n);
	for(int i=0;i<n;i++)
	{
		scanf("%d%d",&u,&num);
		for(int i=1;i<=num;i++)
		{
			scanf("%d",&v);
			add(u,v);
		}
	}
	dfs(0);
	printf("%d",min(dp[0][0],dp[0][1]));
	return 0;
}
```

</br>

## C. [二叉苹果树](https://www.luogu.com.cn/problem/P2015)

### 题意

​	有一颗二叉苹果树，也就是说树枝如果分叉，一定是分二叉。苹果树有 $n$ 个节点，树根编号为 1 。每个树枝上都有一定数量的苹果，如果最终保留 $q$ 根树枝，问最多能够保留多少苹果。

### 思路

​	这个树形dp要比上面的还要难一些，因为我们的状态不能够只考虑是否保留当前的树枝了，因为保留当前树枝也会有很多种关于保留子树枝选择。看了题解大佬的说法是，可以定义成 $f[u][j]$ 来表示 u 的子树上保留 j 条边时所能获得最大的苹果数。所以我们考虑一下转移方程，首先我们能保留的最大边数是当前子树所具有的所有树枝，对于一个节点下的两个树枝，我们可以选择取，或者不取，那么这就类似于一个背包问题，对于一定的容量（一定量的树枝），我们取几个树枝才能获得最大的价值。但是我们注意，对于一个节点u，我们如果要取他的节点v，那么我们u-v之间这个树枝一定要保留，不然会不能取得v，这是一个隐藏的条件。
​	因此转移方程： $dp[u][i]=max(dp[u][i],dp[u][i-j-1]+dp[v][j]+w[u][v] $ ，$w[u][v]$为当前u-v树枝的苹果数。代码里面因为我懒得计算子树的树枝数了，所以就直接从最大的q来枚举了，就没考虑那么多，可能复杂度稍微高一点，但是高不到哪去其实QAQ...

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=105;
struct edge
{
	int next,to,w;
}e[maxn];
int n,cnt,q;
int u,v,w;
int dp[maxn][maxn];
int head[maxn];
void add(int u,int v,int w)
{
	e[++cnt].next=head[u];
	e[cnt].to=v;
	e[cnt].w=w;
	head[u]=cnt;
}

void dfs(int u)
{
	for(int i=head[u];i;i=e[i].next)
	{
		int v=e[i].to;
		dfs(v);
		for(int j=q;j>=0;j--)
		{
			for(int k=0;k<=j-1;k++)
			{
				dp[u][j]=max(dp[u][j],dp[u][j-1-k]+dp[v][k]+e[i].w);
			}
		}
	}
}
int main()
{
	scanf("%d%d",&n,&q);
	for(int i=1;i<n;i++)
	{
		scanf("%d%d%d",&u,&v,&w);
		add(u,v,w);
	}
	dfs(1);
	printf("%d",dp[1][q]);
	return 0;
}
```