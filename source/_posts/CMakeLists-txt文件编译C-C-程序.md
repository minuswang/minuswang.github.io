---
title: CMakeLists.txt文件编译C/C++程序
date: 2017-11-01 16:21:45
tags:
---
## >CMake编译原理

`CMake`是一种跨平台编译工具，比`make`更为高级，使用起来要方便得多。`CMake`主要是编写`CMakeLists.txt`文件，然后用`cmake`命令将`CMakeLists.txt`文件转化为`make`所需要的`makefile`文件，最后用`make`命令编译源码生成可执行程序或共享库`（so(shared object)）`。因此`CMake`的编译基本就两个步骤：

1. cmake
2. make

`cmake` 指向`CMakeLists.txt`所在的目录，例如`cmake .. `表示`CMakeLists.txt`在当前目录的上一级目录。`cmake`后会生成很多编译的中间文件以及`makefile`文件，所以一般建议新建一个新的目录，专门用来编译，例如

```
mkdir build
cd build
cmake ..
make
```
`make`根据生成`makefile`文件，编译程序。

## >使用`Cmake`编译程序

我们编写一个关于开平方的`C/C++`程序项目，即`b= sqrt(a)`，以此理解整个`CMake`编译的过程。

a.准备程序文件

文件目录结构如下：

```
.
├── build
├── CMakeLists.txt
├── include
│   └── b.h
└── src
    ├── b.c
    └── main.c
```

头文件`b.h`，如下所示：

```c
#ifndef B_FILE_HEADER_INC
#define B_FIEL_HEADER_INC

#include<math.h>

double cal_sqrt(double value);

#endif
```
 

头文件`b.c`，如下所示：

```c
#include "../include/b.h"

double cal_sqrt(double value)
{
    return sqrt(value);
}
```
 

`main.c`主函数，如下所示：

```c
#include "../include/b.h"
#include <stdio.h>
int main(int argc, char** argv)
{
    double a = 49.0; 
    double b = 0.0;

    printf("input a:%f\n",a);
    b = cal_sqrt(a);
    printf("sqrt result:%f\n",b);
    return 0;
}
```
 

## >编写`CMakeLists.txt`

接下来编写`CMakeLists.txt`文件，该文件放在和`src`，`include`的同级目录，实际方哪里都可以，只要里面编写的路径能够正确指向就好了。`CMakeLists.txt`文件，如下所示：


> 1. `cmake verson`，指定`cmake`版本 

  `cmake_minimum_required(VERSION 3.2)`

> 2. `project name`，指定项目的名称，一般和项目的文件夹名称对应

  `PROJECT(test_sqrt)`
  
> 3. `head file path`，头文件目录

```c
  INCLUDE_DIRECTORIES(
  include
 )
 ```

> 4. `source directory`，源文件目录

 `AUX_SOURCE_DIRECTORY(src DIR_SRCS)`
 
> 5. `set environment variable`，设置环境变量，编译用到的源文件全部都要放到这里，否则编译能够通过，但是执行的时候会出现各种问题，比如`"symbol lookup error xxxxx , undefined symbol"`
```c
 SET(TEST_MATH
 ${DIR_SRCS}
 )
 ```
 > 6. `add executable file`，添加要编译的可执行文件

 `ADD_EXECUTABLE(${PROJECT_NAME} ${TEST_MATH})`
 
 > 7. `add link library`，添加可执行文件所需要的库，比如我们用到了`libm.so`（命名规则：`lib+name+.so`），就添加该库的名称

 `TARGET_LINK_LIBRARIES(${PROJECT_NAME} m)`


 `CMakeLists.txt`主要包含以上的7个步骤，具体的意义，请阅读相应的注释。

## >编译和运行程序

准备好了以上的所有材料，接下来，就可以编译了，由于编译中出现许多中间的文件，因此最好新建一个独立的目录`build`，在该目录下进行编译，编译步骤如下所示：
```c
mkdir build
cd build
cmake ..
make
```
操作后，在`build`下生成的目录结构如下：

```c
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   │   ├── 3.2.2
│   │   │   ├── CMakeCCompiler.cmake
│   │   │   ├── CMakeCXXCompiler.cmake
│   │   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   │   ├── CMakeSystem.cmake
│   │   │   ├── CompilerIdC
│   │   │   │   ├── a.out
│   │   │   │   └── CMakeCCompilerId.c
│   │   │   └── CompilerIdCXX
│   │   │       ├── a.out
│   │   │       └── CMakeCXXCompilerId.cpp
│   │   ├── cmake.check_cache
│   │   ├── CMakeDirectoryInformation.cmake
│   │   ├── CMakeOutput.log
│   │   ├── CMakeTmp
│   │   ├── feature_tests.bin
│   │   ├── feature_tests.c
│   │   ├── feature_tests.cxx
│   │   ├── Makefile2
│   │   ├── Makefile.cmake
│   │   ├── progress.marks
│   │   ├── TargetDirectories.txt
│   │   └── test_sqrt.dir
│   │       ├── build.make
│   │       ├── C.includecache
│   │       ├── cmake_clean.cmake
│   │       ├── DependInfo.cmake
│   │       ├── depend.internal
│   │       ├── depend.make
│   │       ├── flags.make
│   │       ├── link.txt
│   │       ├── progress.make
│   │       └── src
│   │           ├── b.c.o
│   │           └── main.c.o
│   ├── cmake_install.cmake
│   ├── Makefile
│   └── test_sqrt
├── CMakeLists.txt
├── include
│   └── b.h
└── src
    ├── b.c
    └── main.c
```

注意在`build`的目录下生成了一个可执行的文件`test_sqrt`,运行获取结果如下：

命令：
`./test_sqrt `
结果：
```
input a:49.000000
sqrt result:7.000000
```

d.源码

[地址：test_sqrt.tar.gz](http://files.cnblogs.com/files/cv-pr/test_sqrt.tar.gz)

 

3.参考资料

[CMake 使用方法 & CMakeList.txt](http://blog.csdn.net/u012150179/article/details/17852273)