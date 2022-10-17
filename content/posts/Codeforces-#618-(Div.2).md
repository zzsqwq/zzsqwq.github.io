---
title: "Codeforces #618 (Div.2)"
categories: [ "ACM" ]
tags: [ "Codeforces" ]
draft: false
slug: "22"
date: "2020-02-10 00:39:00"
---

## A. [Non-zero](https://codeforces.com/contest/1300/problem/A)

### 题意

   给出一段含有 $n$ 个数的序列 $a$ ，可以对其中任何数加一，问最少操作多少次让每一个数和序列和都不为0。


<!--more-->


------

### 思路

   输入的时候如果输入的是 $0$ 就将答案加一，最后如果序列和为 $0$ 的话答案加一。

---

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

int t,n,sum,p,ans;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		sum=ans=0;
		scanf("%d",&n);
		for(int i=1;i<=n;i++)
		{
			scanf("%d",&p);
			if(p==0)
			{
				ans++;
				sum+=1;
			}
			else sum+=p;
		}
		if(sum==0) printf("%d\n",ans+1);
		else printf("%d\n",ans);
	}
}
```

---



##  B. [Assigning to Classes](https://codeforces.com/contest/1300/problem/B)

### 题意

   将 $2n$ 个数分成个奇数序列，问两个奇数序列的中位数之差最小为多少。

---

### 思路

   直接就将序列排序然后输出中间两个数之差即可。

---

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn = 100005;
int t,n,a[maxn<<1];
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d",&n);
		int p=n<<1;
		for(int i=1;i<=p;i++)
		{
			scanf("%d",&a[i]);
		}
		sort(a+1,a+1+p);
		printf("%d\n",abs(a[n]-a[n+1]));
	}
	return 0;
}
```

---



## C. [Anu Has a Function](https://codeforces.com/contest/1300/problem/C)

### 题意

   给出函数 $f: f(x,y)=(x|y)-y $ ，给出序列 $a$，序列 $a$ 中含有 $n$ 个数，可以表示为$[a_1,a_2\cdots,a_n ]$ ，定义 $x=f(f(...f(f(a_1,a_2),a_3),...a_{n-1}),a_n)$ ，你可以对序列 $a$ 中元素进行重排，求使得 $x$ 最大的序列 $a$ 。如果有多种情况，输出一种即可。

### 思路

+ 第一种思路是因为 $f(x,y)=(x|y)-y$ ，我们可以发现对于经过这样的运算之后，如果 $x$ 的某一位是1，如果 $y$ 的相应位是0，那么运算出来的 $f(x,y)$ 对应位就是1，如果 $y$ 对应位是1，那么运算出来就是0。那么对于 $x$ 的计算过程中的每一位这个规律都是适应的。因此我们只需要将位数从高到低依次扫一遍，如果这个位数为1的情况在序列所有元素中只出现了一次，那么就将唯一出现1的那个数放到第一位即可。

+ 第二种思路
  $$
  \because f(x,y)=(x|y) - y {\Longleftrightarrow} f(x,y) = x\&({\sim} y) 
  \therefore x=(a_1)\&({\sim}a_2)\&({\sim} a_3){\cdots}({\sim}a_n)
  $$
     我们发现后面其实都是可交换的，所以第一个只有第一个是起决定作用的，那么我们就可以处理一个前缀和后缀的 and 数组，这样我们就可以 $O(1)$ 的计算出后面那部分，然后遍历序列 $a$ 找到最合适的 $a_1$。

### 代码实现

#### 第一种思路 

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=100005;
int n,a[maxn],maxk;
int cnt;
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&a[i]);
		maxk=max(maxk,a[i]);
	}
	int p=1,k=0;
	while(p<=maxk)
	{
		k++;
		p<<=1;
	}
	for(int i=k;i>=0;i--)
	{
		cnt=0;
		for(int j=1;j<=n;j++)
		{
			if(a[j]&(1<<i))
			{
				cnt++;
				if(cnt==1) swap(a[j],a[1]);
			}
		}
//		printf("%d %d\n",i,cnt);
		if(cnt==1)
		{
			for(int j=1;j<=n;j++)
			{
				printf("%d ",a[j]);
			}
			return 0;
		}
	}
	for(int i=1;i<=n;i++)
	{
		printf("%d ",a[i]);
	}
	return 0;
}
```

#### 第二种思路

```cpp
#include<cstdio>
#include<algorithm>
#include<cstdio>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=100005;
int n,a[maxn];
int ans;
int pre[maxn],suf[maxn]; //pre is prefix,suf is suffix
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&a[i]);
		a[i]=~a[i];
		if(i==1) pre[i]=a[i];
		else pre[i]=pre[i-1]&a[i];
	}
	suf[n]=a[n];
	for(int i=n-1;i>=1;i--)
	{
		suf[i]=suf[i+1]&a[i];
	}
	for(int i=1;i<=n;i++)
	{
		int p=a[i];
		p=~a[i];
		if(i==1)
		{
			int now=suf[i+1]&p;
			ans=max(ans,now);
		}
		else if(i==n)
		{
			int now=pre[i-1]&p;
			if(now>ans)
			{
				swap(a[i],a[1]);
				ans=now;
			}
		}
		else 
		{
			int now=pre[i-1]&suf[i+1]&p;
			if(now>ans)
			{
				swap(a[i],a[1]);
				ans=now;
			}
		}
	}
	for(int i=1;i<=n;i++)
	{
		printf("%d ",~a[i]);
	}
	return 0;
}
```

---



## D. [Aerodynamic](https://codeforces.com/contest/1299/problem/B)

### 题意

   给定一个凸多边形 $P$ 的所有顶点，可以将凸多边形沿向量 $(x,y)$ 平移，我们定义多边形 $T$ 是所有 $P$ 平移到与原点有交点后所构成的点集所形成的图形（我知道这句话有点绕，我实在是解释不明白，实在不行康康原题吧）。那么问这个 $T$ 是否是和 $P$ 相似的，如果是输出YES，不是输出NO。

***

### 思路

   就是判断这个图形是不是中心对称图形就行了，证明还不会，暂且放一下，会了再写QAQ..

---

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=100005;
int n;
int x[maxn],y[maxn];
bool check()
{
	int p=n/2;
//	printf("%d",p);
	int x1=x[1]+x[1+p];
	int y1=y[1]+y[1+p];
	for(int i=2;i<=p;i++)
	{
		if(x1!=x[i]+x[i+p]||y1!=y[i]+y[i+p])
		{
			return false;
		}
	}
	return true;
}
int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d%d",&x[i],&y[i]);
	}
	if(n&1)
	{
		printf("NO");
	}
	else
	{
		if(check())
		{
			printf("YES");
		}
		else printf("NO");
	}
	return 0;
}
```

---
