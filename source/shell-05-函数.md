# 五、函数

## 一、概念

linux shell 可以用户定义函数，然后在shell脚本中可以随便调用,以此来重复调用公共函数，减少代码量。

## 二、格式

```
[ function ] funname()
{
    action;
    [return int;]
}
```

说明：

* function 关键字可写，也可不写。
* 参数返回，可以显示加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255）,hell 函数返回值只能是整形数值，一般是用来表示函数执行成功与否的，0表示成功，其他值表示失败。因而用函数返回值来返回函数执行结果是不合适的。如果要硬生生地return某个计算结果，比如一个字符串，往往会得到错误提示：“numeric
 argument required”。
如果一定要让函数返回一个或多个值，可以定义全局变量，函数将计算结果赋给全局变量，然后脚本中其他地方通过访问全局变量，就可以获得那个函数“返回”的一个或多个执行结果了。

```
#!/bin/bash
function output_data() {
	DATA=$((1+1))
	return $DATA
}
output_data
echo $?
```

```
#!/bin/bash
# function:add number
function add_num() {
	echo "请输入第一个数："
	read number01
	echo "请输入第二个数字"
	read number02
	if [[ "$number01" =~ ^[0-9]+$ ]] && [[ "$number02" =~ ^[0-9]+$ ]];then
		sum=$(($number01+$number02))
		echo "$number01 + $number02 = $sum"
	else
		echo "input must be number"
	fi
}
add_num
```

## 三、函数参数

将函数写成无状态的，将数据当做参数进行传入

```
#!/bin/bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
    echo "作为一个字符串输出所有参数 $@ !"

}
funWithParam `seq 1 20`

```

```
${1..n} 指定第n个输入的变量名称
$0	    脚本自身名字
	
$?	    返回上一条命令是否执行成功，0 为执行成功，非 0 则为执行失败
	    
$#	    位置参数总数
	
$*	    所有的位置参数被看做一个字符串
	
$@	    每个位置参数被看做独立的字符串
	
$$	    当前进程 PID
	
$!	    上一条运行后台进程的 PID
```

eg:函数炸弹

```
:(){ :|:& };:
```

:|: 表示每次调用函数":"的时候就会生成两份拷贝。

& 放到后台

递归调用自身，直至系统崩溃
