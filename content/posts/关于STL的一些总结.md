---
title: "关于STL的一些总结"
categories: [ "STL" ]
tags: [ "数据结构","STL" ]
draft: false
slug: "28"
date: "2020-02-16 00:55:00"
---

## 前言

STL之前只会用 stack 和 queue ，set 和 map 啥的也不太会用。学习一下。


<!--more-->


---

## 队列(queue)

### 概述

队列是一种特殊的线性表，是一种先进先出（FIFO）的数据结构。它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作。进行插入操作的端称为队尾，进行删除操作的端称为队头。队列中没有元素时，称为空队列。

### 用法

首先使用之前需要声明头文件 `#include<queue>` ，通过 `queue<typename> q` 的形式来进行定义队列，上述为定义了一个队列元素类型为 typename 的队列，队列名称为 q，typename可以为C++原有数据类型，例如int，char，string这些，也可以是自定义的结构体类型等。

### 主要函数及用途

使用下述函数都是用类似于 队列名称.函数名()  的形式，好比pop函数就是 q.pop()

> 1. push(x)   将元素x从队尾入队
> 2. front( ) & back( )    分别为获取队首元素和队尾元素，使用的时候必须确保队列不为空
> 3. pop( )  弹出队首元素，使用的时候必须确保队列不为空
> 4. empty( )  判断队列是否为空，空返回true，不空返回false
> 5. size( ) 查询队列中有多少个元素

### 应用实例

```cpp
#include<cstdio>
#include<iostream>
#include<queue>

using namespace std;

queue<int> q;
int main()
{
	q.push(1);
	if(!q.empty()) printf("%d\n",q.front()); //
	q.push(2);
	if(!q.empty()) printf("%d\n",q.back());
	printf("%d\n",q.size());
	if(!q.empty()) q.pop();
	if(q.empty()) printf("queue is empty\n");
	else printf("queue is not empty\n");
	if(!q.empty()) q.pop();
	if(q.empty()) printf("queue is empty\n");
}
/*
运行结果
1
2
2
queue is not empty
queue is empty
*/
```

---

</br>

## 栈(stack) 

### 概述

与队列相对应，是一种先进后出（FILO）的数据结构，限定仅在表尾进行插入和删除操作的线性表。这一端被称为栈顶，相对地，把另一端称为栈底。向一个栈插入新元素又称作进栈、入栈或压栈，它是把新元素放到栈顶元素的上面，使之成为新的栈顶元素；从一个栈删除元素又称作出栈或退栈，它是把栈顶元素删除掉，使其相邻的元素成为新的栈顶元素。

### 用法

首先使用之前需要声明头文件 `#include<stack>` ，通过 `stack<typename> s` 的形式来进行定义栈，上述为定义了一个队列元素类型为 typename 的栈，栈名称为 s，typename可以为C++原有数据类型，例如int，char，string这些，也可以是自定义的结构体类型等。

### 主要函数及用途

使用下述函数都是用类似于 栈名称.函数名()  的形式，好比pop函数就是 s.pop()

> 1.  push(x)  将元素x压栈
> 2.  pop( )  将栈顶元素出栈，使用时确保栈不为空
> 3.  top( ) 获取栈顶元素的值，使用时要确保栈不为空
> 4.  size( ) 返回栈中元素的个数
> 5.  empty( ) 查询栈是否为空，空返回true，不空返回false

### 应用实例

```cpp
#include<cstdio>
#include<iostream>
#include<stack>

using namespace std;

stack<int> s;

int main()
{
	s.push(1);
	s.push(2);
	printf("Now the top element is %d\n",s.top()); //Now the top element is 2
	printf("Now the stack size is %d\n",s.size()); //Now the stack size is 2
	if(!s.empty()) s.pop(); //元素2 弹出栈
	printf("Now the top element is %d\n",s.top()); //Now the top element is 1
	printf("Now the stack size is %d\n",s.size()); //Now the stack size is 1
	if(s.empty()) printf("stack is empty\n"); 
	else printf("stack is not empty\n"); //stack is not empty
	if(!s.empty()) s.pop(); //元素1 弹出栈
	if(s.empty()) printf("stack is empty\n"); //stack is empty
	else printf("stack is not empty\n");
}
/*
运行结果
Now the top element is 2
Now the stack size is 2
Now the top element is 1
Now the stack size is 1
stack is not empty
stack is empty
*/
```

---

</br>

## 映射(map)

### 概述

map是STL的一个关联容器，提供一对一的数据处理能力，可以建立两个数据之间一一映射关系，map的定义需要关键字和存储值两个参数，我们可以通过关键字来查找对应的存储值（感觉类似于下标可以为任何类型的数组）吧，因为map的底层实现为红黑树（虽然我没学过），所以具有自动排序功能，也就是说map内部有序。

### 用法

使用之前声明头文件 `#include<map>` ，通过 `map<typename,typename> m`  的形式来定义映射，如果我们要建立string和int这两个类型之间的一一映射，就可以写成 `map<string,int> m` ，我们可以通过关键字string来查找对应的int值。下述的讲述我们用这个m这个映射来进行。

### 迭代器

迭代器就是类似于指针吧，我们可以通过`map<string,int>::iterator it` ，来定义一个对应映射的迭代器，他能够用来指向map中的元素，通过它们我们可以对map执行定点删除，遍历等操作。

### 主要函数及用途

#### 1. 插入数据

+ 通过insert函数进行插入
  + `m.insert(pair<string,int>("zs",1))`
  + `m.insert(make_pair("zs",2)) `
+ 通过类似于数组的形式插入
  + `m["zs"] = 2`

上述两种形式有一定的区别，因为集合中元素是唯一的，用insert函数插入的时候，如果已经有相应的关键字，那么就不会插入。而如果用类似于数组的方式进行插入，就会覆盖原关键字对应的值。

```cpp
map<string,int> m;
int main()
{
	m.insert(pair<string,int>("zs",1));
	printf("%d ",m["zs"]);
	m.insert(make_pair("zs",2));
	printf("%d ",m["zs"]);
	m["zs"]=2;
	printf("%d ",m["zs"]);
	/*
	输出结果  1 1 2 
	*/
}
```

#### 2. 查找数据

通过find函数来查找关键字在map中的位置，如果找到了的话就返回对应的迭代器，如果没有找到的话就返回尾部的迭代器（end函数返回的值）。

```cpp
map<string,int> m;
int main()
{
	map<string,int>::iterator it;
	m["zs"]=1;
	it=m.find("zs");
	cout<<it->first<<" "<<it->second;
	/*
	输出结果 zs 1 
	*/ 
}
```

#### 3. 删除数据

+ 清空map，可以使用clear函数。
+ 删除特定元素
  + 先用find函数找到特定元素的迭代器，通过erase函数清除。
  + 直接通过相应关键字清除。
+ 删除一串序列，通过erase(起始迭代器，终点迭代器) 来实现。

```cpp
map<string,int> m;

int main()
{
	map<string,int>::iterator it;
	m["zs"]=1;
	it=m.find("zs");
	m.erase(it);
	it=m.find("zs");
	if(it==m.end())
	printf("Not find zs\n");
	m["zs"]=2;
	m.erase("zs");
	it=m.find("zs");
	if(it==m.end())
	printf("Not find zs");
	/*
	输出结果 Not find zs 
		    Not find zs
	*/ 
}
```

#### 4. 其他

> 1. count("关键字")  查询相应关键字在map中是否出现过，出现过返回1，没出现返回0
> 2. empty( )  判断是否为空，空返回true，不空返回false
> 3. begin( ) & end( ) 分别为返回头和尾迭代器，配合迭代器可实行遍历
> 4. iterator->first & iterator->second 分别对应相应迭代器指向的元素的关键字和值

---

</br>

## 集合(set)

### 概述

set为一个容器，用来存储同一数据类型的数据，并且能从一个数据集合中取出数据，在set中每个元素的值都唯一（集合的唯一性），并且内部能根据元素的值自动进行排序。

### 用法

使用之前需要声明头文件 `#include<set>` ，通过 `set<typename> s` 来定义一个存储数据类型为typename的集合，名字叫做s。下述实例用此做基础。

### 迭代器 

通过`set<typename>::iterator it`，可以来定义一个相应的set的迭代器，用来遍历和指向其中元素。

### 主要函数及用途

#### 1. 插入数据

+ 插入特定元素可以通过 `s.insert(3)` 插入对应键值，返回值为pair<set<int>::iterator,bool> ，后续bool变量标志是否成功，如果元素3已经存在，那么bool值为false，迭代器对应的是该元素在其中的位置，如果元素不存在其中，返回true，并且返回的迭代器对应的在集合中位置
+ 插入一个区间的元素，例如有整数数组a ，可以用 `s.insert(a,a+3)` ，可以将a中的 a[0] a[1] a[2] 插入到set中。

#### 2. 删除数据

删除和map非常像，也是三种。具体可参考map讲解。

#### 3. 查找元素

也是可以通过find函数，也是和map非常的像~

#### 4. 其他

> 1. begin() & end( ) 返回头尾迭代器，注意尾迭代器是尾元素的后一位。
> 2. clear( ) 清除set容器中所有元素
> 3. empty( ) 判断set容器是否为空，为空则返回true，不空返回false
> 4. size( ) 返回当前set容器中元素的个数
> 5. rebegin( ) & rend( ) 返回尾和头迭代器，配合reverse_iterator可以反序遍历set

---



## 关于vector和string等

+ vector好的学习文章 ： [C++ vector容器浅析](https://www.runoob.com/w3cnote/cpp-vector-container-analysis.html)

+ string好的学习文章  ： [C++ STL(一)介绍及string](https://www.cnblogs.com/fzxiaoyi/p/12083144.html)