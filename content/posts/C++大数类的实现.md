---
title: "C++大数类的实现"
categories: [ "C++" ]
tags: [ "C++","大数类" ]
draft: false
slug: "64"
date: "2020-04-06 01:22:00"
---

# 						C++大数类设计思路

### [洛谷大数类](https://www.luogu.com.cn/problem/U111551)的评测结果(开了氧气优化)

![BigNumber.png][1]

 **这个第四个点真的优化不过去了QAQ，24W的数据，~~丧心病狂~~**


<!--more-->


### 整体构思

​	&nbsp;&nbsp;&nbsp;构造大数类名为 **BigNumber** ,首先想法是用字符串读入大数，然后将其转化为vector数组倒序分位存储的整数，然后通过一个 **len** 来记录数字的位数，便于做运算。还设计了一个标记变量，用于标记这数为正数还是负数。

### 构造函数

​	&nbsp;&nbsp;&nbsp;我用了两种构造函数，一个是无参构造函数，一个是拷贝构造函数，当然还有一个有参构造函数，但是实际过程中我没有用到。无参构造函数用于上述类成员的初始化，拷贝构造函数用于复制一个相同的大数类进行运算。有参构造函数可以用于对类成员的复制。

### 重载运算符

#### 重载 "+" 

+ 首先在类中进行了声明, `BigNumber operator + (const BigNumber &b);` ，用当前类 ***this**  来和引入的类 **b** 进行加法运算，返回值为一个 **BigNumber** 类。
+ 在类的外部进行重载的定义，先将用拷贝构造函数将当前的类 ***this** 拷贝为 **a** ，然后对 **a** 和 **b** 进行加法运算，模拟竖式，对应位相加，大于10则进位，最后去掉尾部的 0 即可。



```cpp
BigNumber BigNumber::operator + (const BigNumber &b) //重载 "+" 定义 
{
    
	BigNumber Result;
	BigNumber a(*this);
	Result.len = max(a.len,b.len)+1;
	int add=0;
	for(int i=0;i<Result.len||add!=0;i++)
	{
		int p=add;
		if(i<a.len) p+=a.v[i];
		if(i<b.len) p+=b.v[i];
		add=p/10;
		Result.v.push_back(p%10);
	}
	while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
	{
		Result.v.pop_back();
	}
	Result.len=Result.v.size();
	return Result;
    
}
```



#### 重载 "<"

​	&nbsp;&nbsp;&nbsp;因为进行减法前需要比较两个大数类的大小，我就先重载了 **<** 。思路就是先比较**a、b**两个大数的长度，长的那个肯定比较大，如果两个长度相等。从尾部开始依次向前比较，如果不相等的话，就看两个数的相对大小，大的那个肯定整体比较大。如果总是相等，到了最后，就返回相应的值表示他们相等。**这里我的返回值为int类型，用的标记是：如果两个相等，返回-1，如果前者小于后者，返回1，如果前者大于后者，返回0.**



```cpp
int  BigNumber::operator < (const BigNumber &b) // 重载 "<" 定义 
{
	BigNumber a(*this);
	
	if(a.len < b.len) return 1;
	if(a.len > b.len) return 0;
	
	for(int i=a.len-1;i>=0;i--)
	{
		if(a.v[i]!=b.v[i])
		{
			return a.v[i] < b.v[i];
		}
	}
	return -1;
}
```



####  重载 "-"

+ 首先在类中进行了声明, `BigNumber operator - (BigNumber &b);` ，用当前类 ***this**  来和引入的类 **b** 进行减法运算，返回值为一个 **BigNumber** 类。
+ 在类的外部进行重载的定义，先将用拷贝构造函数将当前的类 ***this** 拷贝为 **a** ，然后对 **a** 和 **b** 进行减法运算。如果 **a<b** ，那么我们就把 **flag** 设为 **true** ，标志得数为一个负数，然后用 **swap** 交换两个类，保证总是大数减小数。减法的话就是从前往后扫，对应位相减，如果不够减的就进行借位。如果借完位当前位置小于0了，那么就再向前借位。最后要去掉结尾多于的0.



```cpp
BigNumber BigNumber::operator - (BigNumber &b) // 重载 "-" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b==1)
	{
		swap(a,b);
		Result.flag=true;
	}
	Result.len=a.len;
	for(int i=0;i<a.len;i++)
	{
		if(a.v[i]<0)
		{
			a.v[i]+=10;
			a.v[i+1]--;
		} 
		if(a.v[i] < b.v[i]&&i<b.len)
		{
			a.v[i]+=10;
			a.v[i+1]--;
		}
		if(i<b.len)
		Result.v.push_back(a.v[i]-b.v[i]);
		else Result.v.push_back(a.v[i]);
	}
	while(Result.v[Result.len-1]==0 && Result.len > 1)
	{
		Result.v.pop_back();
		Result.len--;
	}
	return Result;
}
```



#### 重载 "*"

+ 首先在类中进行了声明, `BigNumber operator * (BigNumber &b);`，用当前类 ***this**  来和引入的类 **b** 进行乘法运算，返回值为一个 **BigNumber** 类。
+ 在类的外部进行重载的定义，先将用拷贝构造函数将当前的类 ***this** 拷贝为 **a** ，然后对 **a** 和 **b** 进行乘法运算。乘法也是模拟竖式运算，两个位数相乘对应的得数中的哪一位不难发现，因此只需要边乘边进位即可，一开始想的是所有乘完之后再进位，后来想了想运算的次序不会影响除和模的运算，所以就可以边乘边取商和模，可以少掉两层循环。算是一个小小的优化。最后要去掉结尾多于的0.



```cpp
BigNumber BigNumber::operator * (BigNumber &b) // 重载 "*"  定义 
{
	BigNumber Result;
	BigNumber a(*this);
	Result.len=a.len+b.len;
	for(int i=0;i<Result.len;i++) Result.v.push_back(0);
	for(int i=0;i<a.len;i++)
	{
		for(int j=0;j<b.len;j++)
		{
			Result.v[i+j]+=a.v[i]*b.v[j];
			Result.v[i+j+1]+=Result.v[i+j]/10;
			Result.v[i+j]%=10;
		}
	}
	while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
	{
		Result.v.pop_back();
	}
	Result.len=Result.v.size();
	return Result;
}
```



#### 重载  "/" 

+ 首先在类中进行了声明,`BigNumber operator / (BigNumber &b);`，用当前类 ***this**  来和引入的类 **b** 进行除法运算，返回值为一个 **BigNumber** 类。

+ 在类的外部进行重载的定义，先将用拷贝构造函数将当前的类 ***this** 拷贝为 **a** ，然后对 **a** 和 **b** 进行除法法运算。我选择了做除法的时候大数类中同时存储商和余数，这样可以加快效率，因为我自己的想法是把除法和取模一起处理，求得商的同时，模也能求出来。因此他们的框架肯定是相差无几的，所以我选择了一次性算出来两个，在大数类中用两个vector数组分别存商和余数，求商和余数我用的是减法的策略，分下面三种情况来讨论
  +  **a<b** ：很显然商为0，余数为a。
  +  **a==b** ：很显然商为1，余数为0。
  +  **a>b** ：这个是最难处理的，我们想想一下模拟除法的竖式运算，先在b的后面填0，让a和b的位数相同，然后再一直对a进行减法运算，将得到的数排列起来即可。里面细节还是挺多的，具体的看代码。最后要去掉结尾多于的0.

```cpp
BigNumber BigNumber::operator / (BigNumber &b) // 重载 "/" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b==1)
	{
		Result.v.push_back(0);
		for(int i=0;i<a.v.size();i++)
		{
			Result.m.push_back(a.v[i]);
		}
		return Result;
	}
	if(a<b==-1)
	{
		Result.v.push_back(1);
		Result.m.push_back(0);
		Result.len=1;
	}
	if(a<b==0)
	{
		int size = a.len-b.len;
		for(int i=size;i>=0;i--)
		{
			BigNumber p(b);
			int cnt=0;
			for(int j=1;j<=i;j++)
			{
				p.v.insert(p.v.begin(),0);
			}
			p.len = p.v.size();
			while((a-p).flag==false)
			{
				a=a-p;
				cnt++;
			}
			if(i==size)
			{
				for(int j=1;j<=size;j++)
				{
					Result.v.push_back(0);
				}
				Result.v.push_back(cnt);
			}
			else
			{
				Result.v[i] = cnt;
			}
		}
		while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
		{
			Result.v.pop_back();
		}
		Result.len=Result.v.size();
		for(int i=0;i<a.v.size();i++)
		{
			Result.m.push_back(a.v[i]);
		}
	}
	return Result;
}
```



#### 重载 "%"

​	这个和除法的类似，我们除法的其实已经求出来模了，这个只是象征性的搞一搞。QAQ。



```cpp
BigNumber BigNumber::operator % (BigNumber &b) //重载 "%" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b)
	{
		Result.v.push_back(0);
		return Result;
	}
	else
	{
		int size = a.len-b.len;
		for(int i=size;i>=0;i--)
		{
			BigNumber p(b);
			for(int j=1;j<=i;j++)
			{
				p.v.insert(p.v.begin(),0);
			}
			p.len = p.v.size();
			while((a-p).flag==false)
			{
				a=a-p;
			}
		}
		for(int i=0;i<a.v.size();i++)
		{
			Result.v.push_back(a.v[i]);
		}
	}
	return Result;
} 
```



### 其中遇到的问题

+ 在做减法的时候，会出现莫名奇怪的数据，后来发现是由于访问b数组的时候越界，而且vector数组的clear只是将数组的size置为了0，而不是将所有数据都莫抹除，而且vector通过下标访问越界还不会报错，我整个人都傻了，调了巨长时间。
+ 在做除法的时候，一度自闭。本来是只是一直减，这样效率真的巨tm慢。后来想到了用这个办法好像可以优化到 log 级别的，但是好难调试啊。。从昨天下午一直搞到现在。
+ 因为swap这个东西，好像会影响到类的源数据，所以我就多定义了几个数，分别对他们进行操作，这样就不会相互影响，虽然看起来挺丑的。

### 整体的代码实现

```cpp
/*
	Name: BigNumber Class
	Copyright: Zs
	Author: Zs
	Date: 04/04/20 09:08
	Description: A BigNumber Class
*/

#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<iostream>
#include<vector>

using namespace std;

class BigNumber
{
	private:
		vector<int> v; // 和，差，积，商 
		vector<int> m; // 余数，当无余数的时候 size 为0 
		int len;  // 数字的长度 = v.size() 
		bool flag; //是否为负数的标志 
	public:
		BigNumber();//无参构造函数 
		BigNumber(string s)  //带参数的构造函数 
		{
			*this=s;
		}
		BigNumber(const BigNumber &); // 拷贝构造函数 
		BigNumber operator = (const string s);  // 重载 "=" 声明 
		BigNumber operator = (int k);  // 重载输入的数为整型的时候的 "=" 声明
		
		int operator < (const BigNumber &b);  // 重载 "<" 声明 
		BigNumber operator + (const BigNumber &b); // 重载 "+" 声明 
		BigNumber operator - (BigNumber &b); // 重载 "-" 声明 
		BigNumber operator * (BigNumber &b);  // 重载 "*" 声明 
		BigNumber operator / (BigNumber &b); //重载 "/" 声明 
		BigNumber operator % (BigNumber &b); //重载 "%" 声明 
		
		void print() //输出函数 
		{
			if(flag)
			{
				printf("-");
				flag=false;
			}
			for(int i=v.size()-1;i>=0;i--) printf("%d",v[i]);
			printf("\n");
			for(int i=m.size()-1;i>=0;i--) printf("%d",m[i]);
		}
		
};

BigNumber::BigNumber() //无参构造函数 
{
	v.clear();
	m.clear();
	len = 0;
	flag=false;
}
BigNumber::BigNumber(const BigNumber &T) // 拷贝构造函数 
{
	v.assign(T.v.begin(),T.v.end());
	len = T.len;
	flag = T.flag;
}

BigNumber BigNumber::operator = (const string s) // 重载 "=" 定义 
{
	len = s.length();
	for(int i=0;i<len;i++)
	{
		v.push_back(s[len-1-i]-'0');
	}
	return *this;
}
BigNumber BigNumber::operator = (int k) // 重载输入的数为整型的时候的 "=" 定义 
{
	while(k)
	{
		v.push_back(k%10);
		k/=10;
		len++; 
	}
	len--;
	return *this;
}

int  BigNumber::operator < (const BigNumber &b) // 重载 "<" 定义 
{
	BigNumber a(*this);
	
	if(a.len < b.len) return 1;
	if(a.len > b.len) return 0;
	
	for(int i=a.len-1;i>=0;i--)
	{
		if(a.v[i]!=b.v[i])
		{
			return a.v[i] < b.v[i];
		}
	}
	return -1;
}
BigNumber BigNumber::operator + (const BigNumber &b) //重载 "+" 定义 
{
	
	BigNumber Result;
	BigNumber a(*this);
	Result.len = max(a.len,b.len)+1;
	int add=0;
	for(int i=0;i<Result.len||add!=0;i++)
	{
		int p=add;
		if(i<a.len) p+=a.v[i];
		if(i<b.len) p+=b.v[i];
		add=p/10;
		Result.v.push_back(p%10);
	}
	while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
	{
		Result.v.pop_back();
	}
	Result.len=Result.v.size();
	return Result;
	
}
BigNumber BigNumber::operator - (BigNumber &b) // 重载 "-" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b==1)
	{
		swap(a,b);
		Result.flag=true;
	}
	Result.len=a.len;
	for(int i=0;i<a.len;i++)
	{
		if(a.v[i]<0)
		{
			a.v[i]+=10;
			a.v[i+1]--;
		} 
		if(a.v[i] < b.v[i]&&i<b.len)
		{
			a.v[i]+=10;
			a.v[i+1]--;
		}
		if(i<b.len)
		Result.v.push_back(a.v[i]-b.v[i]);
		else Result.v.push_back(a.v[i]);
	}
	while(Result.v[Result.len-1]==0 && Result.len > 1)
	{
		Result.v.pop_back();
		Result.len--;
	}
	return Result;
}

BigNumber BigNumber::operator * (BigNumber &b) // 重载 "*"  定义 
{
	BigNumber Result;
	BigNumber a(*this);
	Result.len=a.len+b.len;
	for(int i=0;i<Result.len;i++) Result.v.push_back(0);
	for(int i=0;i<a.len;i++)
	{
		for(int j=0;j<b.len;j++)
		{
			Result.v[i+j]+=a.v[i]*b.v[j];
			Result.v[i+j+1]+=Result.v[i+j]/10;
			Result.v[i+j]%=10;
		}
	}
	while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
	{
		Result.v.pop_back();
	}
	Result.len=Result.v.size();
	return Result;
}

BigNumber BigNumber::operator / (BigNumber &b) // 重载 "/" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b==1)
	{
		Result.v.push_back(0);
		for(int i=0;i<a.v.size();i++)
		{
			Result.m.push_back(a.v[i]);
		}
		return Result;
	}
	if(a<b==-1)
	{
		Result.v.push_back(1);
		Result.m.push_back(0);
		Result.len=1;
	}
	if(a<b==0)
	{
		int size = a.len-b.len;
		for(int i=size;i>=0;i--)
		{
			BigNumber p(b);
			int cnt=0;
			for(int j=1;j<=i;j++)
			{
				p.v.insert(p.v.begin(),0);
			}
			p.len = p.v.size();
			while((a-p).flag==false)
			{
				a=a-p;
				cnt++;
			}
			if(i==size)
			{
				for(int j=1;j<=size;j++)
				{
					Result.v.push_back(0);
				}
				Result.v.push_back(cnt);
			}
			else
			{
				Result.v[i] = cnt;
			}
		}
		while(Result.v[Result.v.size()-1]==0 && Result.v.size() > 1)
		{
			Result.v.pop_back();
		}
		Result.len=Result.v.size();
		for(int i=0;i<a.v.size();i++)
		{
			Result.m.push_back(a.v[i]);
		}
	}
	return Result;
}
BigNumber BigNumber::operator % (BigNumber &b) //重载 "%" 定义 
{
	BigNumber Result;
	BigNumber a(*this);
	if(a<b)
	{
		Result.v.push_back(0);
		return Result;
	}
	else
	{
		int size = a.len-b.len;
		for(int i=size;i>=0;i--)
		{
			BigNumber p(b);
			for(int j=1;j<=i;j++)
			{
				p.v.insert(p.v.begin(),0);
			}
			p.len = p.v.size();
			while((a-p).flag==false)
			{
				a=a-p;
			}
		}
		for(int i=0;i<a.v.size();i++)
		{
			Result.v.push_back(a.v[i]);
		}
	}
	return Result;
} 
int main()
{
//	freopen("test.in","r",stdin);
//	freopen("test.out","w",stdout);
	string s1,s2;
	cin>>s1>>s2;
	BigNumber a,b,c,e,f,k,j;
	a=s1,b=s2;
	e=s1,f=s2;
	k=s1,j=s2;
	c=a+b;
	c.print();
	c=a-b;
	c.print();
	c=k*j;
	c.print();
	c=e%f;
	c.print();
	return 0;
}
```


  [1]: https://www.zzsqwq.cn/usr/uploads/2020/08/4200651250.png