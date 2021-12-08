---
title: "dp习题练习"
categories: [ "dp" ]
tags: [ "Luogu","dp" ]
draft: false
slug: "24"
date: "2020-02-12 00:45:00"
---

## A. [方格取数](https://www.luogu.com.cn/problem/P1004)

### 题意

​	&nbsp;&nbsp;&nbsp;有一个 $N*N$ 的整数方阵，每个点初始值为0，在一些点上放上数，一个人从左上角走到右下角，规定只能向下或向右走，当他经过的点上有数时会取走它，问走两遍最多能取的数的和最大为多少。


<!--more-->


### 思路

​	&nbsp;&nbsp;&nbsp;也就是说我们要找两条路径使他取数最大，首先我一开始想法是先走一遍，找到最大的那个路径，将这条路径上所有点设为0，然后再回来找这个方阵中最大的那个路径，两个加起来就行。但是路径上所有的点设为0这个地方不是很好实现，因此我们可以考虑另一个思路，两次同时走。我们把这两次看成两个人走的，表述方便。
​	&nbsp;&nbsp;&nbsp;我们用 $dp[i][j][k][l]$ 来表示当第一个人走到 $(i,j)$  第二个人走到 $(k,l)$ 时做能取数最多为多少 ，那么我们就可以考虑一下转移怎么转移，因为到达一个点只能是从左边来，或者是从上边来，因此第一个人从 $(i-1,j)$ 或者 $(i,j-1)$ 转移来，那么第二个人就从 $(k-1,l)$ 或者 $(k,l-1)$ 转移来，那么这个转移方程就是四种转移方式。我们需要保证一个问题，就是他俩经过同一个点的判定情况。我们需要将第二个人的坐标通过第一个人来限制，也就是说要确保第二个人和第一个人步数是相同的，当他们步数相同的时候，那么就不存在他们经过同一个点但是时间却是不同的情况了，因为到达一个点的步数是一定的。
​	&nbsp;&nbsp;&nbsp;再就是这个状态数组其实还是可以压缩到三维和二维的，这个就先不谈了，可以看洛谷的题解区。
​	&nbsp;&nbsp;&nbsp;这个题是一个经典的多维dp的题目，感觉还是挺有意义的。而且这个题和 [传纸条](https://www.luogu.com.cn/problem/P1006) 很像，可以双倍经验。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=15;
int map[maxn][maxn];
int n,x,y,z;
int m;
int dp[maxn][maxn][maxn][maxn];
int main()
{
	scanf("%d",&n);
	while(1)
	{
		scanf("%d%d%d",&x,&y,&z);
		if(x==0&&y==0&&z==0) break;
		map[x][y]=z;
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=n;j++)
		{
			for(int k=1;k<=n;k++)
			{
				m=i+j-k;
				if(m<=0) continue;
				if(i==k&&j==m) dp[i][j][k][m]=max(max(dp[i-1][j][k-1][m],dp[i-1][j][k][m-1]),max(dp[i][j-1][k-1][m],dp[i][j-1][k][m-1]))+map[i][j];
				else dp[i][j][k][m]=max(max(dp[i-1][j][k-1][m],dp[i-1][j][k][m-1]),max(dp[i][j-1][k-1][m],dp[i][j-1][k][m-1]))+map[i][j]+map[k][m];
			}
		}
	}
	printf("%d",dp[n][n][n][n]);
	return 0;
} 
```

</br> 

## B. [创意吃鱼法](https://www.luogu.com.cn/problem/P1736)

### 题意

​	&nbsp;&nbsp;&nbsp;给出一个 $N*M$ 的只包含0和1的数阵，求只有对角线为1，其余位置为0的子方阵的最大边长。

### 思路

​	&nbsp;&nbsp;&nbsp;这个题我们需要考虑两个方向，这个对角线既可以是斜向左上，也可以是斜向右上的。我们先考虑前者，后者同理即可。我们可以用两个数组 $col[i][j]$ 和 $row[i][j]$ 分别来表示，包含 $(i,j)$ 这个点往上一列有多少个0，以及包含这个点往左一行有多少个0，（这个在程序里好像我写反了，但是没啥区别说实话）。然后我们用 $f[i][j]$ 来表示，从这个点左上走满足条件的方阵的最大边长。那么很显然这个值和上方的0，左方的0，以及左上的状态有关，这个点是1的话，转移方程就是 $f[i][j]=min(f[i-1][j-1],min(col[i-1][j],row[i][j-1]))+1$ 。如果是0的话我们就更新 $col$ 和 $row$ 数组的值。然后求完斜向左上的再求一遍斜向右上的，取一个最大值即可。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=2505;
int n,m;
int map[maxn][maxn];
int col[maxn][maxn],row[maxn][maxn];
int dp[maxn][maxn];
int ans;
int main()
{
//	freopen("test.in","r",stdin);
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
		{
			scanf("%d",&map[i][j]);
		if(!map[i][j])
		{
			row[i][j]=row[i-1][j]+1;
			col[i][j]=col[i][j-1]+1;
		}
		else 
		{
			dp[i][j]=min(dp[i-1][j-1],min(row[i-1][j],col[i][j-1]))+1;
			ans=max(dp[i][j],ans);
		}
		}
	}
	memset(row,0,sizeof(row));
	memset(col,0,sizeof(col));
	memset(dp,0,sizeof(dp));
	for(int i=1;i<=n;i++)
	{
		for(int j=m;j>=1;j--)
		{
			if(!map[i][j])
			{
				row[i][j]=row[i-1][j]+1;
				col[i][j]=col[i][j+1]+1;
			}
			else
			{
				dp[i][j]=min(dp[i-1][j+1],min(row[i-1][j],col[i][j+1]))+1;
				ans=max(ans,dp[i][j]);
			}
		}
	}
	printf("%d",ans);
}
```

</br>

## C. [乌龟棋](https://www.luogu.com.cn/problem/P1541)

### 题意

​	&nbsp;&nbsp;&nbsp;给出标号分别为1，2，3，4的四种卡片若干张，分别可以移动1，2，3，4步，玩家初始处于坐标为1的位置。玩家出一张牌，可移动相应的步数。移动到不同的坐标会加不同的分数，很明显不同的出牌顺序会对应着不同的分数，求玩家能获得的最大分数为多少。

### 思路

​	&nbsp;&nbsp;&nbsp;很显然我们可以通过已经出的牌计算出现在已经到达到哪个位置。我们可以用一个四重循环，来循环每张牌用的数量，很明显我们到达一个目标位置所用的最后一张牌可以是1，2，3，4的任何一个，因此我们可以从这个四个状态转移过来，找其中最大那个就可以了，注意要判断一下要转移过来的状态是否合法。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=355;
const int maxm=125;
int n,m,pos;
int w[maxn];
int x,num[5];
int dp[42][42][42][42];
int main()
{
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++) scanf("%d",&w[i]);
	for(int i=1;i<=m;i++)
	{
		scanf("%d",&x);
		num[x]++;
	} 
	memset(dp,-1,sizeof(dp));
	dp[0][0][0][0]=w[1];
	for(int a=0;a<=num[1];a++)
	{
		for(int b=0;b<=num[2];b++)
		{
			for(int c=0;c<=num[3];c++)
			{
				for(int d=0;d<=num[4];d++)
				{
					pos=1+a+(b<<1)+c*3+(d<<2);
					if(a>0&&dp[a-1][b][c][d]!=-1)
					{
						dp[a][b][c][d]=max(dp[a][b][c][d],dp[a-1][b][c][d]+w[pos]);
					}
					if(b>0&&dp[a][b-1][c][d]!=-1)
					{
						dp[a][b][c][d]=max(dp[a][b][c][d],dp[a][b-1][c][d]+w[pos]);
					}
					if(c>0&&dp[a][b][c-1][d]!=-1)
					{
						dp[a][b][c][d]=max(dp[a][b][c][d],dp[a][b][c-1][d]+w[pos]);
					}
					if(d>0&&dp[a][b][c][d-1]!=-1)
					{
						dp[a][b][c][d]=max(dp[a][b][c][d],dp[a][b][c][d-1]+w[pos]);
					}
				}
			}
		}
	}
	printf("%d",dp[num[1]][num[2]][num[3]][num[4]]);
	return 0;
}
```

</br>

## D. [能量项链](https://www.luogu.com.cn/problem/P1063)

### 题意

​	&nbsp;&nbsp;&nbsp;给出一段含有 $n$ 个珠子的环状项链，对于相邻的两个珠子，前一颗珠子的尾标记等于后方珠子的头标记。
​	&nbsp;&nbsp;&nbsp;例如项链为 $[2,4,6,8]$ ， 那么用加入标记表示就是 $[(2,4),(4,6),(6,8),(8,2)]$  。当两个珠子两两合并的时候会产生的能量大小为 前方珠子头标记 $\times$ 前方珠子尾标记 $\times$ 后方珠子尾标记。显然合并的顺序不同最终会产生不同的能量值，问能产生的最大能量值为多少。

### 思路

​	&nbsp;&nbsp;&nbsp;这道题和合并石子很像，也是一个区间dp的例题，我们也是通过枚举区间长度，然后枚举区间断点来分割区间。这个题也是一个环状，我们也是断环为链，不过处理释放的能量值的问题，我是用了一个结构体，来表示每一颗珠子的标记，通过这个来计算释放能量。不过记得处理子区间也要从处理到 $1\sim 2n$， 这个地方卡了我巨长时间，因为你后面要用到这个状态，如果不计算子区间无法转移到后面。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

typedef long long ll;
const int maxn=105;
int n;
ll dp[maxn<<1][maxn<<1];
struct node
{
	ll w;
	ll nxt;
}a[maxn<<1];
int main()
{
//	freopen("test.in","r",stdin);
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%lld",&a[i].w);
		a[i+n].w=a[i].w;
	}
	for(int i=1;i<=n*2-1;i++)
	{
		a[i].nxt=a[i+1].w;
	}
	for(int i=1;i<=2*n;i++) dp[i][i]=0;
	for(int p=2;p<=n;p++)
	{
		for(int i=1;i<=2*n-p+1;i++) //重要(卡我巨长时间)
		{
			int j=i+p-1;
			for(int k=i;k<j;k++)
			{
				dp[i][j]=max(dp[i][j],dp[i][k]+dp[k+1][j]+a[i].w*a[k].nxt*a[j].nxt);
			}
		}
	}
//	for(int i=1;i<=n;i++)
//	{
//		printf("%d ",dp[i][i+1]);
//	}
	ll ans=0;
	for(int i=1;i<=n;i++) ans=max(ans,dp[i][i+n-1]);
	printf("%lld",ans);
	return 0;
}
```

