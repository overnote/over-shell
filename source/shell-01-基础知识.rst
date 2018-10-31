.. contents::
   :depth: 3
..

一、基础知识
============

一、Shell简介
-------------

Shell 是一个 C 语言编写的脚本语言，它是用户与 Linux
的桥梁，用户输入命令交给 Shell 处理， Shell
将相应的操作传递给内核（Kernel），内核把处理的结果输出给用户。

程序=指令+数据

二、Shell分类
-------------

2.1 图形界面 Shell（GUI Shell）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

GUI 为 Unix 或者类 Unix
操作系统构造一个功能完善、操作简单以及界面友好的桌面环境。主流桌面环境有
KDE，Gnome 等。

2.2 命令行界面 Shell（CLI Shell）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

CLI
是在用户提示符下键入可执行指令的界面，用户通过键盘输入指令，完成一系列操作。

在 Linux 系统上主流的 CLI 实现是 Bash，是许多 Linux 发行版默认的
Shell。还有许多 Unix 上Shell。

::

   [root@10-234-2-128 pyworkspace]# cat /etc/shells 
   /bin/sh
   /bin/bash
   /sbin/nologin
   /usr/bin/sh
   /usr/bin/bash
   /usr/sbin/nologin

**Shell的分类:**

::

   * Bourne Shell（/usr/bin/sh或/bin/sh）
   * Bourne Again Shell（/bin/bash）
   * C Shell（/usr/bin/csh）
   * K Shell（/usr/bin/ksh）
   * Shell for Root（/sbin/sh）

**脚本命名:**

注意：见名知意，后缀规范为\ ``.sh``

三、第一个Shell
---------------

::

   #!/bin/bash
   echo "this is my first shell script"

``#!`` 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序
``/bin/bash`` 指定使用的是那种shell ``echo``\ 在终端打印出内容

四、执行Shell的三种方法
-----------------------

4.1 直接bash执行
~~~~~~~~~~~~~~~~

::

   [root@shell workspace]# ll
   total 4
   -rw-r--r-- 1 root root 44 Sep  3 14:16 01-scripts.sh
   [root@shell workspace]# cat 01-scripts.sh 
   #!/bin/bash

   echo "this is my first script"
   [root@shell workspace]# bash 01-scripts.sh 
   this is my first script

4.2 ./执行
~~~~~~~~~~

::

   [root@shell workspace]# ./01-scripts.sh
   -bash: ./01-scripts.sh: Permission denied
   [root@shell workspace]# chmod +x 01-scripts.sh 
   [root@shell workspace]# ll
   total 4
   -rwxr-xr-x 1 root root 44 Sep  3 14:16 01-scripts.sh
   [root@shell workspace]# ./01-scripts.sh 
   this is my first script

这种方式默认根据脚本第一行指定的解释器处理，如果没写以当前默认 Shell
解释器执行。

4.3 source执行
~~~~~~~~~~~~~~

::

   [root@shell workspace]# source 01-scripts.sh 
   this is my first script

五、Shell变量
-------------

变量名+内存空间

变量赋值：\ ``name=value``

弱类型变量，所有变量类型视为字符串类型，对于数值相加自动转换为数组类型，无需实现声明

5.1 变量命名规则：
~~~~~~~~~~~~~~~~~~

-  命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
-  中间不能有空格，可以使用下划线（_）。
-  不能使用标点符号。
-  不能使用bash里的关键字（可用help命令查看保留关键字）
-  做到见名知意

环境变量作用范围：当前shell进程及其子进程

本地变量作用范围：当前shell

局部变量作用范围：代码片段

利用export将本地变量导入到环境，扩大作用范围

5.2 系统内置变量
~~~~~~~~~~~~~~~~

在命令行提示符直接执行\ ``env、set``\ 查看系统或环境变量。\ ``env``
显示用户环境变量，\ ``set`` 显示 Shell
预先定义好的变量以及用户变量。可以通过 ``export`` 导出成用户变量。

还可通过\ ``printevn/declare -x``

::

   $SHELL      默认 Shell
       
   $HOME       当前用户家目录
       
   $IFS        内部字段分隔符
       
   $LANG       默认语言
       
   $PATH       默认可执行程序路径
       
   $PWD        当前目录
       
   $UID        当前用户 ID
       
   $USER       当前用户
       
   $HISTSIZE   历史命令大小，可通过 HISTTIMEFORMAT 变量设置命令执行时间
       
   $RANDOM     随机生成一个 0 至 32767 的整数
       
   $HOSTNAME   主机名

**特殊变量**

::

   ${1..n} 指定第n个输入的变量名称
   $0      脚本自身名字
       
   $?      返回上一条命令是否执行成功，0 为执行成功，非 0 则为执行失败
           
   $#      位置参数总数
       
   $*      所有的位置参数被看做一个字符串
       
   $@      每个位置参数被看做独立的字符串
       
   $$      当前进程 PID
       
   $!      上一条运行后台进程的 PID

**相同点**\ ：都是引用所有参数。

**不同点**\ ：只有在双引号中体现出来。假设在脚本运行时写了三个参数
1、2、3，，则 " \* " 等价于 “1 2 3”（传递了一个参数），而 “@” 等价于 “1”
“2” “3”（传递了三个参数）。

profile 类型： \* 定义全局变量 \* 运行命令或脚本

bashrc 类型： \* 定义本地变量 \* 定义命令别名

交互式登录shell： 加载顺序：/etc/profile -> /etc/profile.d/\* ->
~/.bash_profile -> ~/.bashrc -> /etc/bashrc

非交互式登录shell： 加载顺序：~/.bashrc -> /etc/bashrc ->
/etc/profile.d/\*

5.3 用户自定义变量
~~~~~~~~~~~~~~~~~~

-  普通变量

::

   [root@shell workspace]# var=normal
   [root@shell workspace]# echo $var
   normal

-  临时环境变量

在当前shell下定义的变量，只对当前shell有效，新的bash已经其子bash无法使用当前定义的shell，如果在本shell存在的情况下，使用\ ``export``\ 来导入到系统变量中，如果当前shell终端终端，那么导入的变量将全部失效，永久生效需要写入linux配置文件中。

-  只读变量

::

   [root@shell ~]# var='test'
   [root@shell ~]# echo $var
   test
   [root@shell ~]# readonly var
   [root@shell ~]# var='bbb'
   -bash: var: readonly variable

-  删除变量

``unset variable_name``

变量被删除后不能再次使用。unset 命令不能删除只读变量。

5.4 变量引用
~~~~~~~~~~~~

-  = 变量赋值
-  += 变量相加

::

   [root@shell data]# var=123
   [root@shell data]# var+=234
   [root@shell data]# echo $var
   123234

为避免特殊字符及变量与字符连接使用，建议引用变量添加大括号

六、引号
--------

单引号是告诉 Shell
忽略特殊字符，而双引号则解释特殊符号原有的意义，比如$、！。

::

   [root@xuel-tmp-shell www]# var1="aaa"
   [root@xuel-tmp-shell www]# echo '$var1'
   $var1
   [root@xuel-tmp-shell www]# echo "$var1"
   aaa

::

   [root@xuel-tmp-shell www]# var2="aa"
   [root@xuel-tmp-shell www]# var3='bb $var2'
   [root@xuel-tmp-shell www]# echo $var3
   bb $var2
   [root@xuel-tmp-shell www]# var4="bb $var2"
   [root@xuel-tmp-shell www]# echo $var4
   bb aa

七、注释
--------

-  单行注释使用\ ``#``
-  多行注释固定函数格式

::

   :<<EOF
   内容...
   内容...
   EOF
