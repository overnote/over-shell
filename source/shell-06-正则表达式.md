
# 六、正则表达式

## 一、基本正则表达式

#### 1.1 字符匹配

* .:匹配任意单个字符
* []:匹配指定范围内的任意单个字符
* \[^]:匹配指定范围外的任意单个字符
* [:digit:]匹配元字符

```
posix字符
[:alnum:] 字母数字[a-z A-Z 0-9]
[:alpha:]字母[a-z A-Z]
[:blank:]空格或制表键
[:cntrl:] 任何控制字符
[:digit:] 数字 [0-9]
[:graph:] 任何可视字符（无空格）
[:lower:] 小写 [a-z]
[:print:] 非控制字符
[:punct:] 标点字符
[:space:] 空格
[:upper:] 大写 [A-Z]
[:xdigit:] 十六进制数字 [0-9 a-f A-F]
```
```
特殊字符
\w 匹配任意数字和字母，等效[a-zA-Z0-9_]
\W 和\w相反，等效[^a-zA-Z0-9_]
\b 匹配字符串开始或结束，等效\<和\>
\s 匹配任意的空白字符
\S 匹配非空白字符
```


#### 1.2 次数匹配

用在制定的字符后面，表示制定前面的字符出现多少次
* \*:匹配前面的字符任意次（0次获无数次）
* \?:匹配前面的字符0次或1次
* \+:匹配前面的字符至少1次
* {m\,}:匹配前面的字符至少m次（默认工作在贪婪模式下，?取消贪婪模式）
* {m,n}:匹配前面的字符至少m次，至多n次
eg:

```
.*:匹配任意字符任意次数
```

#### 1.3 位置锚定

* ^:行首锚定，用于模式最左边
* $:行尾锚定,用于模式最右边
* \\<或\b:锚定词首，用于单词模式左侧
* \\>或\b:锚定词尾，用于单词模式右侧



eg:

```
^$:锚定空行
```

#### 1.4 分组引用

分组
* \(\):将一个或多个字符当成一个整体来进行后续处理

引用
* 1：从左侧起，引用第一个左括号以及与之匹配右括号之间的模式所匹配到的字符，后向引用


exercises:

```
1.显示/etc/init.d/functions文件中以大小s开头的行(使用两种方式)
grep '^[Pp]' /etc/init.d/functions
grep -i "^p" /etc/init.d/functions

2.显示/etc/passwd文件中不以/bin/bash结尾的行
grep -v "/bin/bash$" /etc/passwd

3.显示/etc/passwd文件中ID号最大用户的用户名
sort -t: -k3 -n /etc/passwd |tail -1 |cut -d: -f1

4.如果root用户存在,显示其默认的shell程序
id root && grep '^\<root\>' /etc/passwd |awk -F: '{print $NF}'

5.找出/etc/passwd中的两位或三位数
grep -o -E "[0-9]{2,3}" /etc/passwd
grep -o "[0-9]\{2,3\}" /etc/passwd

6.显示/etc/rc.d/rc.sysinit文件中,至少以一个空白字符开头的且后面存非空白字符的行:
grep '^[[:space:]]\+[^[:space:]]' /etc/rc.d/rc.sysinit


7.找出"netstat -tan"命令的结果以"LISTEN"后跟0,1或多个空白字符结尾的行
netstat -tan|grep 'LISTEN[[:space:]]*$'

8.如果root用户登录了系统,就显示root用户在线,否则说明未登录
w |grep '^\<root\>'>/dev/null && echo "root在线"|| echo "root未登录"

9.找出/etc/rc.d/init.d/functions文件中某单词后面跟一对小括号的行
grep '[[:alpha:]]*()' /etc/rc.d/init.d/functions

10.使用echo输出一个路径,使用egrep取出基名
 echo /tmp/tmp1/vmstat.8.gz |grep -E  -o '[^/]+/?$'|cut -d/ -f1
echo /tmp/tmp1/vmstat.8.gz |awk -F'/' '{print $NF}'

11.匹配PPID开头，行中又再次出现PPID的内容。/etc/init.d/functions
grep -E "(PPID).*\1" /etc/init.d/functions

12.利用awk找出/etc/ssh/sshd_config内出过空行与以#开头的行
awk '!/^#/ && !/^$/{print}' /etc/ssh/sshd_config
grep -v -E '^#|^$' /etc/ssh/sshd_config


```