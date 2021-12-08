---
title: "Python初步学习"
categories: [ "Python" ]
tags: [ "Python" ]
draft: false
slug: "66"
date: "2020-04-18 01:24:00"
---

# Python学习笔记 



## Python的不同解释器

1. **CPython**

   这是自带的用C语言开发的解释器，因此叫CPython。它也是使用最广的Python解释器。

2. **IPython**

   这是基于CPython之上的一个交互式解释器，只是相比于CPython多了交互上的优化。

3. **PyPy**

   它的目标是执行速度。PyPy采用[JIT技术](http://en.wikipedia.org/wiki/Just-in-time_compilation)，对Python代码进行动态编译（注意不是解释），所以可以显著提高Python代码的执行速度。他与CPython略有不同。


<!--more-->


1. **Jython**

   这是运行在Java平台上的Python解释器，可以直接把Python代码编译成Java字节码执行。

2. **IronPython**

   这是运行在微软.Net平台上的Python解释器，可以直接把Python代码编译成.Net的字节码。



## Python基础

### 简单的输入和输出(I/O)

#### 输入

​	&nbsp;&nbsp;&nbsp;python提供了一个 `input()` 函数供我们输入使用，这读入的是字符串数据，并返回，可以将返回值存放在一个变量中。**input**函数中可以带字符串，这段字符串在输入前会打印在屏幕上，这使得我们具有很好的交互性，好比我们写:

```python
name = input("hello,friend! please input your name")
print("The input name is ",name)
```

​	&nbsp;&nbsp;&nbsp;那么这时候我们运行上述程序，就会提示 `hello , friend! please input your name` ，这就提示我们应该输入名字。

​	&nbsp;&nbsp;&nbsp;这里需要注意的是，`input()` 函数读入的是一个字符串 `str` ，就算我们输入了整数他也是一个字符串，如果我们要用真正的整数，那么就需要用 `int()` 进行类型强制转换。如果其中不是合法的整数，那么会报错。

#### 输出

​	&nbsp;&nbsp;&nbsp;python中的输出函数是 `print()` ，这和 **C++** 的`printf` 差了一个f。我们在函数的参数中传入什么，他就会打印什么。好比我们写  `print("heelo,world") ` ，那么运行就会打印 `hello,world`  在屏幕上。`print` 支持我们传入多个参数，好比 `printf("my name is","zs")` ，两两之间用逗号隔开，这在输出时会被解析成空格，也就是说两段字符串之间有一个空格。当然此函数也可以打印整数等。



### 一些规则

#### 缩进

​	&nbsp;&nbsp;&nbsp;Python中对代码块的区分，不是用C++的大括号，而是用缩进。处于连续同一缩进的是一个代码块，这也是为什么Python又被戏称为**游标卡尺语言**的原因。**当语句以冒号`:`结尾时，缩进的语句视为代码块。**我们通常用**一个Tab / 四个空格** 的缩进。

#### 注释

​	&nbsp;&nbsp;&nbsp;Python的注释用的是 `#` ，而 C++ 中的注释用的是 `\\` 。

#### Case Sensitive

​	&nbsp;&nbsp;&nbsp;Python中是大小写敏感的，也就是 a 和 A 不是同一个东西。

### 数据类型

#### 1. 整数

​	&nbsp;&nbsp;&nbsp;Python一个很大的好处就是可以处理任意大小的整数，包括负整数。这也是为什么很多大数题大家都喜欢用Python，hhhh。多数地方都用十进制，但是也是支持其他进制的哈~好比 `0x` 前缀就是16进制。

#### 2. 浮点数

​	&nbsp;&nbsp;&nbsp;浮点数是小数，之所以称为浮点数，是因为小数点位置在科学计数法中是可变的。这里需要注意，整数和浮点数在计算机内部存储方式不同，浮点数应该都是 `IEEE754` 标准吧？整数之间的运算永远都是精确的，包括除法。而浮点数的运算则会有一定的误差。Python的浮点数也没有大小限制，但是超出一定范围就直接表示为`inf`（无限大）。

#### 3. 字符串

​	&nbsp;&nbsp;&nbsp;字符串是以单引号 `''` 或者双引号 `""` 括起来的任意文本。我们注意到**Python**中**没有单个字符的概念**，就算是单个字符也是一个字符串。如果`''` 括起来的字符串内部出现 `''` 需要使用`\`转义，相同的，如果 `""` 括起来的字符串内部出现 `""` 需要转义。好比下面的程序

```python
print("My name is 'zs'")  #合法  My name is 'zs'

print("My name is "zs"")  #不合法 invalid syntax

print("My name is \"zs\"") #合法 My name is "zs" 

print('My name is "zs"')  #合法  My name is "zs"

print('My name is 'zs'')  #不合法 invalid syntax

print('My name is \'zs\'') #合法 My name is 'zs'
```

​	&nbsp;&nbsp;&nbsp;Python中也有很多转义字符，跟C++的很类似。`\n`,`\t`,`\\`  分别代表换行，横向制表，字符`\`  。在python中还支持用 `r' '` 表示 `''`  内部的字符默认不转义。

​	&nbsp;&nbsp;&nbsp;Python在输出多行语句时，可以用 `print('''content''')` 其中content中的内容，支持用直觉上的换行。

```python
print('''python
name
zs''')

# The output
python
name
zs
```

#### 4. 布尔值

​	&nbsp;&nbsp;&nbsp;含有 **Ture** 和 **False** 两种类型，代表真和假。支持 `and` ,  `or` ,`not` 三种运算。

#### 5. 空值

​	&nbsp;&nbsp;&nbsp;空值是Python中的一个特殊值，用 `None` 表示。 `None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。



### 变量

​	&nbsp;&nbsp;&nbsp;变量在程序中就是用一个变量名表示了，变量名必须是大小写英文、数字和`_`的组合，且不能用数字开头。

​	&nbsp;&nbsp;&nbsp;变量命名时最好能做到顾名思义，当然也有很多规范的命名规则，可以自行百度。

​	&nbsp;&nbsp;&nbsp;在 **Python** 中我们不需要指定一个变量是特定的类型，它可以在不同的类型之间变来变去，这确实很方便，不过感觉也是很占内存和时间的。

​	&nbsp;&nbsp;&nbsp;这种变量本身类型不固定的语言称之为**动态语言**，与之对应的是**静态语言**。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错



### 常量

​	&nbsp;&nbsp;&nbsp;Python中没有C++中的const来限制常量，但是通常用**变量名全大写来代表这个变量为一个常量**，但是这玩意是个约定俗成的，并不是说你这么写他就真是个常量了。



### 字符串和编码

#### 字符编码

​	&nbsp;&nbsp;&nbsp;字符编码有很多种，不同的语言也对应着不同的字符编码。常见的几个是 `ASCII` ,`Unicode` ,`UTF-8`,`GB2313` 他们分别是英文和特殊字符的编码，统一的一套编码，可变长的统一编码，常用的中文编码。

#### Python的字符串存储

​	&nbsp;&nbsp;&nbsp;Python 3的的字符串是 `Unicode` 编码的，也就是说Python的字符串支持多语言，因为这是一套统一的编码。

​	&nbsp;&nbsp;&nbsp;对于单个字符的编码，Python提供了 `Ord()` 函数获取字符的整数表示，`chr()` 函数通过整数获取对应字符。

​	&nbsp;&nbsp;&nbsp;Python中的字符串类型为 `str` ，一个字符对应若干字节。**如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。**Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示。

​	&nbsp;&nbsp;&nbsp;我们可以通过`encode('编码方式')` 将 **str** 转变成 **bytes** 。我们也可以通过 `decode('编码方式')` 将 **bytes** 转变为 **str** 。

​	&nbsp;&nbsp;&nbsp;Python为我们提供了一个 `len()` 函数，如果字符串是 **str** ，那么计算出的是字符数，如果是 **bytes** ，那么计算的是字节数。我们为了防止乱码问题，在两者相互转换时推荐用 **utf-8** 编码。

​	&nbsp;&nbsp;&nbsp;由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。而且需要你的编辑器支持 UTF-8编码。



#### 字符串的格式化输出

##### 第一种方式

​	&nbsp;&nbsp;&nbsp;Python的 `print()` 格式化输出和C语言的很像，也是 `%d` 代表整数，`%f` 代表浮点数， `%s` 代表字符串，`%s`代表十六进制整数。关于格式的指定，好比补0什么的也和C的很像。具体格式如下：

```python
print('%2d-%02d' % (3,1)) # 输出 3-01

print('my name is %s' % "zs") # 输出 my name is zs
```

​	&nbsp;&nbsp;&nbsp;如果我们要在字符串里面输出 `%` ，那么就需要用 `%%` 来转义表示 `%` 。

```python
print("This is a common %% %s " % "字符") # 输出 This is a common % 字符
```

##### 第二种方式

​	&nbsp;&nbsp;&nbsp;除了上面的方法，`print` 还可以用 .format 的方法进行格式化输出。例如下例子

```python
print('{0} name is {1}'.format("who","zs")) #输出 who name is zs
```



### List & tuple (列表和元组)

#### List(列表)

​	&nbsp;&nbsp;&nbsp;List像是一个大杂烩，里面可以有各种类型的东西，是一个有序的集合，也就是说可以通过下标索引。

​	&nbsp;&nbsp;&nbsp;我们创建一个列表可以用中括号， `Mylist = ["zs","wx"]` ，这就创建了具有两个元素的列表，第一个元素是字符串 **zs** 第二个元素是字符串 **wx** 。这个列表的名字就是 **Mylist** 。

​	&nbsp;&nbsp;&nbsp;我们可以通过 `len()` 来获取列表中元素的个数，也可以通过下表索引列表的元素，但是要注意下标是从0开始计数的，如果索引出界会报 `IndexError` 。有趣的是，我们可以通过负的下表来访问元素，是倒着访问的，好比上述列表中 `Mylist[-1]` 就代表元素 **"wx"** 。 

​	&nbsp;&nbsp;&nbsp;我们可以通过 `listk = []` ，来创建一个空列表 **listk** ，如果用 `len()` 查看长度那么长度为**0**. 

​	&nbsp;&nbsp;&nbsp;此外列表中的元素也可以是列表，可以通过**类似于二维数组的形式索引**。

​	&nbsp;&nbsp;&nbsp;列表中，有许多的**方法**。就像是相对于这个类型内置的一些函数，用法如下：

```python
Mylist = ["zs","wx"]
print(Mylist) # 输出 ['zs', 'wx']

Mylist.append('Better') # 用于在列表后面追加一个元素
print(Mylist) # 输出 ['zs', 'wx', 'Better']

Mylist.insert(1,'Good') # 用于在下标为1的位置，插入一个元素
print(Mylist) # 输出 ['zs', 'Good', 'wx', 'Better']

popx = Mylist.pop() # 用于删除列表中最后一个元素，并返回元素的值
print(Mylist,popx) # 输出 ['zs', 'Good', 'wx'] Better

popx = Mylist.pop(1) # 用于删除列表中下标为1的元素，并返回元素的值
print(Mylist,popx) # 输出 ['zs', 'wx'] Good

L = [] # 创建了一个空列表 L
print(len(L)) #输出0
```



#### Tuple(元组)

​	&nbsp;&nbsp;&nbsp;元组跟上面的链表差不多，只不过是**不可变的，一旦初始化就不能修改**，也是可以通过下标访问元素。

​	&nbsp;&nbsp;&nbsp;不同的的是，我们定义一个元组是用 `()` ，好比我们定义 `Mytuple = ("zs","wx")` ，这是含有两个字符串元素的元组，我们可以通过 `Mytuple = ()` 来定义一个空的元组。

​	&nbsp;&nbsp;&nbsp;需要注意的是，当我们定义一个只有一个元素的元组，如果我们写成 `Mytuple = ("zs")` ，那么Python会默认解析为这是一个字符串，把括号当初普通的括号，不解释成元组。 那么我们如何定义只有一个元素的元组呢，我们应该写 `Mytuple = ("zs",)` 这样就是只有一个元素的元组。

​	&nbsp;&nbsp;&nbsp;**元组中的不可变，是指它的指向不变**，那么如果好比元组的元素中有一个列表，那么其实这个元组中的列表的元素还是可以改变的。



### 条件判断

​	&nbsp;&nbsp;&nbsp;条件判断是一个经典的语句。用于分支结构。

​	&nbsp;&nbsp;&nbsp;用法跟C很像，不过 **else if** 可以缩写为 **elif** ，并且因为 **if** ，**else** , **elif**  后面接的都是语句块，因此要加 `:` 。

```python
weight = 120
if weight>=200:
    print("too fat")
elif weight<=100:
    print("too thin")
else: 
    print("Good")
```



###  循环语句

​	&nbsp;&nbsp;&nbsp;循环结构也是三大结构之一。

#### for

​	&nbsp;&nbsp;&nbsp;for语句是我C++中最喜欢循环语句。在Python中，他的写法变成了 `for x in something:` ，下面接相应的循环语句块。这个 **x** 是变量的名字，**something** 是某一个容器，可以是列表可以是元组啥的，这个写法的意思就是 遍历 **something** 中的每个元素，带入变量 **x** 中，执行循环操作。

​	&nbsp;&nbsp;&nbsp;我们通常配合 `range()` 函数来执行循环操作，通过 `range(n)` 可以生成从 **[0,n)** 的整数。

```python
for x in list(range(11)):
    print(x)  #输出 0~10
```



#### while

​	&nbsp;&nbsp;&nbsp;while语句也是和C语言差不多，当型循环，当满足条件时就执行循环，也记住不要忘记加 `:` 。



#### break & continue

​	&nbsp;&nbsp;&nbsp;**break** 的作用是结束整个循环。

​	&nbsp;&nbsp;&nbsp;**continue** 的作用是跳过这一次循环。



### Dict & Set (字典和集合)

#### Dict

​	&nbsp;&nbsp;&nbsp;这个字典其实就是C语言中的 **map** ，人家 Python 直接内置了，属实业界良心。其实就是利用键值对匹配，一个键对应一个值。实现方式为哈希 **(Hash)** 。

​	&nbsp;&nbsp;&nbsp;创建就是 `Mydict = {"zs":250 , "wx":666`} 这样第一个元素的键为 "zs" ，对应值为 250 。第二个值与这个的解读类似。我们也可以通过类似于数组的形式往字典里面加元素. `Mydict["jjh"] = 100`  ，那么这时候就往里面加入了一个键值对。

​	&nbsp;&nbsp;&nbsp;字典中每个键值是唯一的，但是值可以相同，类似于函数。我们也可以通过类似于数组的形式，下标为键来访问值。当我们下标在字典中不存在，利用下标访问就会直接报错。**Python** 为我们提供了另一种方法来满足我们的需求，利用 `Mydict.get(键)` 可以获得对应的值，当不存在这个键，会返回 `None` 。此外，我们也可以指定其返回值，`Mydict.get(键,something)` ，这样当不存在的时候就会返回这个 **something** 。

​	&nbsp;&nbsp;&nbsp; 最后，**字典中的 Key 只能是不可变对象。**

#### Set

​	&nbsp;&nbsp;&nbsp;set 就是数学中的集合，具有无序性和唯一性。里面元素不重复，并且无序导致没法通过下标访问。可以用来给一组数据去重。通过 `add(key)  remove(key)` 等函数去除对应值的元素。

​	&nbsp;&nbsp;&nbsp;可以通过 `&` 求交集， `|` 求并集， `-` 求差集等。

​	&nbsp;&nbsp;&nbsp;**set 中的元素也只能是不可变对象。**



## 函数

### 调用函数

​	&nbsp;&nbsp;&nbsp;调用函数的时候要保证**参数个数、参数顺序、参数类型**满足函数的定义。然后正确的处理好返回值。

### 定义函数

#### 函数的定义

​	&nbsp;&nbsp;&nbsp;普通函数的定义通过 `def` 来进行，好比我们要写一个求绝对值的函数，那么就可以如下定义

```python
def myabs(x):
    if x>=0:
        return x
    else:
        return -x
```

​	&nbsp;&nbsp;&nbsp;这样我们就可以类似调用 `myabs(-5)` 来获得 -5 的绝对值。

​	&nbsp;&nbsp;&nbsp;如果我们想要定义一个**空的函数**，也就是什么都不做，那么函数内部的语句可以写 `pass` 。这类似于C++中的分号的作用？大概是。

​	&nbsp;&nbsp;&nbsp;通过  `函数.__name__`  可以获得函数的真实名字。

#### 多个返回值的函数

​	&nbsp;&nbsp;&nbsp;我们可以在 **return** 后面写多个参数，这样在返回的时候会返回一个元组。我们接受返回值的时候，也可以并拍写多个变量，这样就会把返回值的元组中的各个值依次赋给每个变量。



### 函数的参数

​	&nbsp;&nbsp;&nbsp;Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数，可变参数，关键字参数，这使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

#### 默认参数

​	&nbsp;&nbsp;&nbsp;当我们调用一个函数，有几个参数大多数情况下是一个默认的值，少数时候是变的，那么我们可以用到默认参数，默认参数的使用我们可以在那个参数的后面加上 `=something` ，这意思说是，这个参数默认为 **something**

```python
def poww(x,n=2):
    k = 1
    while n>0:
        n = n-1
        k = k*x
    return k
```

​	&nbsp;&nbsp;&nbsp;例如上面这个例子，我们要求 $2^5$ ，那么可以调用 `poww(2,5)` 。那么我们如果要求 $2^2$ ，那么就可以直接调用`poww(2)` ，另一个参数可以不写，那么就是默认的2。

​	&nbsp;&nbsp;&nbsp;需要注意的是，**必选参数在前，默认参数要在后**。否则话会产生歧义，因为解释器不知道你到底传入的参数是默认参数还是必选参数。

​	&nbsp;&nbsp;&nbsp;此外，当我们有多个默认参数的时候，我们要在前面几个默认参数使用默认值，而后面那个用传入参数的时候，我们需要加上参数名，也就是 `参数名 = value` 这样传入。例子如下：

```python
def poww(x,n=2,z=3):
    k = 1
    f = n+z
    while n>0:
        f = f-1
        k = k*x
    return k
```

​	&nbsp;&nbsp;&nbsp;这时候，我们如果要调用 `poww(5,4)` ，代表求的是 $5^{4+3}$ ，如果我们要求 $5^{2+7}$ ，可以按照如下方法调用，使用 `poww(5,z=7)` 。

​	&nbsp;&nbsp;&nbsp;最后还有一点很重要，默认参数要指向 **不变对象** 。否则当我们重复调用会发生错误。



#### 可变参数

​	&nbsp;&nbsp;&nbsp;我们很多时候可能需要一个函数内传入不定量个数的参数，这就要用到可变参数，可变参数就是数量可变。

​	&nbsp;&nbsp;&nbsp;我们很容易想到，可以往里面传**列表或者元组** ，不过这样当我们传之前还有要把所有的参数归到一个列表和元素中，这样太麻烦。Python给出了一个简便写法，我们只需要在参数面前加一个 `*` ，这样我们就可以传入可变个参数。而且调用的时候，按普通的调用方法来即可，不需要传入元组。

```python
def test(*numbers):
    sum = 0
    for i in numbers:
        sum = sum + i*i
    return sum

print(test(1,2,3)) # 输出 14
```

​	&nbsp;&nbsp;&nbsp;那么当这时候我们想往里面传一个元组，或者列表。当然可以挨个用数组访问然后写，不过Python也给了我们一个简便做法，只需要在列表或者元组前面加一个 `*` ，就可以把它结构解开，然后挨个元素传入函数中。

```python
def test(*numbers):
    sum = 0
    for i in numbers:
        sum = sum + i*i
    return sum

Mylist = list(range(5))
print(test(*Mylist))  #输出30
```



#### 关键词参数

​	&nbsp;&nbsp;&nbsp;可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。

```python
def myfun(**kw):
    print(kw)

myfun(city = "jz",name = "zs") # 输出 {'city': 'jz', 'name': 'zs'}
Mydict = {"city":"jz","name":"zs"}
myfun(**Mydict)  # 输出 {'city': 'jz', 'name': 'zs'}
```

​	&nbsp;&nbsp;&nbsp;参数前面加 `**` 即是关键词参数。当然我们也可以传入参数时，用dict，然后加 `**`  解开结构传入。



#### 命名关键词参数

​	&nbsp;&nbsp;&nbsp;我们可以在上述的基础上，传入特定的参数，给相应的参数命名。这需要我们在定义各个参数之前，在参数之前加上 `*` ，好比 `def person(name,age,*,city,job)` 规定了，我们传入的两个关键词参数名字只能是 **city** 和和 **job** 。不过，当我们前面有一个参数是可变参数，那么就可以不用加那个 `*` 。好比像如下方法定义上面那个函数， `def person(name,age,*city,job)` 。在这里 **city** 是一个可变参数，后面的 **job** 就是一个命名关键词参数。



#### 参数调用顺序

​	&nbsp;&nbsp;&nbsp;在函数的参数中，上述各类参数可以组合使用。但是要注意顺序必须是：**必选参数、默认参数、可变参数、命名关键字参数和关键字参数。**



### 函数的递归调用

​	&nbsp;&nbsp;&nbsp;函数的递归调用就是自己调用自己，QAQ，C++里面搞得也挺多的，就不赘述了。



## 高级特性

### 切片

​	&nbsp;&nbsp;&nbsp;切片操作感觉真的是Python很方便的一个特性了。

​	&nbsp;&nbsp;&nbsp;**字符串，列表，元组支持切片。**

​	&nbsp;&nbsp;&nbsp;好比我们有一个列表，里面有数，0~100，我们要取其中的奇数，那么我们就需要每隔一个数取一个数，我们可以通过for循环来实现这个操作，但是呢，Python有一种更便利的方法来实现，那就是切片。他的用法类似于matlab中的冒号表达式，`begin:end:step`  这三个变量分别代表起始，终止和步长，也就是每个多少取一个，这三个参数都可以省略，**省略时默认为序列起始点，序列终止点，1。这里要注意**，**序列范围为 `[begin,end)` ，前闭后开**，例子：

```python
L = list(range(101))
print(L[1:100:2]) # 输出0~100所有的奇数

print(L[-10::2])  # 从倒数第十个数开始输出奇数，输出 [91, 93, 95, 97, 99]

print(L[-50:0:-2])  # 从倒数第50个数，往前开始输出奇数，为 51~0中所有奇数

```



### 迭代

​	&nbsp;&nbsp;&nbsp;迭代就是类似于遍历吧，通过 **for** 可以迭代遍历一个容器内的所有元素。

​	&nbsp;&nbsp;&nbsp;字符串和列表，元组，集合，字典这些都是可以用**for**遍历的，这也叫做可迭代对象。需要注意的是，我们在遍历字典和集合时，因为是无序的，所以两次遍历顺序可能不太一样。

​	&nbsp;&nbsp;&nbsp;当我们遍历字典时，默认遍历的是键值。例如下面这样

```python
L = {"zs":"rj","jjh":"nb"}

for key in L:
    print(key)  # 输出 zs jjh
    
for value in L.values():
    print(value) # 输出 rj nb
    
for k,v in L.items():
    print(k,v) # 输出 zs rj jjh nb

```

​	&nbsp;&nbsp;&nbsp;可以注意到，可以同时迭代两个数，或者多个数。



### 列表生成器

​	&nbsp;&nbsp;&nbsp;列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。

​	&nbsp;&nbsp;&nbsp;好比我们生成 `[1*1,2*2,3*3,···,n*n]` 这样的列表，可以用循环实现，也可以用列表生成式。

​	&nbsp;&nbsp;&nbsp;列表生成式格式大概是 `[元素 规则]` 就是前面是要往里面加的元素的表达式，后面是生成的规则。

```python
L = [x*x for x in range(1,11)]
print(L)  # 输出[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

D = [x*x for x in range(1,11) if x % 2 == 0]
print(D)  # 输出[4, 16, 36, 64, 100]

```



### 生成器

​	&nbsp;&nbsp;&nbsp;我们将上述列表生成式外面的 `[]` 改成 `()` ，就变成了一个列表生成器。列表生成器里面每个元素不是原来就存在的，而是你要用的时候他按照规则去生成，可以节省空间。他也是一个可迭代对象，可以通过for循环来访问，此外也可以用 `next(迭代器)` 来获取下一个元素。

​	&nbsp;&nbsp;&nbsp;除了上述方法，我们也可以用函数的方法来定义生成器，当一个函数中有了关键字 ： `yield` 他就不是一个普通的函数了，就变成了一个生成器，按照函数的规则来生成相应数据。规则如下：当我们进入函数的时候，开始从头开始执行，执行到 **yield** ，函数结束，返回 **yield** 后面接的内容。然后下次进入函数的时候，从上次结束的地方继续开始，然后这样一直循环，直到再不能取数为止。



### 迭代器

​	&nbsp;&nbsp;&nbsp;凡是可作用于`for`循环的对象都是`Iterable`类型，就是可迭代对象；

​	&nbsp;&nbsp;&nbsp;凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列，**这感觉就像是一个指针呀其实（自我认为），可以通过指针访问可迭代对象中的元素**；

​	&nbsp;&nbsp;&nbsp;集合数据类型如`list`、`dict`、`str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象。

​	&nbsp;&nbsp;&nbsp;Python的`for`循环本质上就是通过不断调用`next()`函数实现的，例如：

```python
for x in [1, 2, 3, 4, 5]:
    pass
```

实际上完全等价于：

```python
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```



## 函数式编程

### 高阶函数

#### map/reduce

​	&nbsp;&nbsp;&nbsp;`map()` 函数包括两个参数，第一个参数是一个函数，第二个参数是一个序列。**map** 的作用就是将序列中的每个元素代入到函数中并且求出每个元素对应的值，然后会返回一个相应的 `Iterator ` ，我们可以通过对应的语句，好比 `list()` ，`tuple()` 啥的转成对应的序列。

​	&nbsp;&nbsp;&nbsp;`reduce()` 函数也是包括两个参数，一个是函数，一个是序列。**reduce** 的作用是类似于一个递归的感觉吧大概，好比序列是 `L = [1,2,3,4,5]` ，有一个函数是 `f` ，我们暂且不管这个函数的作用是什么，那么如果我们现在调用 `reduce(f,L)` ，他返回一个 `f` 函数的返回值，值为 `f(f(f(1,2),3),4)` 。就是类似于这种嵌套的结构。



#### filter

​	&nbsp;&nbsp;&nbsp;`filter()` 函数也包括两个参数，一个是函数，一个是序列。他是通过那个函数的返回值是 `True` or `False` 来判断是否保留那个序列中的每个元素。返回值也是 `Iterator` 。

 

#### sorted

​	&nbsp;&nbsp;&nbsp;`sorted()` 顾名思义，这是一个排序函数，我们往里面传入一个序列，那么他就会默认的对序列按升序排序，并且返回一个这个排序后序列。**但是我们传入的序列不会有变动。**

​	&nbsp;&nbsp;&nbsp;此外，`sorted()` 里面还可以加关键词 key 键值来确定规则，好比我们可以加 `key=abs` 这样就可以将序列中所有的元素按照绝对值从小到大的顺序。我们也可以加 `reverse=True` 来变成降序排序。



### 返回函数

​	&nbsp;&nbsp;&nbsp;我们知道，函数名只是一个指向函数的变量，我们也可以用另一个变量指向这个函数来引用函数，相当于起了一个别名。因此，我们也可以在函数的返回值中返回一个函数，然后将返回值赋值给一个变量，这样就可以通过这个变量来调用返回的函数。

> 我们在一个函数中又定义了一个函数，并且，内部函数可以引用外部函数的参数和局部变量，当外部函数返回内部函数时，相关参数和变量都保存在返回的函数中，这种称为“闭包**（Closure）**”的程序结构拥有极大的威力。

​	&nbsp;&nbsp;&nbsp;返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。因为这个变量相当于一个静态变量，现在变了，前面相应的结果也会变。

> 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

​	&nbsp;&nbsp;&nbsp;通过  `函数.__name__`  可以获得函数的真实名字。

### 匿名函数

​	&nbsp;&nbsp;&nbsp;通过 `lambda` 可以创建匿名函数，这个就类似于matlab里面的那个 `@` 创建的匿名函数。

​	&nbsp;&nbsp;&nbsp;格式为： `lambda 变量: 返回值`  

```python
print(list(map(lambda x: x*x,range(1,11)))) # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

​	&nbsp;&nbsp;&nbsp;我们也可以把匿名函数当作函数的返回值返回。



### 装饰器

​	&nbsp;&nbsp;&nbsp;我们可能想在调用函数之前在前面打印一下函数的运行日志，或者其他的一些内容，其他的操作。

​	&nbsp;&nbsp;&nbsp;这样我们就可以通过装饰器来实现，装饰器是为了给函数加一些其他的修饰，但是不需要在原本函数的基础上做改变。

​	&nbsp;&nbsp;&nbsp;本质上，**装饰器(decorator)** 是一个返回函数的高阶函数，一个能打印日志的 **decorator** 可以如下定义：

```python
def log(func):
    def wrapper(*args,**kw):
        print("call %s()" % func.__name__)
        return func(*args,**kw)
    return wrapper

```

​	&nbsp;&nbsp;&nbsp;调用如下：

```python
@log
def now():
	print('2020-4-18')
    
print(now()) # 输出 call now() 2020-4-18
```

​	&nbsp;&nbsp;&nbsp;在这里， `@log` 可以等效为 `now = log(now)` ，那么我们应该怎么理解呢，首先，在我们调用之前，我们就把这个函数传入这个`log`函数，然后进入 `wrapper` 函数，先输出了日志，然后返回了一个 `func()` 函数，然后结束这个函数的定义，又返回了 `wrapper` 函数，这样就是将日志和原函数组合在一起了。所以最后一起输出



### 偏函数

​	&nbsp;&nbsp;&nbsp;偏函数可以看做一个函数其中某一个参数固定，然后另成一个新函数，这样到时候我们重复调用的时候就会比较方便了。

> 当函数的参数个数太多，需要简化时，使用`functools.partial`可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。

​	**当用 `functools.partial` 之前我们需要引入 `functools` 模块。**

```python
import functools

int2 = functools.partial(int,base=2)

print(int('10110',base=2)) # 以二进制来转化这个字符串，得到22
print(int2('10110')) # 和上述等价

```





## 模块

​	&nbsp;&nbsp;&nbsp;这个就是类似于头文件的感觉，模块里面包含了很多别人已经写好了的函数，你引入之后可以直接拿来用，能够大大提高自己的编程效率。

​	&nbsp;&nbsp;&nbsp;模块是放在包里的，这样可以避免不同模块之间的冲突，包中可以有很多的模块，并且所有包里都有一个相同名字的模块 ， `__init__.py` ，这个文件说明这个目录是一个包，里面的其他的内容是模块。

​	&nbsp;&nbsp;&nbsp;模块命名为 `包名.模块名` ，这样就有效避免了模块与模块之间的冲突

> 模块是一组Python代码的集合，可以使用其他模块，也可以被其他模块使用。
>
> 创建自己的模块时，要注意：
>
> - 模块名要遵循Python变量命名规范，不要使用中文、特殊字符；
> - 模块名不要和系统模块名冲突，最好先查看系统是否已存在该模块，检查方法是在Python交互环境执行`import abc`，若成功则说明系统存在此模块。



### 使用模块

​	&nbsp;&nbsp;&nbsp;在使用模块之前只需要  `import 模块名` ，就可以通过 **模块名.方法** 的方式来调用这个模块里面的方法。

​	&nbsp;&nbsp;&nbsp;当我们在命令行运行模块文件时，Python解释器把一个特殊变量`__name__`置为`__main__`，而如果在其他地方导入模块时，`if`判断将失败，因此，这种`if`测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。



#### 封装

​	&nbsp;&nbsp;&nbsp;在一个模块中，我们可能会定义很多函数和变量，但有的函数和变量我们希望给别人使用，有的函数和变量我们希望仅仅在模块内部使用。在Python中，是通过`_`前缀来实现的。

​	&nbsp;&nbsp;&nbsp;正常的函数和变量名是公开的（public）。但是外部不需要引用的函数全部定义成private，只有外部需要引用的函数才定义为public。



### 安装第三方模块

​	&nbsp;&nbsp;&nbsp;我们用包管理工具  `pip`  来安装第三方模块。

​	&nbsp;&nbsp;&nbsp;安装一个模块只需要 `pip install 库名` ，这样就成功安装了一个包了。

​	&nbsp;&nbsp;&nbsp;此外，我们也可以直接安装 [Anaconda](https://www.anaconda.com/) ，这是一个基于Python的数据处理和科学计算平台，它已经内置了许多非常有用的第三方库，我们装上Anaconda，就相当于把数十个第三方模块自动安装好了，非常简单易用。

​	&nbsp;&nbsp;&nbsp;下载地址为 ： [Anaconda官网](https://www.anaconda.com/download/) 



### 模块搜索路径

​	&nbsp;&nbsp;&nbsp;当我们加载一个模块时，Python会在指定路径搜索对应的模块文件，如果找不到就会返回错误。

​	&nbsp;&nbsp;&nbsp;默认情况下，Python解释器会搜索当前目录，所有已安装的内置模块和第三方模块，搜索路径存放在  `sys` 模块的 `path` 变量中。我们可以通过 `sys.path` 来查看。

​	&nbsp;&nbsp;&nbsp;我们要改动这个目录，往里面添加我们需要的，有两个方法。

+ 直接通过 `sys.path.append()` 添加对应的路径，但是运行结束后会失效。
+ 设置环境变量`PYTHONPATH`，该环境变量的内容会被自动添加到模块搜索路径中。设置方式与设置Path环境变量类似。注意只需要添加你自己的搜索路径，Python自己本身的搜索路径不受影响。





## 资料补充

+ [廖雪峰的Python教程](https://www.liaoxuefeng.com/wiki/1016959663602400)
+ [Anaconda介绍、安装及使用教程](https://www.jianshu.com/p/62f155eb6ac5)