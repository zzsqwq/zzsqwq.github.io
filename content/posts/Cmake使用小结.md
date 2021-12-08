---
title: "Cmake使用小结"
categories: [ "Cmake" ]
tags: [ "Linux","cmake","CMakeLists" ]
draft: true
slug: "150"
date: "2020-12-04 01:51:00"
---

### 前言

​	&nbsp;&nbsp;&nbsp;最近在学习Cmake，主要是CMakeLists的书写，顺手做一个小结。

### 例子

​	&nbsp;&nbsp;&nbsp;首先先在一个文件夹创建CMakeLists.txt以及main.c，其中的内容分别为

CMakeLists.txt

```cmake
project (hello)
set(SRC_LIST main.c)
message(STATUS "This is SOURCE dir" ${HELLO_SORUCE_DIR})
add_executable(hello ${SRC_LIST})
```

main.c

```c
#include<stdio.h>
int main()
{
    printf("Hello cmake!");
    return 0;
}
```

​	&nbsp;&nbsp;&nbsp;c语言的自然不用说，关于CMakeLists中的内容，大概我们也能看懂，说的是这个工程叫做`hello`，我们定义一个变量叫 `SRC_LIST` 其内容为 `main.c` ，第三行这是一个打印语句，打印出我们当前的`SOURCE_DIR` ，最后，把我们可编译的源文件放入里面，我们利用 `${变量名}` 来把这个变量解析。

​	&nbsp;&nbsp;&nbsp;需要注意的是，在我看的教程 `Cmake Practice` 里面这一句是 `add_executable(hello SRC_LIST)`，在新版本中这样写会报错，我们需要把其中内容解析出来。



### 一些指令的用法

+ project语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`project(projectname [CXX] [C] [JAVA])` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们用这个命令来定义工程名称，后面的三个可选变量为定义工程支持的语言列表，默认情况支持所有语言，这个命令还定义了两个变量 `<projectname>_BINARY_DIR` 和 `<projectname_SOURCE_DIR>` ，同时 cmake 也预定义了 `PROJECT_BINARY_DIR` 和 `PROJECT_SOURCE_DIR` 变量，他们的值分别跟` <projectname>_BINARY_DIR` 与 `<projectname>_SOURCE_DIR` 一致。

+ set语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`set(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这是显式的定义一个名为 `VAR` 的变量，如果有多个源文件，可以空格间隔一直写。

+ message语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display" )` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个指令用于向终端输出用户定义的信息，包含了三种类型: 
       1.SEND_ERROR，产生错误，生成过程被跳过。 
       2.SATUS，输出前缀为—的信息。 
       3.FATAL_ERROR，立即终止所有 cmake 过程.

+ add_executable语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`add_executable(filename SRC_LIST)`

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个是把源文件编译为可执行文件，可执行文件名为 `filename` ，源文件列表为 `SRC_LIST` ，这里我们在前面定义了一个为这个名称的变量，所以可以用 `${}` 来引用他。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;上述例子里，这个语句也可以写为 `add_executable(hello main.c)`

+ add_subdirectory语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里有三个参数，一个是指定源文件的文件夹，第二个是指定中间二进制和目标二进制的存放位置，第三个是将某个目录在整个编译中排除。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是，如果我们只填一个 `source_dir` ，那么在编译后的中间二进制文件，也会存在编译目录下的 `src` 文件用于和源文件文件夹对应。

+ add_library语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`add_library(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2...)`

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里的libname为库的名称，你填写的名称为 `hello` 则生成的文件自动为 `libhello.xxx` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续的参数，有三个可选，分别为生成动态库，静态库，仅对使用dyld的系统生效，如果不支持dyld，则被当作动态库对待。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后续参数分别为除……以外，以及源文件。

+ set_target_properties语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`set_target_properties(target1 target2 ... PROPERTIES prop1 value1 prop2 value2)`

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这条指令可以用来设置输出的名称，对于动态库，还可以设置动态库版本和api版本。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果我们用上述例子输出两个libname相同的动态库和静态库，发现只能生成一个，但是可以先生成一个名字不同的，然后用该语句对其改名，可以这么操作。

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `set_target_properties(原名称 PROPERTIES OUTPUT_NAME 改后名称)`

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于修改动态库版本和api版本，可以这么做

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`set_target_properties(hello PROPERTIES VERSION 1.2 SOVERSION 1)` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这么写的意思是，将 `libhello.so` 这个动态库的版本设为 1.2 api版本设为1

+ include_directories语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`include_directories([AFTER|BEFORE] [SYSTEM] dir1 dir2 ....)` 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这条指令可以为工程添加特定的文件搜索路径 路径之间用空格分隔

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如前面一样，如果路径本身就包含了空格，可以用双引号把它括起来，这条语句默认的行为是追加到当前头文件搜索路径后面，通过如下两种方式可以控制

       1.在`include_directories` 中设置AFTER/BEFORE语句来控制。
       2.通过在 `CMakeLists.txt` 中SET `CMAKE_INCLUDE_DIRECTORIES_BEFORE` 这个变量为on，可以让他追加在前面

+ target_link_libraries语句

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`target_link_libraries(target library1 <debug | optimized> library2 ...)`

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个指令用来为target添加需要链接的共享库，可以为可执行文件添加，也可以为共享库本身添加。

### 基本语法规则

+ 变量使用 ${}方式取值，但是IF语句中直接使用变量名
+ 指令(参数1 参数2……) 参数使用括弧包含，中间用空格或者分号分隔。
+ 指令是大小写不敏感的，参数和变量是大小写敏感的。
+ 如果文件名中间用空格 需要用`" "`包含来避免解析成两个文件。

### 外部构建和内部构建

​	&nbsp;&nbsp;&nbsp;我们上述的过程就是内部编译。

​	&nbsp;&nbsp;&nbsp;下面说一下外部编译，我们把文件夹中除了初始的main.c源文件和CMakeLists.txt全部删除掉，然后我们新建一个build目录，并在其中运行`camke ..` 然后 `make` ，我们看一下文件夹可以发现，目标文件生成在了 `build` 目录，这就是外部编译。

​	&nbsp;&nbsp;&nbsp;外部编译的好处是所有动作都发生在编译目录，而不是原工程目录。

​	&nbsp;&nbsp;&nbsp;这里需要注意的是，`PROJECT_SOURCE_DIR` 仍然指的是工程目录，跟原先一样

​	&nbsp;&nbsp;&nbsp;但是 `PROJECT_BINARY_DIR` 现在指的是编译一路径，在build中。

### 其他相关

+ 我们可以在构建后用 `make clean` 来对构建结果清理。

+ 我们可以通过在 `CMakeLists.txt` 中设置 `EXECUTABLE_OUTPUT_PATH` 和 `LIBRARY_OUTPUT_PATH` 两个目录来定义可执行文件和库文件输出的目录。

+ 把握一个简单的原则，在哪里 ADD_EXECUTABLE 或 ADD_LIBRARY， 如果需要改变目标存放路径，就在哪里加入改变路径的语句。很明显我们在 `source_dir`  目录下的 `CMakeLists.txt` 文件添加`add_executable`，所以我们在 `source_dir` 目录下添加改变输出文件路径的语句。

+ 动态库和静态库作为一个一个target不能够同名。 