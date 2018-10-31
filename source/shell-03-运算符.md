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

```
[root@monitor ~]# [ 1 -gt 1 ] && echo true || echo false
false
[root@monitor ~]# [ 1 -ne 1 ] && echo true || echo false
false
[root@monitor ~]# [ 1 -eq 1 ] && echo true || echo false
true
[root@monitor ~]# [ 1 -ne 1 ] && echo true || echo false
false
[root@monitor ~]# [ 1 -gt 1 ] && echo true || echo false
false
[root@monitor ~]# [ 2 -gt 1 ] && echo true || echo false
true
[root@monitor ~]# [ 2 -lt 1 ] && echo true || echo false
false
[root@monitor ~]# [ 2 -le 1 ] && echo true || echo false
false
```


#### 1.2 算术运算符

假定变量 a 为 10，变量 b 为 20：
注意：运算符两边有空格

运算符 | 说明 | 举例
---|---|---
+ |	加法 |	`expr $a + $b` 结果为 30。
- |	减法 |	`expr $a - $b` 结果为 -10。
* |	乘法 |	`expr $a \* $b` 结果为  200。
/ |	除法 |	`expr $b / $a` 结果为 2。
% |	取余 |	`expr $b % $a` 结果为 0。
= |	赋值 |	a=$b 将把变量 b 的值赋给 a。
== |	相等| 用于比较两个数字，相同则返回 true。	[ $a == $b ] 返回 false。
!=	| 不相等|用于比较两个数字，不相同则返回 true。	[ $a != $b ] 返回 true。

```
A=3
B=6
1、let 算术运算表达式
let C=$A+$B
2、$[算术运算表达式]
C=$[$A+$B]
3、$((算术运算表达式))
C=$(($A+$B))
4、expr 算术运算表达式，表达式中各操作数及运算符之间要有空格，而且要使用命令引用
C=`expr $A + $B`

```


#### 1.3 布尔运算符


运算符 |	说明 |	举例
---|---|---
!	| 非运算，表达式为 true 则返回 false，否则返回 true。|	[ ! false ] 返回 true。
-o |	或运算，有一个表达式为 true 则返回 true。 |	[ $a -lt 20 -o $b -gt 100 ] 返回 true。
-a	| 与运算，两个表达式都为 true 才返回 true。|	[ $a -lt 20 -a $b -gt 100 ] 返回 false。

#### 1.4 逻辑运算符


运算符	| 说明 |	举例
---|---|---
&&	| 逻辑的 AND |	[[ $a -lt 100 && $b -gt 100 ]] 返回 false
\|\|  | 逻辑的 OR	 |  [[ $a -lt 100 \|\| $b -gt 100 ]] 返回 true

#### 1.5 文件测试运算符

操作符	| 说明 |	举例
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

运算符 |	说明 |	举例
---|---|---
=	| 检测两个字符串是否相等，相等返回 true。 |	[ $a = $b ] 返回 false。
!= |	检测两个字符串是否相等，不相等返回 true。 |	[ $a != $b ] 返回 true。
-z	| 检测字符串长度是否为0，为0返回 true。	| [ -z $a ] 返回 false。
-n	| 检测字符串长度是否为0，不为0返回 true。| 	[ -n "$a" ] 返回 true。
str	| 检测字符串是否为空，不为空返回 true。| 	[ $a ] 返回 true。


