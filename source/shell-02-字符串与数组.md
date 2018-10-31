
# 二、字符串与数组

## 一、字符串常用操作

#### 1.1 获取字符串长度

利用`${#var}`来获取字符串长度

```
[root@xuel-tmp-shell ~]# var='abcstring'
[root@xuel-tmp-shell ~]# echo ${#var}
9
```

#### 1.2 字符串切片

格式：

${parameter:offset}
${parameter:offset:length}

截取从 offset 个字符开始，向后 length 个字符。

```
[root@xuel-tmp-shell ~]# var="hello shell"
[root@xuel-tmp-shell ~]# echo ${var:0}
hello shell
[root@xuel-tmp-shell ~]# echo ${var:0:5}
hello
[root@xuel-tmp-shell ~]# echo ${var:6:5}
shell
[root@xuel-tmp-shell ~]# echo ${var:(-1)}
l
[root@xuel-tmp-shell ~]# echo ${var:(-2)}
ll
[root@xuel-tmp-shell ~]# echo ${var:(-5):2}
sh

```

#### 1.3 字符串替换

格式：${parameter/pattern/string}

```
[root@xuel-tmp-shell ~]# var="hello shell"
[root@xuel-tmp-shell ~]# echo ${var/shell/world}
hello world
```

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

```
[root@xuel-tmp-shell ~]# url="https://www.baidu.com/index.html"
[root@xuel-tmp-shell ~]# echo ${url#*/}
/www.baidu.com/index.html
[root@xuel-tmp-shell ~]# echo ${url##*/}
index.html

[root@xuel-tmp-shell ~]# echo ${url%/*}
https://www.baidu.com
[root@xuel-tmp-shell ~]# echo ${url%%/*}
https:
```

#### 1.5 变量状态赋值

${VAR:-string}	如果 VAR 变量为空则返回 string

${VAR:+string}	如果 VAR 变量不为空则返回 string

${VAR:=string} 如果 VAR 变量为空则重新赋值 VAR 变量值为 string 

${VAR:?string} 如果 VAR 变量为空则将 string 输出到 stderr

```
[root@xuel-tmp-shell ~]# url="https://www.baidu.com/index.html"
[root@xuel-tmp-shell ~]# echo ${url:-"string"}
https://www.baidu.com/index.html
[root@xuel-tmp-shell ~]# echo ${url:+"string"}
string
[root@xuel-tmp-shell ~]# unset url
[root@xuel-tmp-shell ~]# echo $url

[root@xuel-tmp-shell ~]# echo ${url:-"string"}
string
[root@xuel-tmp-shell ~]# echo ${url:+"string"}


找出/etc/group下的所有组名称
for i in `cat /etc/group`;do echo ${i%%:*};done

```

## 二、数组

bash支持一维数组（不支持多维数组），并且没有限定数组的大小。数组是相同类型的元素按一定顺序排列的集合。
类似与 C 语言，数组元素的下标由 0 开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于 0。

#### 2.1 数组定义

在 Shell 中，用括号来表示数组，数组元素用"空格"符号分割开

```
[root@xuel-tmp-shell ~]# args1=(aa bb cc 1123)
[root@xuel-tmp-shell ~]# echo $args1
aa

[root@xuel-tmp-shell ~]# echo ${args1[@]}
aa bb cc 1123
```

#### 2.2 数组元素读取

```
[root@xuel-tmp-shell ~]# args1=(aa bb cc 1123)
[root@xuel-tmp-shell ~]# echo ${#args1[@]}     #获取数组元素个数  
4
[root@xuel-tmp-shell ~]# echo ${args1[0]}
aa
[root@xuel-tmp-shell ~]# echo ${args1[1]}
bb

[root@monitor workspace]# filelist=($(ls))
[root@monitor workspace]# echo ${filelist[*]}
check_url_for.sh check_url_while01.sh check_url_while02.sh func01.sh func02.sh func03.sh urllist.txt

获取数组元素的下标
[root@monitor workspace]# echo ${!filelist[@]}
0 1 2 3 4 5 6
```

遍历文件

```
filelist=($(ls));for i in ${!filelist[@]};do echo ${filelist[$i]};done
```

## 三、字符显示颜色

字体颜色 | 字体背景颜色 | 显示方式
--------|---|---
30：黑	|	 40：黑	|   
31：红	|	41：深红|	0：终端默认设置
32：绿	|	42：绿	|	1：高亮显示
33：黄	|	43：黄色|	4：下划线
34：蓝色|	44：蓝色|	5：闪烁
35：紫色|	45：紫色|	7：反白显示
36：深绿|	46：深绿|	8：隐藏
37：白色|	47：白色|	
格式：	|	
\033[1;31;40m|	# 1 是显示方式，可选。31 是字体颜色。40m 是字体背景颜色。
\033[0m	| # 恢复终端默认颜色，即取消颜色设置。


* 显示方式

```
for i in {1..8};do echo -e "\033[$i;31;40m hello world \033[0m";done
```

* 字体颜色

```
for i in {30..37};do echo -e "\033[$i;40m hello world \033[0m";done
```

* 背景颜色

```
for i in {40..47};do echo -e "\033[47;${i}m hello world! \033[0m";done

```
