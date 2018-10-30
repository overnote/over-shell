一、基础知识
============

一、Shell简介
-------------

Shell 是一个 C 语言编写的脚本语言，它是用户与 Linux
的桥梁，用户输入命令交给 Shell 处理， Shell
将相应的操作传递给内核（Kernel），内核把处理的结果输出给用户。

程序=指令+数据 ## 二、Shell分类 #### 2.1 图形界面 Shell（GUI Shell） GUI
为 Unix 或者类 Unix
操作系统构造一个功能完善、操作简单以及界面友好的桌面环境。主流桌面环境有
KDE，Gnome 等。 #### 2.2 命令行界面 Shell（CLI Shell） CLI
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

**Shell的分类:** \* Bourne Shell（/usr/bin/sh或/bin/sh） \* Bourne Again
Shell（/bin/bash） \* C Shell（/usr/bin/csh） \* K Shell（/usr/bin/ksh）
\* Shell for Root（/sbin/sh）

**脚本命名:**

注意：见名知意，后缀规范为\ ``.sh``

三、第一个Shell
---------------

::

   #!/bin/bash
   echo "this is my first shell script"

``#!`` 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序
``/bin/bash`` 指定使用的是那种shell ``echo``\ 在终端打印出内容 ##
四、执行Shell的三种方法 #### 4.1 直接bash执行

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
^^^^^^^^^^

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
^^^^^^^^^^^^^^^^^^

-  命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
-  中间不能有空格，可以使用下划线（_）。
-  不能使用标点符号。
-  不能使用bash里的关键字（可用help命令查看保留关键字）
-  做到见名知意
-  。

环境变量作用范围：当前shell进程及其子进程

本地变量作用范围：当前shell

局部变量作用范围：代码片段

利用export将本地变量导入到环境，扩大作用范围

5.2 系统内置变量
^^^^^^^^^^^^^^^^

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
^^^^^^^^^^^^^^^^^^

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
^^^^^^^^^^^^

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
忽略特殊字符，而双引号则解释特殊符号原有的意义，比如\ :math:`、！。 ``` [root@xuel-tmp-shell www]# var1="aaa" [root@xuel-tmp-shell www]# echo '`\ var1’
:math:`var1 [root@xuel-tmp-shell www]# echo "`\ var1" aaa

::

[root@xuel-tmp-shell www]# var2=“aa” [root@xuel-tmp-shell www]# var3=‘bb
$var2’ [root@xuel-tmp-shell www]# echo $var3 bb $var2
[root@xuel-tmp-shell www]# var4=“bb $var2” [root@xuel-tmp-shell www]#
echo $var4 bb aa

::



   ## 七、注释
   * 单行注释使用`#`
   * 多行注释固定函数格式

:<<EOF 内容… 内容… EOF

::


   # 二、字符串与数组
   ## 一、字符串常用操作
   #### 1.1 获取字符串长度
   利用`${#var}`来获取字符串长度

[root@xuel-tmp-shell ~]# var=‘abcstring’ [root@xuel-tmp-shell ~]# echo
${#var} 9

::


   #### 1.2 字符串切片
   格式：

   ${parameter:offset}
   ${parameter:offset:length}

   截取从 offset 个字符开始，向后 length 个字符。

[root@xuel-tmp-shell ~]# var=“hello shell” [root@xuel-tmp-shell ~]# echo
${var:0} hello shell [root@xuel-tmp-shell ~]# echo ${var:0:5} hello
[root@xuel-tmp-shell ~]# echo ${var:6:5} shell [root@xuel-tmp-shell ~]#
echo ${var:(-1)} l [root@xuel-tmp-shell ~]# echo ${var:(-2)} ll
[root@xuel-tmp-shell ~]# echo ${var:(-5):2} sh

::


   #### 1.3 字符串替换
   格式：${parameter/pattern/string}

[root@xuel-tmp-shell ~]# var=“hello shell” [root@xuel-tmp-shell ~]# echo
${var/shell/world} hello world

::



   #### 1.4 字符串截取
   格式：


   ${parameter#word}
   \# 删除匹配前缀

   ${parameter##word}


   ${parameter%word}
   \# 删除匹配后缀

   ${parameter%%word}

   \# 去掉左边，最短匹配模式，##最长匹配模式。

   % 去掉右边，最短匹配模式，%%最长匹配模式。

[root@xuel-tmp-shell ~]# url=“https://www.baidu.com/index.html”
[root@xuel-tmp-shell ~]# echo ${url#\ */} /www.baidu.com/index.html
[root@xuel-tmp-shell ~]# echo ${url##*/} index.html

[root@xuel-tmp-shell ~]# echo ${url%/\ *} https://www.baidu.com
[root@xuel-tmp-shell ~]# echo ${url%%/*} https:

::


   #### 1.5 变量状态赋值
   ${VAR:-string}  如果 VAR 变量为空则返回 string

   ${VAR:+string}  如果 VAR 变量不为空则返回 string

   ${VAR:=string} 如果 VAR 变量为空则重新赋值 VAR 变量值为 string 

   ${VAR:?string} 如果 VAR 变量为空则将 string 输出到 stderr

[root@xuel-tmp-shell ~]# url=“https://www.baidu.com/index.html”
[root@xuel-tmp-shell ~]# echo ${url:-“string”}
https://www.baidu.com/index.html [root@xuel-tmp-shell ~]# echo
${url:+“string”} string [root@xuel-tmp-shell ~]# unset url
[root@xuel-tmp-shell ~]# echo $url

[root@xuel-tmp-shell ~]# echo ${url:-“string”} string
[root@xuel-tmp-shell ~]# echo ${url:+“string”}

找出/etc/group下的所有组名称 for i in ``cat /etc/group``;do echo
${i%%:*};done

::

   ## 二、数组
   bash支持一维数组（不支持多维数组），并且没有限定数组的大小。数组是相同类型的元素按一定顺序排列的集合。
   类似与 C 语言，数组元素的下标由 0 开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于 0。
   #### 2.1 数组定义
   在 Shell 中，用括号来表示数组，数组元素用"空格"符号分割开

[root@xuel-tmp-shell ~]# args1=(aa bb cc 1123) [root@xuel-tmp-shell ~]#
echo $args1 aa

[root@xuel-tmp-shell ~]# echo ${args1[@]} aa bb cc 1123

::

   #### 2.2 数组元素读取

| [root@xuel-tmp-shell ~]# args1=(aa bb cc 1123) [root@xuel-tmp-shell
  ~]# echo ${#args1[@]} #获取数组元素个数
| 4 [root@xuel-tmp-shell ~]# echo ${args1[0]} aa [root@xuel-tmp-shell
  ~]# echo ${args1[1]} bb

[root@monitor workspace]# filelist=($(ls)) [root@monitor workspace]#
echo ${filelist[*]} check_url_for.sh check_url_while01.sh
check_url_while02.sh func01.sh func02.sh func03.sh urllist.txt

获取数组元素的下标 [root@monitor workspace]# echo
:math:`{!filelist[@]} 0 1 2 3 4 5 6 ``` 遍历文件 ``` filelist=(`\ (ls));for
i in ${!filelist[@]};do echo :math:`{filelist[`\ i]};done

::


   ## 三、字符显示颜色

   字体颜色 | 字体背景颜色 | 显示方式
   --------|---|---
   30：黑    |    40：黑   |   
   31：红    |   41：深红|  0：终端默认设置
   32：绿    |   42：绿    |   1：高亮显示
   33：黄    |   43：黄色|  4：下划线
   34：蓝色|  44：蓝色|  5：闪烁
   35：紫色|  45：紫色|  7：反白显示
   36：深绿|  46：深绿|  8：隐藏
   37：白色|  47：白色|  
   格式： |   
   \033[1;31;40m|  # 1 是显示方式，可选。31 是字体颜色。40m 是字体背景颜色。
   \033[0m | # 恢复终端默认颜色，即取消颜色设置。


   * 显示方式

for i in {1..8};do echo -e “\\033[$i;31;40m hello world \\033[0m”;done

::


   * 字体颜色

for i in {30..37};do echo -e “\\033[$i;40m hello world \\033[0m”;done

::


   * 背景颜色

for i in {40..47};do echo -e “\\033[47;${i}m hello world! \\033[0m”;done

::


   # 三、运算符
   ## 一、Shell表达式
   #### 1.1 整数比较符

   比较符                  |       描述      |                     示例
   ---|---|---
   -eq，equal              |       等于      |      [ 1 -eq        1       ]为 true
   -ne，not equal          |       不等于    |      [ 1 -ne        1       ]为 false            |     
   -gt，greater than       |       大于      |      [ 2    -gt     1       ]为 true            |     
   -lt，lesser than        |       小于      |      [ 2    -lt     1       ]为 false          |     
   -ge，greater or equal   |       大于或等于|     [ 2     -ge             1 ]为 true         |     
   -le，lesser or equal    |       小于或等于|     [ 2     -le     1       ]为 false

[root@monitor ~]# [ 1 -gt 1 ] && echo true \|\| echo false false
[root@monitor ~]# [ 1 -ne 1 ] && echo true \|\| echo false false
[root@monitor ~]# [ 1 -eq 1 ] && echo true \|\| echo false true
[root@monitor ~]# [ 1 -ne 1 ] && echo true \|\| echo false false
[root@monitor ~]# [ 1 -gt 1 ] && echo true \|\| echo false false
[root@monitor ~]# [ 2 -gt 1 ] && echo true \|\| echo false true
[root@monitor ~]# [ 2 -lt 1 ] && echo true \|\| echo false false
[root@monitor ~]# [ 2 -le 1 ] && echo true \|\| echo false false

::



   #### 1.2 算术运算符
   假定变量 a 为 10，变量 b 为 20：
   注意：运算符两边有空格

   运算符 | 说明 | 举例
   ---|---|---
   + | 加法 |    `expr $a + $b` 结果为 30。
   - | 减法 |    `expr $a - $b` 结果为 -10。
   * | 乘法 |    `expr $a \* $b` 结果为  200。
   / | 除法 |    `expr $b / $a` 结果为 2。
   % | 取余 |    `expr $b % $a` 结果为 0。
   = | 赋值 |    a=$b 将把变量 b 的值赋给 a。
   == |    相等| 用于比较两个数字，相同则返回 true。    [ $a == $b ] 返回 false。
   !=  | 不相等|用于比较两个数字，不相同则返回 true。 [ $a != $b ] 返回 true。

A=3 B=6 1、let 算术运算表达式 let C=\ :math:`A+`\ B
2、\ :math:`[算术运算表达式] C=`\ [:math:`A+`\ B]
3、\ :math:`((算术运算表达式)) C=`\ ((:math:`A+`\ B)) 4、expr
算术运算表达式，表达式中各操作数及运算符之间要有空格，而且要使用命令引用
C=\ ``expr $A + $B``

::



   #### 1.3 布尔运算符


   运算符 |   说明 |    举例
   ---|---|---
   !   | 非运算，表达式为 true 则返回 false，否则返回 true。|   [ ! false ] 返回 true。
   -o |    或运算，有一个表达式为 true 则返回 true。 |    [ $a -lt 20 -o $b -gt 100 ] 返回 true。
   -a  | 与运算，两个表达式都为 true 才返回 true。|   [ $a -lt 20 -a $b -gt 100 ] 返回 false。

   #### 1.4 逻辑运算符


   运算符 | 说明 |  举例
   ---|---|---
   &&  | 逻辑的 AND | [[ $a -lt 100 && $b -gt 100 ]] 返回 false
   \|\|  | 逻辑的 OR   |  [[ $a -lt 100 \|\| $b -gt 100 ]] 返回 true
   #### 1.5 文件测试运算符

   操作符 | 说明 |  举例
   ---|---|---
   -b| file        检测文件是否是块设备文件，如果是，则返回 true。    |     [ -b $file ] 返回 false。
   -c| file        检测文件是否是字符设备文件，如果是，则返回 true。   |    [ -c $file ] 返回 false。
   -d| file        检测文件是否是目录，如果是，则返回 true。     | [ -d $file ] 返回 false。 
   -f| file        检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。   |  [ -f $file ] 返回 true。
   -g| file        检测文件是否设置了 SGID 位，如果是，则返回 true。     |  [ -g $file ] 返回 false。
   -k| file        检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  |   [ -k $file ] 返回 false。
   -p| file        检测文件是否是有名管道，如果是，则返回 true。  | [ -p $file ] 返回 false。
   -u| file        检测文件是否设置了 SUID 位，如果是，则返回 true。   |    [ -u $file ] 返回 false。
   -r| file        检测文件是否可读，如果是，则返回 true。| [ -r $file ] 返回 true。
   -w| file        检测文件是否可写，如果是，则返回 true。 | [ -w $file ] 返回 true。
   -x| file        检测文件是否可执行，如果是，则返回 true。    |   [ -x $file ] 返回 true。
   -s| file        检测文件是否为空（文件大小是否大于0），不为空返回 true。     |   [ -s $file ] 返回 true。
   -e| file        检测文件（包括目录）是否存在，如果是，则返回 true。   |  [ -e $file ] 返回 true。



   #### 1.6 字符串测试
   假定变量 a 为 "abc"，变量 b 为 "efg"：

   运算符 |   说明 |    举例
   ---|---|---
   =   | 检测两个字符串是否相等，相等返回 true。 |  [ $a = $b ] 返回 false。
   != |    检测两个字符串是否相等，不相等返回 true。 |   [ $a != $b ] 返回 true。
   -z  | 检测字符串长度是否为0，为0返回 true。    | [ -z $a ] 返回 false。
   -n  | 检测字符串长度是否为0，不为0返回 true。|  [ -n "$a" ] 返回 true。
   str | 检测字符串是否为空，不为空返回 true。|    [ $a ] 返回 true。



   # 四、流程控制
   ## 一、if语句
   #### 1.1 单分支

if condition then command1 command2 … commandN fi

::

   eg:

if [ ``ps -ef |grep /usr/sbin/sshd|grep -v grep|wc -l`` -eq 1 ];then
echo “sshd server exist”;fi

::


   #### 1.2 双分支

if condition then command1 command2 … commandN else command fi

::

   eg:

if [ ``ps -ef |grep /usr/sbin/sshd|grep -v grep|wc -l`` -eq 0 ];then
echo “sshd server exist”;else echo “sshd server not exist”;fi

::

   #### 1.3 多分支

if condition1 then command1 elif condition2 then command2 else commandN
fi

::

   eg:

#! /bin/bash

cmd=\ ``rpm -q centos-release|cut -d- -f3``

if [ $cmd -eq 6 ];then echo “sysversion is $cmd” elif [ $cmd -eq 7
];then echo “sysversion is $cmd” else echo “sysversion is
``rpm -q centos-release``” fi

::

   ## 二、for循环

for var in item1 item2 … itemN do command1 command2 … commandN done

::

   eg1:

for i in /*; do echo -e " :raw-latex:`\c"`; find $i \|wc -l|sort -nr;
done

::

   eg2:

#!/bin/bash for i in {1..3}; do echo
:math:`i done ``` eg3: ``` #!/bin/bash for i in "`\ @"; { #
$@是将位置参数作为单个来处理 echo $i }

::


   默认 for 循环的取值列表是以空白符分隔，也就是第一章讲系统变量里的$IFS:

#!/bin/bash OLD_IFS=$IFS IFS=“:” for i in $(head -1 /etc/passwd); do
echo $i done

::

#!/bin/bash

for ip in 192.168.1.{1..254}; do

::

   if ping -c 1 $ip >/dev/null; then

       echo "$ip OK."

   else

       echo "$ip NO!"

   fi

done

::


   读取文件,判断url可用性

#!/bin/bash #function:check url filename=urllist.txt for url in $(cat
$filename) do status=\ ``curl -I $url -s|awk '/HTTP/{print $2}'`` if [
$status == “200” ];then echo “Url:$url is ok!status is
:math:`status" else  echo "Url:`\ url is error!status is $status” fi
done

::


   ## 三、while语句
   格式：

while 条件表达式:do command done

::


   eg1:

#!/bin/bash N=0 while [ $N -lt 5 ]; do let N++ echo $N done

::


   条件表达式为 true，将会产生死循环,利用此可以将脚本一直放在后台进行执行
   eg2:

#!/bin/bash IP=10.75.128.8 dir=“/DATA/oracle/netdir/” if [ ! -d ${dir}
];then mkdir -p ${dir} fi echo 1 >
:math:`{dir}ping.lock while true do  Time=`date +%F`  TIME="`\ {Time}
23:59" if [ “:math:`{data}" == "`\ {TIME}” ];then mkdir
:math:`{dir}`\ {Time} && mv ${dir}ping2.log
:math:`{dir}`\ {Time}-ping2.log mv :math:`{dir}`\ {Time}-ping2.log
:math:`{dir}`\ {Time} fi find ${dir} -mtime +7 -name "*-ping2.log" -exec
rm -rf {} ; find ${dir} -mtime +7 -type d -exec rm -rf {} ;

::

   data=`date +%F' '%H:%M`
   data1=`date +%F' '%H:%M:%S`
   echo "------------${data1}---------------">>${dir}ping2.log
   ping -c 10 ${IP} >>${dir}ping2.log
   if [ $? -eq 1 ];then
       STAT=`cat ${dir}ping.lock`
       if [ ${STAT} -eq 1 ];then
           /usr/bin/python /DATA/oracle/netdir/GFweixin.py xuel GLP-VPN "GLP from PDC(172.16.6.1

50) ping 金融云(10.75.128.8)中断，请检查深信服VPN！
    :raw-latex:`\n `TIME:${data1}" echo 0 > ${dir}ping.lock else
    continue fi else STAT=\ ``cat ${dir}ping.lock`` if [ ${STAT} -eq 0
    ];then /usr/bin/python /DATA/oracle/netdir/GFweixin.py xuel GLP-VPN
    "GLP from PDC(172.16.6.1
51) ping 金融云(10.75.128.8)恢复！ :raw-latex:`\n `TIME:${data1}" echo 1
    > ${dir}ping.lock else continue fi fi

done

::

   文件处理
   eg3: 

#!/bin/bash #function:check url filename=urllist.txt cat $filename \|
while read url;do status=\ ``curl -I $url -s|awk '/HTTP/{print $2}'`` if
[ $status == “200” ];then echo “Url:$url is ok!status is
:math:`status" else  echo "Url:`\ url is error!status is $status” fi
done

::

   或

#!/bin/bash #function:check url filename=urllist.txt while read url; do
status=\ ``curl -I $url -s|awk '/HTTP/{print $2}'`` if [ $status ==
“200” ];then echo "Url:$url is ok!status is
:math:`{status}" else  echo "Url:`\ url is error!status is
:math:`{status}" fi done <`\ filename

::

   ## 四、break 和 continue 语句
   break跳出循环

#!/bin/bash

N=0 while true; do let N++ if [ $N -eq 5 ]; then break fi echo $N done

::


   continue

#!/bin/bash N=0 while [ $N -lt 5 ]; do let N++ if [ $N -eq 3 ]; then
continue fi echo $N done

::

   ## 五、case语句
   语句

case 模式名 in 模式 1) 命令 ;; 模式 2) 命令 ;; \*)
不符合以上模式执行的命令 esac

::


   eg

| #!/bin/bash case $1 in start) echo “start.”
| ;; stop) echo “stop.” ;; restart) echo “restart.” ;; \*) echo “Usage:
  $0 {start|stop|restart}” esac

::


   # 五、函数
   ## 一、概念
   linux shell 可以用户定义函数，然后在shell脚本中可以随便调用,以此来重复调用公共函数，减少代码量。

   ## 二、格式

[ function ] funname() { action; [return int;] }

::

   说明：

   * function 关键字可写，也可不写。
   * 参数返回，可以显示加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255）,hell 函数返回值只能是整形数值，一般是用来表示函数执行成功与否的，0表示成功，其他值表示失败。因而用函数返回值来返回函数执行结果是不合适的。如果要硬生生地return某个计算结果，比如一个字符串，往往会得到错误提示：“numeric
    argument required”。
   如果一定要让函数返回一个或多个值，可以定义全局变量，函数将计算结果赋给全局变量，然后脚本中其他地方通过访问全局变量，就可以获得那个函数“返回”的一个或多个执行结果了。

#!/bin/bash function output_data() { DATA=$((1+1)) return $DATA }
output_data echo $?

::

#!/bin/bash # function:add number function add_num() { echo
“请输入第一个数：” read number01 echo “请输入第二个数字” read number02
if [[ ":math:`number01" =~ ^[0-9]+` ]] && [[
":math:`number02" =~ ^[0-9]+` ]];then
sum=\ :math:`((`\ number01+\ :math:`number02))  echo "`\ number01 +
$number02 = $sum" else echo “input must be number” fi } add_num

::


   ## 三、函数参数
   将函数写成无状态的，将数据当做参数进行传入

#!/bin/bash funWithParam(){ echo “第一个参数为 $1 !” echo “第二个参数为
$2 !” echo “第十个参数为 $10 !” echo “第十个参数为 ${10} !” echo
“第十一个参数为 ${11} !” echo “参数总数有 $# 个!” echo
“作为一个字符串输出所有参数 $\* !” echo “作为一个字符串输出所有参数 $@
!”

} funWithParam ``seq 1 20``

::

${1..n} 指定第n个输入的变量名称 $0 脚本自身名字

$? 返回上一条命令是否执行成功，0 为执行成功，非 0 则为执行失败

$# 位置参数总数

$\* 所有的位置参数被看做一个字符串

$@ 每个位置参数被看做独立的字符串

$$ 当前进程 PID

$! 上一条运行后台进程的 PID

::


   eg:函数炸弹

:(){ :|:& };:

::

   :|: 表示每次调用函数":"的时候就会生成两份拷贝。

   & 放到后台

   递归调用自身，直至系统崩溃


   # 六、正则表达式
   ## 一、基本正则表达式
   #### 1.1 字符匹配
   * .:匹配任意单个字符
   * []:匹配指定范围内的任意单个字符
   * \[^]:匹配指定范围外的任意单个字符
   * [:digit:]匹配元字符

posix字符 [:alnum:] 字母数字[a-z A-Z 0-9] [:alpha:]字母[a-z A-Z]
[:blank:]空格或制表键 [:cntrl:] 任何控制字符 [:digit:] 数字 [0-9]
[:graph:] 任何可视字符（无空格） [:lower:] 小写 [a-z] [:print:]
非控制字符 [:punct:] 标点字符 [:space:] 空格 [:upper:] 大写 [A-Z]
[:xdigit:] 十六进制数字 [0-9 a-f A-F]

::

特殊字符 :raw-latex:`\w `匹配任意数字和字母，等效[a-zA-Z0-9_]
:raw-latex:`\W `和:raw-latex:`\w相反`，等效[^a-zA-Z0-9_]
:raw-latex:`\b `匹配字符串开始或结束，等效<和>
:raw-latex:`\s `匹配任意的空白字符 :raw-latex:`\S `匹配非空白字符

::



   #### 1.2 次数匹配
   用在制定的字符后面，表示制定前面的字符出现多少次
   * \*:匹配前面的字符任意次（0次获无数次）
   * \?:匹配前面的字符0次或1次
   * \+:匹配前面的字符至少1次
   * {m\,}:匹配前面的字符至少m次（默认工作在贪婪模式下，?取消贪婪模式）
   * {m,n}:匹配前面的字符至少m次，至多n次
   eg:

.*:匹配任意字符任意次数

::


   #### 1.3 位置锚定
   * ^:行首锚定，用于模式最左边
   * $:行尾锚定,用于模式最右边
   * \\<或\b:锚定词首，用于单词模式左侧
   * \\>或\b:锚定词尾，用于单词模式右侧



   eg:

^$:锚定空行

::


   #### 1.4 分组引用
   分组
   * \(\):将一个或多个字符当成一个整体来进行后续处理

   引用
   * 1：从左侧起，引用第一个左括号以及与之匹配右括号之间的模式所匹配到的字符，后向引用


   exercises:

1.显示/etc/init.d/functions文件中以大小s开头的行(使用两种方式) grep
‘ [1]_’ /etc/init.d/functions grep -i “^p” /etc/init.d/functions

2.显示/etc/passwd文件中不以/bin/bash结尾的行 grep -v “/bin/bash$”
/etc/passwd

3.显示/etc/passwd文件中ID号最大用户的用户名 sort -t: -k3 -n /etc/passwd
\|tail -1 \|cut -d: -f1

4.如果root用户存在,显示其默认的shell程序 id root && grep ‘^<root>’
/etc/passwd \|awk -F: ‘{print $NF}’

5.找出/etc/passwd中的两位或三位数 grep -o -E “[0-9]{2,3}” /etc/passwd
grep -o “[0-9]{2,3}” /etc/passwd

6.显示/etc/rc.d/rc.sysinit文件中,至少以一个空白字符开头的且后面存非空白字符的行:
grep ‘ [2]_+[^[:space:]]’ /etc/rc.d/rc.sysinit

7.找出“netstat -tan”命令的结果以“LISTEN”后跟0,1或多个空白字符结尾的行
netstat -tan|grep ’LISTEN[[:space:]]*$’

8.如果root用户登录了系统,就显示root用户在线,否则说明未登录 w \|grep
‘^<root>’>/dev/null && echo “root在线”\|\| echo “root未登录”

9.找出/etc/rc.d/init.d/functions文件中某单词后面跟一对小括号的行 grep
’[[:alpha:]]*()’ /etc/rc.d/init.d/functions

10.使用echo输出一个路径,使用egrep取出基名 echo /tmp/tmp1/vmstat.8.gz
\|grep -E -o ‘[^/]+/?$’\|cut -d/ -f1 echo /tmp/tmp1/vmstat.8.gz \|awk
-F’/’ ‘{print $NF}’

11.匹配PPID开头，行中又再次出现PPID的内容。/etc/init.d/functions grep -E
"(PPID).*\1" /etc/init.d/functions

12.利用awk找出/etc/ssh/sshd_config内出过空行与以#开头的行 awk ‘!/^#/ &&
!/^\ :math:`/{print}' /etc/ssh/sshd_config grep -v -E '^#|^`’
/etc/ssh/sshd_config

::



   # 七、三剑客之grep
   ## 一、概念
   #### 1.1 
   grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。egrep是grep的扩展，支持更多的re元字符， fgrep就是fixed grep或fast grep，它们把所有的字母都看作单词，也就是说，正则表达式中的元字符表示回其自身的字面意义，不再特殊。linux使用GNU版本的grep。它功能更强，可以通过-G、-E、-F命令行选项来使用egrep和fgrep的功能。
   ## 二、语法格式
   grep [OPTION]... PATTERN [FILE]

   #### 2.1 选项
   * -i：忽略大小写
   * -c:统计匹配到字符串的次数
   * -n:顺便输出行号
   * -v:反向选择，显示没有匹配到的内容
   * -o：只显示匹配到的串 
   * -A:显示匹配到的字符后面的n行
   * -B:显示匹配到的字符前面的n行
   * -C:显示前后各n行
   #### 2.2 模式
   * 基本正则表达式元字符：

. :匹配任意单个字符 [] :匹配指定范围内的字符
[^]:匹配指定范围外的任意字符

::

   * 次数匹配（贪婪模式）

\*：匹配其前的字符0，1或者多次 ？：匹配其前的字符0或1次 {m,n}:
(m,):至少m次 {0,n}:至多n次 {m}:m次

::

   * 锚定符：

r..t ：root chroot \* 单词锚定： <:锚定词首：<r..t, :raw-latex:`\b`
>:锚定词尾：root> \* 行首行末锚定： ^: ^root, 行首 :math:`: root` 行尾
.*: 任意长度的任意字符

::

   * 分组：
   \(\)
   \(abc\)
   * 引用：

\\1
:后向引用，引用前面的第一个左括号与与之对应的右括号中的模式所匹配到的内容

::


   eg:

dmesg \|grep -n eth0 grep -E ‘/.{2,3}’ /etc/passwd

::


   取掉空行

grep -E -v “:sup:`$\|`\ #” /etc/httpd/conf/httpd.conf

::


   查看mysql中的库

$(mysql -uroot -p’passwd’ -e “show databases;”\|egrep -v
‘Database|^test|mysql|performance_schema|information_schema’)

::


   # 八、三剑客之sed
   ## 一、概念：
   sed是一种流编辑的文本处理工具，
   * 工作模式：将当前处理的行存储在临时缓冲区（模式空间），对缓冲区中的内容利用制定的动作进行处理，完成后输出到屏幕，接着反复重复执行此操作完成整改文件的处理。
   ## 二、适用场景
   * 大文件
   * 有规律的文本
   ## 三、语法
   sed [option] 'Addresscommand' [file ...]

   * 选项
       * -n:安静模式，仅显示script处理后的结果，不再默认显示模式空间中的内容 
       * -e:<script>或--expression=<script> 以选项中指定的script来处理输入的文本文件，可以同时执行多个脚本
       * -f:对制定的文件直接进行sed的command操作
       * -i:直接修改原文件
       * -r:支持扩展正则表达式
   * 地址定界
       * startline，endline
       * /regexp/
       * /pattern1/,/pattern2/:第一次被pattern1匹配到的行开始，直到被pattern2匹配到的行结束
       * linenuber：制定行号
       * startline，+n，从startline开始，向后n行结束
       * startline~step:步长，每隔step步
   * 命令操作
       * d: 删除符合条件的行； 
       * p: 显示符合条件的行； 
       * a \string: 在制定或匹配到的行后面追加新行，内容为string 
       * \n：可以用于换行 
       * i \string: 在制定或匹配到的行前面添加新行，内容为string 
       * s:s/pattern/string/修饰符: 查找并替换，默认只替换每行中第一次被模式匹配到的字符串 
   加修饰符 
       * g: 全局替换 
       * i: 忽略字符大小写 

   * 匹配元字符：
       ```
       ^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
       $ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
       . 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
       * 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
       [] 匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。  
       [^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
       \(..\) 匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。
       & 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
       \< 匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。
       \> 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
       x\{m\} 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
       x\{m,\} 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
       x\{m,n\} 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。
       ```
       
   eg:

1、删除/etc/grub.conf文件中行首的空白符；  sed -r ‘s/ [3]_+//g’
/etc/grub.conf
2、替换/etc/inittab文件中“id:3:initdefault:”一行中的数字为5； 
‘s/id:[0-9]/id:5/g’ /etc/inittab 3、删除/etc/inittab文件中注释行： sed
‘/^#/d’ /etc/inittab 4、取消/etc/inittab文件中开头的#号;  sed ‘s/^#//g’
/etc/inittab 5、打印文件/etc/services匹配blp5开头的行 sed -n ‘/^blp5/p’
/etc/services 6、打印2-5行 sed -n ‘2,5p’ /etc/services 7、打印奇数行 seq
10 \|sed -n ‘1~2p’ 8、打印最后一行 sed ‘$p’ /etc/services

::


   # 九、三剑客之awk
   ## 一、概念
   AWK：报告生成器，格式化文本输出工具

awk [options] ‘script’ file1,file2… awk [options] ‘PATTERN {action}’
file1,file2

::

   * 处理机制：一次从文件中读取出来一行，按照特定分隔符对其进行切片（默认空格）

   * 步骤：
       * 读( Read )：AWK 从输入流（文件、管道或者标准输入）中读入一行然后将其存入内存中。
       * 执行(Execute)：对于每一行输入，所有的 AWK 命令按顺执行。 默认情况下，AWK 命令是针对于每一行输入，但是我们可以将其限制在指定的模式中。
       * 重复（Repeate）：一直重复上述两个过程直到文件结束。
   * 程序结构：
       * 开始块（BEGIN BLOCK）：
       ```
       语法：
       BEGIN{awk-commands}
       开始块就是awk程序启动时执行的代码部分（在处理输入流之前执行），并且在整个过程中只执行一次；一般情况下，我们在开始块中初始化一些变量。BEGIN是awk的关键字，因此必须要大写。【注：开始块部分是可选，即你的awk程序可以没有开始块部分】
       ```

       * 主体块（Body Block）：
       ```
       语法：
       /pattern/{awk-commands}
       针对每一个输入的行都会执行一次主体部分的命令，默认情况下，对于输入的每一行，awk都会执行主体部分的命令，但是我可以使用/pattern/限制其在指定模式下。
       ```
       * 结束块（END BLOCK）：
       ```
       语法：
       END{awk-commands}
       结束块是awk程序结束时执行的代码（在处理完输入流之后执行），END也是awk的关键字，必须大写，与开始块类似，结束块也是可选的。
       ```

   #### 1.1 awk输出
   >1. print 使用格式

print item1,item2…

::

   **要点:**
   * 1.各字段之间逗号隔开，输出时以空白字符分割；

   * 2.输出的字段可以为字符串或数值，当前记录的字段（如$1）、变量或awk的表达式；数值先回转换成字符串然后输出

   * 3.print命令后面的item可以省略，此时其功能相当于print $0,如果想输出空白，可以使用`print ""`

   eg:

awk -F: ‘{print :math:`1,`\ NF}’ /etc/passwd|column -t

::

tail -1 /etc/passwd|awk -F: ‘BEGIN{OFS=“#”}{print “hello”$1,$2}’

::



   > 2.printf

   printf命令的使用格式:

printf item1,item2…

::

   **要点：**
   * 1.其与print命令最大区别,printf 需要指定format,format必须给出

   * 2.format用于指定后面的每个item输出格式

   * 3.printf 语句不会自动打印换行字符:\n


   format格式的指示符都以%开头，后跟一个字符:

%c:显示ascall码 %d:%i:十进制整数 %e，%E：科学计数法 %f:浮点数 %s：字符串
%u：无符号整数 %%：显示%自身

修饰符： #[.#]:第一个#控制显示的宽度：第二个#表示小数点后的精度：

%3.1f

-:左对齐 +：显示数组符号

::


   eg：

awk -F: ‘{printf “Username:%-15s ,Uid:%d:raw-latex:`\n`”,$1,$3}’
/etc/passwd

::


   #### 1.2 awk变量
   * awk内置变量之记录变量：
       + FS:field separator，输入字段分隔符（默认空白）
       + OFS:output field separator，输出字段分隔符
       + RS:Record separator:输入文本换行符（默认回车）

       + ORS:输出文本换行符

   * awk内置变量之数据变量
       + NR:the number of input records,awk命令所处理的文件的行数，如果有多个文件，这个数目会将处理的多个文件计数
       + NF:number of field,当前记录的field个数
       ```
       {print NF},{print $NF}
       ```

       * ARGV:数组，保存命令行本身这个字符串，

       * ARGC：awk命令的参数个数

       * FILENAME:awk命令处理的文件名称

       * ENVIRON：当前shell环境变量及其值的关联数组
       ```
       awk 'BEGIN{print ENVIRON["PATH"]}'
       ```

   * 自定义变量
       -v var=value

       变量名区分大小写
       ```
        awk -v test="abc" 'BEGIN{print test}'
       ```
       ```
       awk 'BEGIN{var="name";print var}'
       ```
       
   #### 1.3 操作符
   * 算术运算

       * +,-,*,/,^,%
       
       `awk 'BEGIN{a=5;b=3;print "a + b =",a+b}'`
   * 字符串操作
       * 无符号操作符，表示字符串连接
       ```
       awk 'BEGIN { str1="Hello,"; str2="World"; str3 = str1 str2; print str3 }'
       ```
   * 赋值操作符：
       * =，+=，-=，*=，/=，%=，^=
       ```
       awk 'BEGIN{a=5;b=6;if(a == b) print "a == b";else print "$a!=b"}'
       
       awk -F: '{sum+=$3}END{print sum}' /etc/passwd
       ```
   * 比较操作符：
       * \>,>=,<,<=,!=,==
   * 模式匹配符：
       * ~:是否匹配
       * !~:是否不匹配
       ```
       awk -F: '$1~"root"{print $0}' /etc/passwd
       ```
   * 逻辑操作符：
       * && 、 || 、 ！
       ```
       awk 'BEGIN{a=6;if(a > 0 && a <= 6) print "true";else print "false"}'
       ```
   * 函数调用：
       * function_name(argu1,augu2)
   * 条件表达式(三元运算):
       * selection？if-true-expresssion：if-false-expression
       ```
       awk -F: '{$3>=100?usertype="common user":usertype="sysadmin";printf "%15s:%s\n",$1,usertype}' /etc/passwd
       ```
   #### 1.4 Pattern
   * empty:空模式，匹配每一行
   * /regular expression/:仅处理能被此处模式匹配到的行
   * relational expression：关系表达式，结果为“真”有“假”，结果为“真”才会被处理,注意：使用模式需要使用双斜线括起来
       * 真：结果为非0值，非空字符串
       ```
       awk -F: '$3>100{print $1,$3}' /etc/passwd
       ```
       ```
       awk -F: '$NF=="/bin/bash"{printf "%15s,%s\n",$NF,$1}' /etc/passwd
       ```
       ```
       awk -F: '$NF~/bash$/{printf "%15s,%s\n",$NF,$1}' /etc/passwd
       ```
       ```
       df -Th|awk '/^\/dev/{print}'
       ```
   * line ranges：行范围，制定startline，endline
       ```
       awk -F: '/10/,/20/{print $1}' /etc/passwd
       awk -F: '(NR>2&&NR<=10){print $1}' /etc/passwd
       ```
   * BEGIN/END模式
       * BEGIN{}：仅在开始处理喂奶姐中的文本之前执行一次
       * END{}:仅在文本处理完成之后执行一次
       ```
       awk -F: 'BEGIN{print "username     uid\n--------------------"}{printf "%-15s:%d\n",$1,$3}END{print "-----------------\ne
   nd"}' /etc/passwd
       ```
       
       
   #### 1.5 常用action
   * Expression
   * Control statements
       * if/while
   * Compound statements
   * input statements
   * output statements

   #### 1.6 控制语句
   * if(condition) {statements}
   * if(condition) {statments} [else {statments}]
       ```
       awk -F: '{if($3>100) print $1,$3}' /etc/passwd
       ```
       ```
       awk -F: '{if($3>100) {printf "Common user:%-15s\n",$1} else {printf "sysadmin user:%-15s\n",$1}}' /etc/passwd
       ```
       ```
       awk -F: '{if($NF=="/bin/bash") print $1,$NF}' /etc/passwd
       ```
       ```
       awk -F: '{if($NF>7) print}' /etc/fstab
       ```
       
       

   * while(conditon) {statments}
       * 条件为“真”，进入循环，条件为“假”，退出循环
       * 使用场景：对一行内的多个字段逐一类似处理时使用，对数组内的各元素逐一进行处理时使用
       ```
       awk '/^[[:space:]]*if/{i=1;while(i<=NF) {print $i,length($i);i++}}' /etc/init.d/functions
       ```
       ```
       awk '/^[[:space:]]*if/{i=1;while(i<NF) {if(length($i)>7) {print $i,length($i)};i++}}' /etc/init.d/functions
       ```
       
   * do {statements} while(condition)
       * 意义：至少执行一次循环体
   * for(expr1;expr2;expr3) {statements}
       * 语法：for(variable assignment;condition;iteration process) {for-body}
       * 特殊用法：能够遍历数组中的元素，`for (var in array) {for-body}`
       ```
       awk '/^[[:space:]]*if/{for(i=1;i<NF;i++) {print $i,length($i)}}' /etc/init.d/functions 
       ```
       ```
       awk '/^[[:space:]]*if/{for(i=1;i<NF;i++) {if(length($i)>7) print $i,length($i)}}' /etc/init.d/functions
       ```
   * break
   * continue
   * delete array[index]
   * switch语句
       * 语法：switch(expression) {case VALUE1 or /REGEXP/: statement; case VALUE2 VALUE2 or /EXGEXP2/: statement;...;default: statement}



   ##### 1.6 array
   * 关联数组：
       * array[index-expression]
           * index-pression:
               * 任意字符串
               * 如果某数组元素事先不存在，引用时候，awk自动创建此元素，并将其值初始化为空串，若要判断数组中是否存在某元素，需要使用`index in array`格式进行
               * 
               ```
               awk 'BEGIN{weekdays["mon"]="Monday";weekdays["tue"]="Tuesday";print weekdays["tue"]}'
               ```
           * 遍历数组使用for循环
               * `for(var in array) {do-body}`
               ```
               awk 'BEGIN{weekdays["mon"]="Monday";weekdays["tue"]="Tuesday";for(i in weekdays) print weekdays[i]}'
               ```
               * 注意：var会遍历array的每个索引：
               ```
               netstat -tan|awk '/^tcp/{state[$NF]++}END{for(i in state) print i,state[i]}'
               ```
               ```
               awk '{ips[$1]++}END{for(i in ips){printf "%-5d,%s\n",ips[i],i}}' /var/log/httpd/access_log-20180916 |sort -k1 -nr
               ```
   #### 1.7 函数
   * 内置函数
       * 数值处理：
           rand():返回0和1之间的一个随机数
           ```
           awk 'BEGIN{print rand()}'
           ```
       * 字符串处理：
           length([s]):返回制定字符串的长度
           sub(r,s,[t])：以r表示的模式来查找t所表示的字符串中匹配到的内容，并将其第一次出现替换为s所表示的那日
           

       
   exercises:

-  统计/etc/fstab文件中每个单词出现的次数，并按从大到小排序 awk
   ‘{for(i=1;i<=NF;i++){words[$i]++}}END{for(key in words)print
   key,words[key]}’ /etc/fstab|sort -k2 -nr

awk ‘{ips[$1]++}END{for(i in ips) print i,ips[i]}’ access_nginx.log
\|column -t|sort -k2 -nr

-  统计/etc/fstab每个文件系统类型出现的次数 awk
   ‘!/\ :sup:`#/&&!/`\ $/{dev[$3]++}END{for(i in dev) print i,dev[i]}’
   /etc/fstab

-  ping一个域名，输出ping此刻的时间 ping baidu.com|awk ‘{print $0"
   “strftime(”%Y-%m-%d %H:%M:%S")}’

-  利用netstat监控服务是否正常监听 netstat -lntup|awk ’NR>2{if($4
   ~/.*:22/) print $0“yes”;exit 0}’

-  统计web服务器日志状态码 awk ‘$9~“[0-9]”{stat[$9]++}END{for(i in stat)
   print i,stat[i]}’ access_log

::


   # 十、shell杂项
   ## 一、输入输出
   * 输入输出
   文件描述符 | 描述 | 映射关系
   ---|---|---
   0 | 标准输入 | /dev/stdin -> /proc/self/fd/0
   1 | 标准输出 | /dev/stdout -> /proc/self/fd/1
   2 | 标准错误 | /dev/stderr -> /proc/self/fd/2

   * 重定向

..

   ：符号左边输出作为右边的输入 >: 符号左边输出追加右边的输入 < :
   符号右边输出作为左边输入（标准输入） <<: 符号右边输出追加左边输入 & :
   重定向绑定符号

::

   eg:

cat > file.txt </dev/null 2>&1

::


   ## 命令
   * tac:倒序打印文件
   * rev反向打印每一行
   * cut:字符切割，常用选项-d 分割，-f输出第几段
   * tr:替换或删除字符
   * seq:打印序列化数字
   * sort:排序
       -t:制定分隔符
       -k:制定field
       -r:倒序排序
       -u:去重行
   * uniq:去重 -c 打印出现次数、-u ：打印不重复的行
   * date:显示系统时间

时间加减： 显示前 30 秒：date -d ‘-30 second’ +‘%F %T’
显示前一分钟：date -d ‘-1 minute’ +‘%F %T’ 显示前一个时间：date -d ‘-1
hour’ +‘%F %T’ 显示前一个天：date -d ‘-1 day’ +‘%F %T’ 显示上一周：date
-d ‘-1 week’ +‘%F %T’ 显示上一个月日期：date -d ‘-1 month’ +%F
显示上一年日期：date -d ‘-1 year’ +%F 或 显示前一天日期：date -d
yesterday +%F 显示后一天日期：date -d tomorrow +%F

::

   * screen
       * screen -ls
       * screen -r 
       * screen -wipe

   脚本编写注意事项

1）开头加解释器：#!/bin/bash

2）语法缩进，使用四个空格；多加注释说明。

3）命名建议规则：变量名大写、局部变量小写，函数名小写，名字体现出实际作用。

4）默认变量是全局的，在函数中变量 local
指定为局部变量，避免污染其他作用域。

5）有两个命令能帮助我调试脚本：set -e 遇到执行非 0 时退出脚本，set -x
打印执行过程。

6）写脚本一定先测试再到生产上。

::



   * 实战
   * 文件扫描校验

#!/bin/bash #func:scan file #md5sum -c $SCAN_FILE

SCAN_DIR=\ ``echo $PATH |sed 's/:/ /g'`` SCAN_CMD=\ ``which md5sum``
SCAN_FILE_FALL="/tmp/scan\_\ :math:`(date +%F%H%m)_fall.txt" SCAN_FILE_BIN="/tmp/scan_`\ (date
+%F%H%m)_bin.txt"

scan_fall_disk() { echo “正在全盘扫描，请稍等！文件路径:$SCAN_FILE_FALL”
find / -type f -exec $SCAN_CMD {} ;>> $SCAN_FILE_FALL 2>/dev/null }

scan_bin() { echo
“正在扫描PATH可执行文件，请稍等，文件路径：$SCAN_FILE_BIN” for file in
$SCAN_DIR do find $filae -type f -exec $SCAN_CMD {} ;>> $SCAN_FILE_BIN
2>/dev/null done }

main() { [ $# -lt 1 ] && echo
“请使用参数，1表示全盘扫描，2表示二进制可执行文件扫描” read number case
$number in 1) scan_fall_disk;; 2) scan_bin;; \*) echo
“参数错误，1，表示全盘扫描，2表示二进制文件扫描” esac }

main

::


   自定义垃圾回收

#!/bin/bash # function:自定义rm命令，每天晚上定时清理

CMD_SCRIPTS=\ :math:`HOME/.rm_scripts.sh TRASH_DIR=`\ HOME/.TRASH_DIR
CRON_FILE=/var/spool/cron/root BASHRC=$HOME/.bashrc

[ ! -d ${TRASH_DIR} ] && mkdir -p ${TRASH_DIR} cat >
:math:`CMD_SCRIPTS <<EOF PARA_CNT=\$# TRASH_DIR=`\ TRASH_DIR

for i in $*; do DATE=$(date +%F%T) fileName=$(basename $i) mv $i
$TRASH_DIR/$fileName.$DATE done EOF

sed -i “s@$(grep ‘alias rm=’ $BASHRC)@alias rm=‘bash ${CMD_SCRIPTS}’@g”
$BASHRC source $HOME/.bashrc

echo "0 0 \* \* \* rm -rf $TRASH_DIR/*" >>
:math:`CRON_FILE echo "删除目录:`\ TRASH_DIR" echo
“删除脚本:$CMD_SCRIPTS” echo “请执行:source $BASHRC
来加载文件或退出当前shell重新登录” \``\`

.. [1]
   Pp

.. [2]
   [:space:]

.. [3]
   [:space:]
