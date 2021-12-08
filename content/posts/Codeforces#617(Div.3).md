---
title: "Codeforces#617(Div.3)"
categories: [ "ACM" ]
tags: [ "Codeforces" ]
draft: false
slug: "17"
date: "2020-02-05 23:18:00"
---

### A. [Array with Odd Sum](https://codeforces.com/contest/1296/problem/A)

---

#### 题意

​	&nbsp; &nbsp; &nbsp; 给出包含 **n** 个正整数的序列 **a** ，你可以把任何一个元素 $a_i$ ，赋值给另一个元素 $a_j$ ($i\neq j$) ，问通过任意此操作能否将序列 **a** 的和变为奇数。可以输出 **YES** ,不可以输入 **NO**.

---

#### 思路

​	&nbsp; &nbsp; &nbsp; 首先当起始和为奇数的时候，就直接可输出 **YES** 了，如果是偶数的话，我们可以发现，如果序列元素中同时包含奇数和偶数，那么就是可以的，否则不可以。


<!--more-->


---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>

using namespace std;

int t,n,flag,sum,p,flag1,flag2;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		flag=false;
		flag2=flag1=false;
		sum=0;
		scanf("%d",&n);
		for(int i=1;i<=n;i++)
		{
			scanf("%d",&p);
			sum+=p;
			if(p%2==1) flag1=true;
			if(p%2==0) flag2=true;
		}
		if(flag1&&flag2) flag=true;
		if(sum%2==1) printf("YES\n");
		else 
		{
			if(flag) printf("YES\n");
			else printf("NO\n");
		}
	}
}
```

---



### B. [Food Buying](https://codeforces.com/contest/1296/problem/B)

---

#### 题意

​	&nbsp; &nbsp; &nbsp; 初始有 **s** 个货币，每次花费 **x** 个货币会返还 $\lfloor{\frac{x}{10}}\rfloor$ 个货币，问最多共能花费多少货币。

---

#### 思路

​	&nbsp; &nbsp; &nbsp; 贪心即可。剩余的货币一直除10累加，注意最终剩余不足10的处理。

---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>

using namespace std;

int t,s;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d",&s);
		{
			int p=s;
			int now=0;
			while(p)
			{
				if(p<10) break;
				now=p/10;
				s+=now;
				p%=10;
				p+=now;
			}
		}
		printf("%d\n",s);
	}
}

```

---



### C. [Yet Another Walking Robot](https://codeforces.com/contest/1296/problem/C)

---

#### 题意

​	&nbsp; &nbsp; &nbsp; 一个机器人初始在 $(0,0)$ 点，规定 **'L'**  ，**'R'** ，**'U'** ，**'D'**  分别对应向左，向右，向上和向下。给定一段包含上述字母的序列 **s** ，机器人遵循指引序列移动。如果删除一段连续序列可使得机器人最终到达终点不变，问删除的最短序列的起始和终点为多少。

---

#### 思路

​	&nbsp; &nbsp; &nbsp; 想了半天想了错误的解法。。一直在考虑 **L** 和 **R** 数相等，**U** 和 **D** 相等，通过这个方法来找序列。看了题解才发现是通过坐标来看。我们可以开一个map记录坐标和步数的关系，从左到右扫序列，如果没有到达过这个坐标，就记录当前是第几次移动到达这个坐标的，如果到达过的话，就看上一次到达这个坐标时的步数，计算他们的序列长度，如果小于计算的就更新答案。因为是需要找最小的，因此只需要记录上一次到达的步数即可。

---

#### 代码实现

---

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<map>

using namespace std;

const int maxn=200005;
int t,n;
char s[maxn];
bool flag;
int main()
{
	scanf("%d",&t);
	while(t--)
	{
		scanf("%d",&n);
		int l=-1,r=n;
		scanf("%s",s+1);
		pair<int,int> pos; //first为x second为y
		map<pair<int,int>,int> last;  
		pos.first=pos.second=0;
		last[pos]=0;
		for(int i=1;i<=n;i++)
		{
			if(s[i]=='L') pos.first--;
			if(s[i]=='R') pos.first++;
			if(s[i]=='U') pos.second++;
			if(s[i]=='D') pos.second--;
/*			
			if(i==2)
			{
				printf("%d %d\n",pos.first,pos.second);
			}
*/			
			if(last.count(pos)!=0)
			{
				int p=i-last[pos];
//				if(i==2) printf("%d %d\n",i,last[pos]);
				if(p<r-l)
				{
					l=last[pos];
					r=i;	
				}	
//				if(i==2 )printf("%d %d\n",l,r);
			}
			last[pos]=i;
		}
		if(l==-1)
		{
			printf("-1\n");
		}
		else
		{
			printf("%d %d\n",l+1,r);
		}
	}
	return 0;
}
```

---



### D. [Fight with Monsters](https://codeforces.com/contest/1296/problem/D)

---

#### 题意

​	&nbsp; &nbsp; &nbsp; 由你先手和对手轮流击打 $n$ 个血量为 $h_i$ 的小怪兽，你可以对怪物造成 $a$ 点伤害，对手可以造成 $b$ 点伤害。你有 $k$ 次机会使对手跳过他的回合。当小怪兽血量 $h\le0$ 时视为被击杀，当你击杀怪兽，你获得一分，当对手击杀，你不得分。求你最多能获得多少分数。

---

#### 思路

​	&nbsp; &nbsp; &nbsp; 先看一下对于每个怪兽我们要击杀需要花费多少机会，你和对手一个回合会击杀怪兽 $a+b$ 点血量，因此你可以一直将回合进行到怪兽血量小于$a+b$，接下来我们可以分两种情况讨论。

+ 怪兽血量为0，那么我们就需要回溯对手最后一个回合，然后需要使用的机会就是 $\lceil\frac{h_i}{a}\rceil$ 次

+ 怪兽血量不为0，我们需要使用的机会就是 $\lceil\frac{h_i}{a}\rceil-1$ 次，注意这里不能直接写 $\lfloor\frac{h_i}{a}\rfloor$ 次，因为如果 $h_i$ 刚好能被 $a$ 整除，后面这个写法就错了。

​	&nbsp; &nbsp; &nbsp; 计算出了每个怪兽需要花费的机会那么就好做了，就变成了一个贪心问题，我们去尽可能得击杀需要的机会少的，当机会消耗完毕，得到的就是答案了。

---

#### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>

using namespace std;

const int maxn=200005;
int n,a,b,k;
int f[maxn];
int cmp(int a,int b)
{
	return a<b;
}
int h[maxn],ans;
int main()
{
	scanf("%d%d%d%d",&n,&a,&b,&k);
	int p=a+b;
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&h[i]);
		h[i]%=p;
		if(h[i]==0)
		{
			h[i]+=b;
			f[i]=ceil((double)h[i]/a);
		}
		else f[i]=ceil((double)h[i]/a)-1;
	}
	sort(f+1,f+1+n,cmp);
	for(int i=1;i<=n;i++)
	{
		if(k-f[i]<0) break;
		ans++;
		k-=f[i];
	}
	printf("%d\n",ans);
}
```

---
