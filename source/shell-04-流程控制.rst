.. contents::
   :depth: 3
..

四、流程控制
============

一、if语句
----------

1.1 单分支
~~~~~~~~~~

::

   if condition
   then
       command1 
       command2
       ...
       commandN 
   fi

eg:

::

   if [ `ps -ef |grep /usr/sbin/sshd|grep -v grep|wc -l` -eq 1 ];then echo "sshd server exist";fi

1.2 双分支
~~~~~~~~~~

::

   if condition
   then
       command1 
       command2
       ...
       commandN
   else
       command
   fi

eg:

::

   if [ `ps -ef |grep /usr/sbin/sshd|grep -v grep|wc -l` -eq 0 ];then echo "sshd server exist";else echo "sshd server not exist";fi

1.3 多分支
~~~~~~~~~~

::

   if condition1
   then
       command1
   elif condition2 
   then 
       command2
   else
       commandN
   fi

eg:

::

   #! /bin/bash

   cmd=`rpm -q centos-release|cut -d- -f3`

   if [ $cmd -eq 6 ];then
       echo "sysversion is $cmd"
   elif [ $cmd -eq 7 ];then
       echo "sysversion is $cmd"
   else
       echo "sysversion is `rpm -q centos-release`"
   fi

二、for循环
-----------

::

   for var in item1 item2 ... itemN
   do
       command1
       command2
       ...
       commandN
   done

eg1:

::

   for i in /*; 
   do
       echo -e "   \c";
       find $i |wc -l|sort -nr;
   done

eg2:

::

   #!/bin/bash
   for i in {1..3};
   do
       echo $i
   done

eg3:

::

   #!/bin/bash
   for i in "$@"; {    # $@是将位置参数作为单个来处理
   echo $i
   }

默认 for 循环的取值列表是以空白符分隔，也就是第一章讲系统变量里的$IFS:

::

   #!/bin/bash
   OLD_IFS=$IFS
   IFS=":"
   for i in $(head -1 /etc/passwd); do
   echo $i
   done

::

   #!/bin/bash

   for ip in 192.168.1.{1..254}; do

       if ping -c 1 $ip >/dev/null; then

           echo "$ip OK."

       else

           echo "$ip NO!"

       fi

   done

读取文件,判断url可用性

::

   #!/bin/bash
   #function:check url
   filename=urllist.txt
   for url in $(cat $filename)
   do
   status=`curl -I $url -s|awk '/HTTP/{print $2}'`
   if [ $status == "200" ];then
       echo "Url:$url is ok!status is $status"
   else 
       echo "Url:$url is error!status is $status"
   fi
   done

三、while语句
-------------

格式：

::

   while 条件表达式:do
       command
   done

eg1:

::

   #!/bin/bash
   N=0
   while [ $N -lt 5 ]; do
   let N++
   echo $N
   done

条件表达式为 true，将会产生死循环,利用此可以将脚本一直放在后台进行执行
eg2:

::

   #!/bin/bash
   IP=10.75.128.8
   dir="/DATA/oracle/netdir/"
   if [ ! -d ${dir} ];then
       mkdir -p ${dir}
   fi
   echo 1 > ${dir}ping.lock
   while true
   do
       Time=`date +%F`
       TIME="${Time} 23:59"
       if [ "${data}" == "${TIME}" ];then
           mkdir ${dir}${Time} && mv ${dir}ping2.log ${dir}${Time}-ping2.log
           mv ${dir}${Time}-ping2.log ${dir}${Time}
       fi
       find ${dir} -mtime +7 -name "*-ping2.log" -exec rm -rf {} \;
       find ${dir} -mtime +7 -type d -exec rm -rf {} \;

       data=`date +%F' '%H:%M`
       data1=`date +%F' '%H:%M:%S`
       echo "------------${data1}---------------">>${dir}ping2.log
       ping -c 10 ${IP} >>${dir}ping2.log
       if [ $? -eq 1 ];then
           STAT=`cat ${dir}ping.lock`
           if [ ${STAT} -eq 1 ];then
               /usr/bin/python /DATA/oracle/netdir/GFweixin.py xuel GLP-VPN "GLP from PDC(172.16.6.1
   50) ping 金融云(10.75.128.8)中断，请检查深信服VPN！ \n TIME:${data1}"            echo 0 > ${dir}ping.lock
           else
               continue
           fi
       else
           STAT=`cat ${dir}ping.lock`
           if [ ${STAT} -eq 0 ];then
               /usr/bin/python /DATA/oracle/netdir/GFweixin.py xuel GLP-VPN "GLP from PDC(172.16.6.1
   50) ping 金融云(10.75.128.8)恢复！ \n TIME:${data1}"          echo 1 > ${dir}ping.lock
           else
               continue
           fi
       fi


   done

文件处理

eg3:

::

   #!/bin/bash
   #function:check url
   filename=urllist.txt
   cat $filename | while read url;do
   status=`curl -I $url -s|awk '/HTTP/{print $2}'`
   if [ $status == "200" ];then
           echo "Url:$url is ok!status is $status"
   else
           echo "Url:$url is error!status is $status"
   fi
   done

或

::

   #!/bin/bash
   #function:check url
   filename=urllist.txt
   while read url;
   do
   status=`curl -I $url -s|awk '/HTTP/{print $2}'`
   if [ $status == "200" ];then
           echo "Url:$url is ok!status is ${status}"
   else
           echo "Url:$url is error!status is ${status}"
   fi
   done <$filename

四、break 和 continue 语句
--------------------------

break跳出循环

::

   #!/bin/bash

   N=0
   while true; do
       let N++
       if [ $N -eq 5 ]; then
       break
       fi
       echo $N
   done

continue

::

   #!/bin/bash
   N=0
   while [ $N -lt 5 ]; do
       let N++
       if [ $N -eq 3 ]; then
           continue
       fi
       echo $N
   done

五、case语句
------------

语句

::

   case 模式名    in
       模式 1)
           命令
           ;;
       模式 2)
           命令
           ;;
       *)
           不符合以上模式执行的命令
   esac

eg

::

   #!/bin/bash
   case $1 in
       start)
           echo "start."   
           ;;
       stop)
           echo "stop."
           ;;
       restart)
           echo "restart."
           ;;
       *)
           echo "Usage: $0 {start|stop|restart}"
   esac
