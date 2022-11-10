---
title: "C++ 类使用注意事项"
categories: ["C++"]
tags: ["C++", "Class"]
draft: false 
slug: "cxx-class-skills"
date: "2022-11-09T22:40:41+08:00"
---

## 前言

本文包含一些在使用 C++ 类时的注意事项，可避免一些常见问题，**并能让你在写代码时更加自信**。

因为目前我使用的是 C++ 17，所以仅保证以下内容在 C++ 17 中正确。

## 构造函数

1. 对于单个参数的构造函数，推荐添加 `explicit` 关键字，防止隐式转换错误调用构造函数。

```C++
class DemoClass {
    explicit DemoClass(int test) {
        this->test_ = test;
    }
}
```

2. 显式声明有参构造函数后，编译器不会自动生成无参构造函数，若需要，请添加 `DemoClass() = default;`

```C++
class DemoClass {
    explicit DemoClass(int test) {
        this->test_ = test;
    }
    DemoClass() = default;
}
```

3. 若子类中没有显式调用父类的构造函数，子类会默认调用父类的无参构造函数，如果父类没有无参构造函数，会报错。

## 析构函数

1. 基类析构函数应该声明为 virtual，这样可以防止子类无法正确的析构。
2. 由于基类析构函数为 virtual，派生类的析构函数应该显式的 override。

```C++
class DemoClass {
    virtual ~DemoClass() = default;
}

class ChildClass: public DemoClass {
    ~ChildClass() override {
        xxx;
    }
}
```

## 成员变量默认值

对于类或局部作用域中未明确指定默认值的成员变量，其值遵循以下规则：

- 对于原生类型（**primitive types**），即 `int`、`float`、`int*`、`string*` 等，默认值为随机的脏数据。
- 对于对象（**objects**），例如自定义类、或 `std::string` 等，会调用默认（无参）构造函数，若没有默认（无参）构造函数，则报错。
- 对于引用（**reference**）类型，例如 `std::string&`，**必须**赋初始值或在构造函数中初始化，若为初始化，则报错。

```C++
float age; // 脏数据、随机值（无意义），此时访问会出现未定义行为
int *ptr;  // 脏数据、随机值（无意义），此时访问会出现未定义行为
string name;  // 调用默认构造函数，对于 std::string 来说为空字符串 ""
DemoClass demo; // 若 DemoClass 存在无参构造函数，则调用，否则编译错误
string *pname;  // 脏数据、随机值（无意义），此时访问会出现未定义行为
string &rname;  // 编译错误，必须显式初始化
const string &crname;  // 同上，编译错误
```

## 成员变量初始化方式

初始化成员变量一般而言有四种方式，一般而言第二种和第四种方式为等价的，不过在下面我们会看到一种例外：

```C++
class DemoClass {
    int a = 1; // (1) 通过 a == 1
    int a{1};  // (2) 通过 a == 1
    int a(1);  // (3) 报错，只能用于对对象的初始化
    int a = {1}; // (4)通过 a == 1
}
```

总体而言，推荐使用第二种方式，更加通用、现代、安全。

只不过在使用存在以 std::initializer_list 为参数做初始化的对象时，推荐使用第三种方式，第二种方式会导致非常贪婪的构造调用，详情可见：[Item 7:区别使用()和{}创建对象 - Effective Modern C++](https://cntransgroup.github.io/EffectiveModernCppChinese/3.MovingToModernCpp/item7.html)

同时，对于某些对象，例如 `std::vector`，第二种方式与第三种方式存在区别，此时要根据使用意图区别调用：

```C++
std::vector vec(10, 20); // vec 包含 10 个元素，值均为 20
std::vector vec{10, 20};  // vec 包含 10, 20 两个元素
```

此外，在 C++17 之前，在搭配使用 `auto` 和 `{}` 时会产生奇怪的行为，同时在这里带 = 和不带 = 的 {} 会出现差别：

```C++
auto a{1};  // C++17 之前 a 为 std::initializer_list<int>，之后为 int
auto a = {1}; // a 为 std::initializer_list<int>
auto a = 1; // a 为 int
auto a{1, 2};  // 报错!
auto a = {1, 2}; // a 为 std::initializer_list<int>
```

## 更进一步

为什么推荐使用第二种方式？

C++11 引入了 `{}` 这种称为统一初始化（uniform initialization）的语法来整合那些混乱且不适于所有情景的初始化语法。

为什么叫统一初始化？看以下几个例子：

```C++
class DemoClass{
    …
    
private:
    int x{ 0 };   //没问题，x初始值为0
    int y = 0;    //也可以
    int z(0);     //错误！
}
```

这里，z 为一个原生类型，而不是对象，无法使用 `()` 来对其初始化。

而 `=` 也不是总是可用的，不可赋值拷贝构造的对象（例如`std::atomic`——见[Item40](https://github.com/kelthuzadx/EffectiveModernCppChinese/blob/master/7.TheConcurrencyAPI/item40.md)）可以使用花括号初始化或者小括号初始化，但是不能使用 `=` 初始化：

```C++
std::atomic<int> ai1{ 0 };      //没问题
std::atomic<int> ai2(0);        //没问题
std::atomic<int> ai3 = 0;       //错误！
```

因此我们很容易理解为什么 `{}` 初始化又叫统一初始化，在 C++ 中这三种方式都被指派为初始化表达式（而不是什么函数声明或者拷贝构造），但是只有 `{}` 任何地方都能被使用。

## 参考资料及拓展阅读资料

1. [How do C++ class members get initialized if I don't do it explicitly?](https://stackoverflow.com/questions/3127454/how-do-c-class-members-get-initialized-if-i-dont-do-it-explicitly#)

1. [Why does auto x{3} deduce an initializer_list?](https://stackoverflow.com/questions/25612262/why-does-auto-x3-deduce-an-initializer-list) 

1. [mariusbancila.ro](https://mariusbancila.ro/blog/2017/04/13/cpp17-new-rules-for-auto-deduction-from-braced-init-list/)

1. https://cntransgroup.github.io/EffectiveModernCppChinese/3.MovingToModernCpp/item7.html
