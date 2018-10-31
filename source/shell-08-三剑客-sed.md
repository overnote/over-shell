# 八、三剑客之sed

## 一、概念：

sed是一种流编辑的文本处理工具，
* 工作模式：将当前处理的行存储在临时缓冲区（模式空间），对缓冲区中的内容利用制定的动作进行处理，完成后输出到屏幕，接着反复重复执行此操作完成整改文件的处理。
* 
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

```
1、删除/etc/grub.conf文件中行首的空白符；
sed -r 's/^[[:space:]]+//g' /etc/grub.conf
2、替换/etc/inittab文件中"id:3:initdefault:"一行中的数字为5；
's/id:[0-9]/id:5/g' /etc/inittab
3、删除/etc/inittab文件中注释行：
sed '/^#/d' /etc/inittab
4、取消/etc/inittab文件中开头的#号;
sed 's/^#//g' /etc/inittab 
5、打印文件/etc/services匹配blp5开头的行
sed -n '/^blp5/p' /etc/services
6、打印2-5行
sed -n '2,5p' /etc/services
7、打印奇数行
seq 10 |sed -n '1~2p'
8、打印最后一行
sed '$p' /etc/services
```