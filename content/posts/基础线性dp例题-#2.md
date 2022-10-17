---
title: "基础线性dp例题 #2"
categories: [ "dp" ]
tags: [ "Luogu","dp" ]
draft: false
slug: "19"
date: "2020-02-07 00:22:00"
---

### 1. [石子归并](https://www.51nod.com/Challenge/Problem.html#problemId=1021)

---

#### 题意

    $N$ 堆石子摆成一条线。现要将石子有次序地合并成一堆。规定每次只能选相邻的2堆石子合并成新的一堆，并将新的一堆石子数记为该次合并的代价。计算将 $N$ 堆石子合并成一堆的最小代价。

---

#### 思路

   很经典的区间dp例题，我们可以用 $dp[i][j]$ 来表示合并 $i\sim j$ 所需的最小代价，通过枚举中间的断点，来通过方程 $dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+cost[i][j])$  ，其中 $cost[i][j]$ 表示从 $i\sim j$ 的石子总数，通过前缀和很容易计算。在进行状态转移时需要前面状态已知，因为是枚举中间断点，所以断开区间的长度一定要小于原区间，因此在转移之前需要确保比他短的区间都已经达到了最小代价，因此我们可以通过枚举区间长度从 $2\sim N$ 来实现。


<!--more-->


---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;
const int inf=1000005;
const int maxn=105;
int n;
int sum[maxn],a[maxn];
int dp[maxn][maxn];
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&a[i]);
		sum[i]=sum[i-1]+a[i];
	}
	for(int i=1;i<=n;i++) dp[i][i]=0;
	for(int p=2;p<=n;p++)
	{
		for(int i=1;i<=n-p+1;i++)
		{
			int j=i+p-1;
			dp[i][j]=inf;
			for(int k=i;k<j;k++)
			{
				dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1]);
			}
		}
	}
	printf("%d",dp[1][n]);
	return 0;
}
```

---



### 2. [P1880 [NOI1995]石子合并](https://www.luogu.com.cn/problem/P1880)

---

#### 题意

   $N$ 堆石子摆成一个环。现要将石子有次序地合并成一堆。规定每次只能选相邻的2堆石子合并成新的一堆，并将新的一堆石子数记为该次合并的代价。计算将 $N$ 堆石子合并成一堆的最小代价和最大代价。

---

#### 思路

   这个题和上个题只有两个地方不同，一个是从线到环，一个是同时求最大和最小代价。害，其实这样也没变什么东西，也是跟上面一样，枚举区间长度和切割点。我们可以把一个环想象成一个链，如果这个环是由 N 个元素构成，那么这个链就由 N+N 个元素构成，这么这样就能确保你每次枚举区间的时候能取到合法的值，好比你要可以合并最后一个和第一个，那么就是对应的  $dp[n][n]+dp[n+1][n+1]+cost[n][n+1]$  。不过这样最后寻找答案的时候肯定不只是 $dp[i][n]$ 了，你要从 $dp[1][n]\sim dp[n][n*2]$ 中寻找最优的答案。同时求最大和最小代价就直接开两个数组记录就可以了。

---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;
const int inf=1000005;
const int maxn=105;
int n;
int sum[maxn<<1],a[maxn<<1];
int ansmax,ansmin=12345678;
int dp[maxn<<1][maxn<<1],f[maxn<<1][maxn<<1]; //later is max , fromer is min 
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&a[i]);
		a[i+n]=a[i];
		sum[i]=sum[i-1]+a[i];
	}
	for(int i=n+1;i<=2*n;i++)
	{
		sum[i]=sum[i-1]+a[i];
	 } 
	for(int i=1;i<=n*2;i++) dp[i][i]=f[i][i]=0;
	for(int p=2;p<=n;p++)
	{
		for(int i=1;i<=2*n-p+1;i++)
		{
			int j=i+p-1;
			dp[i][j]=inf;
			for(int k=i;k<j;k++)
			{
				dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1]);
				f[i][j]=max(f[i][j],f[i][k]+f[k+1][j]+sum[j]-sum[i-1]);
			}
		}
	}
	for(int i=1;i<=n;i++) ansmax=max(ansmax,f[i][i+n-1]);
	for(int i=1;i<=n;i++) ansmin=min(ansmin,dp[i][i+n-1]);
	printf("%d\n%d",ansmin,ansmax);
	return 0;
}
```

---



### 3. [P1140 相似基因](https://www.luogu.com.cn/problemnew/solution/P1140)

---

#### 题意

   有 $A \: T \:C \: G $ 四种碱基，他们之间可以两两配对，特殊的，一个碱基也可以和空碱基配对，但是空碱基和空碱基配对是不被允许的，当不同的碱基间两两配对时，会具有一定的相似度，问给定两段序列 $s$，$t$ 能获得的最大相似度是多少。

+ 碱基配对时相似度的定义如下

|      |  A   |  C   | G    |  T   | 空   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|  A   |  5   |  -1  | -2   |  -1  | -3   |
|  C   |  -1  |  5   | -3   |  -2  | -4   |
|  G   |  -2  |  -3  | 5    |  -2  | -2   |
|  T   |  -1  |  -2  | -2   |  5   | -1   |
|  空  |  -3  |  -4  | -2   |  -1  | 非法 |

---

#### 思路

   首先用一个二维数组来存储对应的相似值表。因为对应的两段序列，我感觉这种题一般都是用 $dp[i][j]$ 来表示第一段序列从 $1\sim i$ 对应第二段序列 $1\sim j$ 所能获得的最大相似度，那么思考一下状态转移。

+ 首先可以是碱基对空碱基，这时候 $s$ 要匹配碱基，$t$ 中匹配空碱基，这样的话就从应该从 $dp[i-1][j] $转移过来，对应的转移方程为  $dp[i][j]=max(dp[i][j],dp[i-1][j]+map[s[i]][blank])$

+ 也可以是空碱基对碱基，对应的转移方程为$dp[i][j]=max(dp[i][j],dp[i][j-1]+map[blank][t[j]])$

+ 也可以是碱基对碱基，对应的转移方程为$dp[i][j]=max(dp[i][j],dp[i-1][j-1]+map[s[i]][t[j]])$

    害，想到这里我又突然懵逼了，我在想是否能确保两个序列所有的碱基都被用到，仔细想想确实是可以的，因为状态定义的就是用了前 $i$ 个碱基和前 $j$ 个碱基所获得的最大相似度。最后我们就处理一下边界就可以了，边界我们可以发现是 $dp[i][0]$ 和 $dp[0][i]$ ，这就是可以对应着空碱基和相应 $s[i]$ 和 $t[i]$ 的碱基，因此只需要用循环依次转移一遍就可以了。

---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;
const int inf=1000005;
const int maxn=105;
int n,m;
int a[maxn],b[maxn];
char s[maxn],c[maxn];
int dp[maxn][maxn];
const int map[5][5]={{5,-1,-2,-1,-3},
					 {-1,5,-3,-2,-4},
					 {-2,-3,5,-2,-2},
					 {-1,-2,-2,5,-1},
					 {-3,-4,-2,-1,0}}; //A 0 C 1 G 2 T 3 blank 4
int main()
{
	scanf("%d%s%d%s",&n,s+1,&m,c+1);
	for(int i=1;i<=n;i++)
	{
		if(s[i]=='A') a[i]=0;
		if(s[i]=='C') a[i]=1;
		if(s[i]=='G') a[i]=2;
		if(s[i]=='T') a[i]=3;
	} 
	for(int i=1;i<=m;i++)
	{
		if(c[i]=='A') b[i]=0;
		if(c[i]=='C') b[i]=1;
		if(c[i]=='G') b[i]=2;
		if(c[i]=='T') b[i]=3;
	}
	for(int i=1;i<=n;i++) dp[i][0]=dp[i-1][0]+map[a[i]][4];
	for(int i=1;i<=m;i++) dp[0][i]=dp[0][i-1]+map[4][b[i]];
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
		{
			dp[i][j]=-inf;
			dp[i][j]=max(dp[i][j],dp[i][j-1]+map[4][b[j]]);
			dp[i][j]=max(dp[i][j],dp[i-1][j]+map[a[i]][4]);
			dp[i][j]=max(dp[i][j],dp[i-1][j-1]+map[a[i]][b[j]]);
		}
	}
	printf("%d",dp[n][m]);
	return 0;
}
```

---