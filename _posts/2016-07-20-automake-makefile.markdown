---
layout:     post
title:      使用automake生成Makefile
date:       2016-07-20 10:12:00
author:     "lemon"
categories: 工具
---

一些Linux平台上开源的C或者C++项目，当你编译这些项目的时候，只需./configure、 make和make install就可以把程序编译完成并安装到系统中。这些开源的项目的编译和安装怎么如此的干净利落，主要通过automake相关工具实现的。

## 工具简介

在使用automake之前，需要安装下列工具：

-   GNU Automake
-   GNU Autoconf
-   GNU m4
-   GNU Libtool

autoconf是一个用于生成可以自动地配置软件源码包，用以适应多种UNIX类系统的shell脚本工具，其中autoconf需要用到m4，便于生成脚本。automake是一个从Makefile.am文件自动生成Makefile.in的工具。为了生成Makefile.in，automake还需用到perl，由于automake创建的发布完全遵循GNU标准，所以在创建中不需要perl。libtool是一款方便生成各种程序库的工具。

## 自动生成makefile步骤

使用上述的工具生成Makefile文件的步骤基本是固定的，只需要在每步按照我们的需求进行适当的配置即可生成Makefile文件。我们先通过图片展示命令之间的关系。

![automake命令](/images/automake/1.png)

1. 运行autoscan命令
扫描源代码以搜寻普通的可移植性问题，比如检查编译器、库、头文件等，生成文件configure.scan，它是configure.ac的一个雏形。
2. 将configure.scan文件重命名为configure.ac，并按照需要修改configure.ac文件。configure.ac文件的内容是一些宏，confiugre.ac调用一系列autoconf宏来测试程序需要的或用到的特性是否存在，以及这些特性的功能。这些宏经过autoconf处理后会变成检查系统特性、环境变量、软件必须的参数的shell脚本。configure.ac文件中的宏的顺序并没有规定，但是你必须在文件的最前面和最后面分别加上AC_INIT宏和AC_OUTPUT宏。在configure.ac中的一些常用宏定义。
3. 执行aclocal命令
aclocal是一个perl 脚本程序。aclocal根据configure.ac文件的内容，自动生成aclocal.m4文件。
4. 执行autoheader命令
该命令生成config.h.in文件。该命令通常会从acconfig.h文件中复制用户附加的符号定义。
5. 执行autoconf命令
有了configure.ac和aclocal.m4 两个文件以后，我们就可以使用autoconf来产生configure文件了。configure脚本能独立于autoconf运行，且在运行的过程中，不需要用户的干预。
6. 在Project目录下新建Makefile.am文件
7. 运行automake命令
automake会根据Makefile.am文件产生一些文件，其中最重要的是Makefile.in文件。
8. 执行configure生成Makefile。
