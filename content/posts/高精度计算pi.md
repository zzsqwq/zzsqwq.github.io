---
title: "高精度计算pi"
categories: [ "数据结构" ]
tags: [ "数据结构" ]
draft: false
slug: "67"
date: "2020-04-20 01:25:00"
---

## 高精度计算PI值

### 题目描述

​    使用双向链表作为存储结构，请根据用户输入的一个整数（该整数表示精确到小数点后的位数，可能要求精确到小数点后 **500** 位），高精度计算PI值。**提示：可以利用反三角函数幂级展开式来进行计算。**

<!--more-->

### 解题思路

#### 求PI的算法

​    首先这道题是要求必须使用双向链表作为存储结构的，这个需要注意，而且也不能用数组计算完了之后挨个赋值给链表的每个节点，~~这是耍赖~~ 。

​    那么我们开始再想，用什么公式来求 **PI** 呢？这是一个问题。先没管题目的提示，我去百度了一通，发现了一个很神奇的算法，用三行就可以计算到圆周率小数点后800+位。

```cpp
#include<cstdio>

using namespace std;

long a=1000,b,c=2800,d,e,f[2801],g;
int main(){
    for(;b-c;) f[b++]=a/5;
    for(;d=0,g=c*2;c-=14,printf("%.3d",e+d/a),e=d%a)
    for(b=c;d+=f[b]*a,f[b]=d%--g,d/=g--,--b;d*=b);
}
```

​    实验了一下发现居然真的是，而且效率还挺高的？看了一会实现的原理一直没看懂，作罢。

​    最后发现是找不到什么除了幂级展开还有啥高效率的算法了好像，还是考虑题目提示的 **三角函数幂级展开**。然后继续在网上搜索了一下，在学长的一个博客里发现了公式。
$$
f_{i} =
        \begin{cases}
            1  & {i=1}\\\\
            f_{i-1}\times \frac{i-1}{2\times i-1} & {i>1}
        \end{cases}
$$

​    那么拿到了这个式子，我们就可以分析一下，怎么和链表结合起来做了。

#### 链表的设计

​    双向链表，也就是每个节点有一个数据域，有前和后两个指针。我考虑到我们做加法和乘法是需要从后往前做，除法是需要从前往后做。因此需要双向遍历，我又添加了一个尾指针，记录当前链表的尾节点的地址，方便从后往前遍历，头节点可以保证从前往后遍历。设计如下：

```cpp
typedef struct node
{
    int data;
    struct node *nxt; 
    struct node *pre;
    struct node *tail;
    node()   //构造函数，用于初始化
    {
        nxt = NULL;
        pre = NULL;
           tail = this;
        data = 0;
    }
}
```

​    数据域就用来存储每一位数字，好比 **3.1415926** 就从第一个节点到第八个节点依次存 **31415926** 。

#### 乘法的实现

​    另出一个函数，函数声明类似于 `void Multi(List L,int k)` 

​    乘法我们模拟竖式的乘法运算，考虑到这是一个 **高精度大数 * 低精度整数** ，因此我们只需要从尾部到头部依次对每一位做乘法即可，考虑到进位问题，可以有两种办法

+ 可以是先做完乘法，然后再回到尾部，再从尾到头依次处理进位，这样的好处是这两种操作分隔开了，操作起来难度不大，也比较好想。
+ 可以是边做乘法边进位，我们定义一个 `temp` 用来存储低位到高一位的进位，这里要注意的是，对于某一位的操作不是先加上进位再做乘法，是先做乘法，再加低位的进位。

​    但是有一个问题我们需要注意，就是好比 **52 * 3** ，这时候原来的两位数变成三位数了，因此需要我们在头节点和第一个节点之间增加新的节点，并且可能增加的不只是一个，只要 `temp` 这个进位大于等于10就需要一直创建新的节点来保证进位。

#### 除法的实现

​    另出一个函数，函数声明类似于 `void Division(List L,int k)` 

​    除法也是模拟竖式运算，这个是从头到尾进行处理，这一位的数据做完除法，余数作为下一位的**“进位”** ，这里可以边做边 **“进位”** 。这里这个进位不是乘法的那种进位，注意区分。

​    需要注意的是最后可能有除不尽的情况，这样我们就可以一直在尾部插入节点，然后处理一直处理数据到**最大位数** 或者 **到 “进位” 为0为止** 。最后不要忘记重置一下尾指针，指向新的尾节点。

#### 加法的实现

​    函数声明类似于 `void Sum(List L,List p)` 。

​    加法的实现也是需要从后往前遍历，然后依次对每一位做加法。先取两个链表的尾节点出来，然后依次向前遍历相加，我们可以把相加的答案放在前面那个链表里面，注意这样是不需要返回值的，因为链表内部是通过地址索引的，我们改变的就是传入链表的值。

#### 最后的整合

​    最后我们总共需要一个和链表，一个 $f_{i}$。 和链表用于计算所有式子的累加和，而 $f_{i-1}$ 其实就是 $f_i$ 的上个阶段 。我们每次把 $f_i$ 和 和链表进行累加即可。 

​    最后再对**和链表乘2** 即可。

### 代码实现

```cpp
#include<cstdio>
#include<cstring>
#include<cmath>
#include<iostream>
#include<algorithm>

using namespace std;

const int maxsize = 600;  // 设定求小数点后多少位
typedef struct node  // 双向链表的结构体
{

    int data;
    struct node *nxt;
    struct node *pre;
    struct node *tail;
    node()              //构造函数，用于初始化
    {
        data = 0;
        tail  = this;
        nxt = NULL;
        pre = NULL;
    }

}Node,*List;

void Mult(List Head,int k);      // 对链表每一位 *k
void Divi(List Head,int k);      // 对链表每一位 ÷k
void Sum(List a,List b);         // 对两个链表的数求和，所得数放在前面链表中
void InitList(List &L);          // 初始化链表，并把第一个节点值设为 1
void InitSum(List &L);           // 初始化最后存和的链表，并把第一个节点置为1
void Output(List L,int k);       // 输出一个链表，保留 k 位小数


int main()
{
    int n = 0;
    scanf("%d",&n);             
    List Head,S;
    InitList(Head); 
    InitSum(S);
    for(int i=2;i<=3000;i++)     //计算pi值
    {
        Mult(Head,i-1);
        Divi(Head,2*i-1);
        Sum(S,Head);
    }

    Mult(S,2);
    Output(S,n);
    return 0;
}

void Mult(List Head,int k)    // 对以Head为头节点的链表中的每一位做乘法
{
    List p = Head->tail;      // 先把指针指向链表的末尾，方便从后往前做乘法
    while(p != Head)          // 从前往后开始算乘法
    {
        p->data *= k;
        p = p->pre;
//      printf("I have done Multi.\n");
    }
    p = Head->tail;
    while(p != Head->nxt)     //开始处理进位 
    {
        p->pre->data += p->data /10;
        p->data %= 10;
        p = p->pre;
    }
    while( p->data > 10)      //一直处理最高位的进位 
    {
        List s = (List)malloc(sizeof(Node));
        s->data = p->data/10;
        p->data %= 10;
        s->pre = Head;
        s->nxt = p;
        p->pre = s;
        Head->nxt = s;
        p = s;
    }
}

void Divi(List Head,int k) // 对链表每一位除k
{
    int temp = 0,depth = 0;     //temp用于进位计算 ，depth 用于计算链表长度
    List p = Head->nxt;
    List t;                     // 存尾部节点
    while(p != NULL)            //模拟做除法
    {
        depth++; 
        p->data += temp*10; 
        temp = p->data % k;
        p->data /= k;
        t = p;
        p = p->nxt;
    }
    p = t;
    while(temp!=0 && depth <= maxsize)  // 如果除不尽，就一直往后拓展节点，但注意不要超过最大位数
    {
//      printf("I have done Division!\n");
        depth++;
        List s = (List)malloc(sizeof(Node));
        s->data = temp*10;
        s->nxt = NULL;
        s->pre = p;
        temp = s->data % k;
        s->data /= k;
        p->nxt = s;
        p = s;
    }
    Head->tail = p;
}

void Sum(List a,List b)  // 对两个链表的数求和，所得数放在前面链表中
{
    List p = a->tail,k = b->tail;  // 先指向各自的尾部，开始从前往后加
    while(p!=a && k!=b)  // 遍历到有一个到头节点为止
    {
        p->data += k->data;
        p->pre->data += p->data / 10;
        p->data %= 10;
        p = p->pre;
        k = k->pre;
    }
}

void InitList(List &L) // 初始化链表，并把第一个节点值设为 1
{
   L = (List)malloc(sizeof(Node));
   L->data = 0;
   List s = (List)malloc(sizeof(Node));
   s->data = 1;
   s->pre = L;
   L->nxt = s;
   L->pre = NULL;
   L->tail = s;
   s->nxt = NULL; 
}

void InitSum(List &L) // 初始化最后存和的链表，并把第一个节点置为1
{
    L = (List)malloc(sizeof(Node));
    L->nxt = NULL;
    L->pre = NULL;
    L->data = 0;
    List p = L;
    int depth = 0;
    while(depth <= maxsize)
    {
        List s = (List)malloc(sizeof(Node));
        s->data = 0;
        s->pre = p;
        p->nxt = s;
        s->nxt = NULL;
        p = s;
        depth++;
    }
    L->nxt->data = 1;
    L->tail = p;
}

void Output(List L,int k) //输出一个列表，保留 k 位小数
{
    List p = L->nxt;
    printf("%d.",p->data);
    p = p->nxt;
    int t = 0;
    while(p != NULL && t<k) 
    {
        t++;
        printf("%d",p->data);
        p = p->nxt;
    }
    printf("\n");
}
```

### 参考链接

+ [数据结构实验：高精度计算圆周率](https://blog.csdn.net/FSAHFGSADHSAKNDAS/article/details/88745656)
+ [圆周率高精度算法](https://blog.csdn.net/LittleWhiteLv/article/details/81587555)