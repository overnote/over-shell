.. contents::
   :depth: 3
..

七、三剑客之grep
================

一、概念
--------

grep (global search regular expression(RE) and print out the
line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。egrep是grep的扩展，支持更多的re元字符，
fgrep就是fixed grep或fast
grep，它们把所有的字母都看作单词，也就是说，正则表达式中的元字符表示回其自身的字面意义，不再特殊。linux使用GNU版本的grep。它功能更强，可以通过-G、-E、-F命令行选项来使用egrep和fgrep的功能。

二、语法格式
------------

grep [OPTION]… PATTERN [FILE]

2.1 选项
~~~~~~~~

-  -i：忽略大小写
-  -c:统计匹配到字符串的次数
-  -n:顺便输出行号
-  -v:反向选择，显示没有匹配到的内容
-  -o：只显示匹配到的串
-  -A:显示匹配到的字符后面的n行
-  -B:显示匹配到的字符前面的n行
-  -C:显示前后各n行
-  .. rubric:: 2.2 模式
      :name: 模式

-  基本正则表达式元字符：

::

   . :匹配任意单个字符
   [] :匹配指定范围内的字符
   [^]:匹配指定范围外的任意字符

-  次数匹配（贪婪模式）

::

   *：匹配其前的字符0，1或者多次
   ？：匹配其前的字符0或1次
   \{m,n\}:
       \(m,\):至少m次
       \{0,n\}:至多n次
       \{m\}:m次

-  锚定符：

::

   r..t  ：root  chroot
   * 单词锚定：
   \<:锚定词首：\<r..t,    \b
   \>:锚定词尾：root\>
   * 行首行末锚定：
   ^: ^root,  行首
   $:    root$ 行尾
   .*:  任意长度的任意字符

-  分组： () (abc)
-  引用：

::

   \1 :后向引用，引用前面的第一个左括号与与之对应的右括号中的模式所匹配到的内容

eg:

::

   dmesg |grep -n eth0
   grep -E '/.{2,3}' /etc/passwd

取掉空行

::

   grep -E -v "^$|^#" /etc/httpd/conf/httpd.conf

查看mysql中的库

::

   $(mysql -uroot -p'passwd' -e "show databases;"|egrep -v 'Database|^test|mysql|performance_schema|information_schema')
