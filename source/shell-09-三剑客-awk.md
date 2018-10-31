
# 九、三剑客之awk

## 一、概念

AWK：报告生成器，格式化文本输出工具

```
awk [options] 'script' file1,file2...
awk [options] 'PATTERN {action}' file1,file2
```
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

> 1. print 使用格式

```
print item1,item2...
```

**要点:**
* 1.各字段之间逗号隔开，输出时以空白字符分割；

* 2.输出的字段可以为字符串或数值，当前记录的字段（如$1）、变量或awk的表达式；数值先回转换成字符串然后输出

* 3.print命令后面的item可以省略，此时其功能相当于print $0,如果想输出空白，可以使用`print ""`

eg:

```
awk -F: '{print $1,$NF}' /etc/passwd|column -t
```
```
tail -1 /etc/passwd|awk -F: 'BEGIN{OFS="#"}{print “hello”$1,$2}'
```


> 2.printf

printf命令的使用格式:

```
printf <format> item1,item2...
```

**要点：**
* 1.其与print命令最大区别,printf 需要指定format,format必须给出

* 2.format用于指定后面的每个item输出格式

* 3.printf 语句不会自动打印换行字符:\n


format格式的指示符都以%开头，后跟一个字符:

```
%c:显示ascall码
%d:%i:十进制整数
%e，%E：科学计数法
%f:浮点数
%s：字符串
%u：无符号整数
%%：显示%自身

修饰符：
#[.#]:第一个#控制显示的宽度：第二个#表示小数点后的精度：

%3.1f

-:左对齐
+：显示数组符号
```

eg：

```
awk -F: '{printf "Username:%-15s   ,Uid:%d\n",$1,$3}' /etc/passwd
```

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

```
* 统计/etc/fstab文件中每个单词出现的次数，并按从大到小排序
awk '{for(i=1;i<=NF;i++){words[$i]++}}END{for(key in words)print key,words[key]}' /etc/fstab|sort -k2 -nr

awk '{ips[$1]++}END{for(i in ips) print i,ips[i]}' access_nginx.log |column -t|sort -k2 -nr

* 统计/etc/fstab每个文件系统类型出现的次数
awk '!/^#/&&!/^$/{dev[$3]++}END{for(i in dev) print i,dev[i]}' /etc/fstab

* ping一个域名，输出ping此刻的时间
ping baidu.com|awk '{print $0" "strftime("%Y-%m-%d %H:%M:%S")}'

* 利用netstat监控服务是否正常监听
netstat -lntup|awk 'NR>2{if($4 ~/.*:22/) print $0"yes";exit 0}'

* 统计web服务器日志状态码
awk '$9~"[0-9]"{stat[$9]++}END{for(i in stat) print i,stat[i]}' access_log
```