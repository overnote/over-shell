.. contents::
   :depth: 3
..

十、shell杂项
=============

一、输入输出
------------

-  输入输出 文件描述符 \| 描述 \| 映射关系 —|—|— 0 \| 标准输入 \|
   /dev/stdin -> /proc/self/fd/0 1 \| 标准输出 \| /dev/stdout ->
   /proc/self/fd/1 2 \| 标准错误 \| /dev/stderr -> /proc/self/fd/2

-  重定向

::

   > ：符号左边输出作为右边的输入
   >>: 符号左边输出追加右边的输入
   < : 符号右边输出作为左边输入（标准输入）
   <<: 符号右边输出追加左边输入
   & : 重定向绑定符号

eg:

::

   cat > file.txt <<EOF
   context
   EOF

   ls aa >/dev/null 2>&1

二、命令
--------

-  tac:倒序打印文件
-  rev反向打印每一行
-  cut:字符切割，常用选项-d 分割，-f输出第几段
-  tr:替换或删除字符
-  seq:打印序列化数字
-  sort:排序 -t:制定分隔符 -k:制定field -r:倒序排序 -u:去重行
-  uniq:去重 -c 打印出现次数、-u ：打印不重复的行
-  date:显示系统时间

::

   时间加减：
   显示前 30 秒：date -d '-30 second' +'%F %T'
   显示前一分钟：date -d '-1 minute' +'%F %T'
   显示前一个时间：date -d '-1 hour' +'%F %T'
   显示前一个天：date -d '-1 day' +'%F %T'
   显示上一周：date -d '-1 week' +'%F %T'
   显示上一个月日期：date -d '-1 month' +%F
   显示上一年日期：date -d '-1 year' +%F
   或
   显示前一天日期：date -d yesterday +%F
   显示后一天日期：date -d tomorrow +%F

-  screen

   -  screen -ls
   -  screen -r
   -  screen -wipe

脚本编写注意事项

::

   1）开头加解释器：#!/bin/bash

   2）语法缩进，使用四个空格；多加注释说明。

   3）命名建议规则：变量名大写、局部变量小写，函数名小写，名字体现出实际作用。

   4）默认变量是全局的，在函数中变量 local 指定为局部变量，避免污染其他作用域。

   5）有两个命令能帮助我调试脚本：set -e 遇到执行非 0 时退出脚本，set -x 打印执行过程。

   6）写脚本一定先测试再到生产上。

三、实战
--------

更多示例可参考：\ `shell
脚本示例 <https://github.com/redhatxl/scripts>`__

3.1 文件扫描校验
~~~~~~~~~~~~~~~~

::

   #!/bin/bash
   #func:scan file
   #md5sum -c $SCAN_FILE


   SCAN_DIR=`echo $PATH |sed 's/:/ /g'`
   SCAN_CMD=`which md5sum`
   SCAN_FILE_FALL="/tmp/scan_$(date +%F%H%m)_fall.txt"
   SCAN_FILE_BIN="/tmp/scan_$(date +%F%H%m)_bin.txt"

   scan_fall_disk() {
           echo "正在全盘扫描，请稍等！文件路径:$SCAN_FILE_FALL"
           find / -type f -exec $SCAN_CMD \{\} \;>> $SCAN_FILE_FALL 2>/dev/null
   }

   scan_bin() {
       echo "正在扫描PATH可执行文件，请稍等，文件路径：$SCAN_FILE_BIN"
       for file in $SCAN_DIR
       do
           find $filae -type f -exec $SCAN_CMD \{\} \;>> $SCAN_FILE_BIN 2>/dev/null
       done
   }

   main() {
       [ $# -lt 1 ] && echo "请使用参数，1表示全盘扫描，2表示二进制可执行文件扫描"
       read number
       case $number in 
       1) 
           scan_fall_disk;;
       2)
           scan_bin;;
       *)
           echo "参数错误，1，表示全盘扫描，2表示二进制文件扫描" 
       esac
   }


   main

3.2 自定义垃圾回收
~~~~~~~~~~~~~~~~~~

::

   #!/bin/bash
   # function:自定义rm命令，每天晚上定时清理

   CMD_SCRIPTS=$HOME/.rm_scripts.sh
   TRASH_DIR=$HOME/.TRASH_DIR
   CRON_FILE=/var/spool/cron/root
   BASHRC=$HOME/.bashrc

   [ ! -d ${TRASH_DIR} ] && mkdir -p ${TRASH_DIR}
   cat > $CMD_SCRIPTS <<EOF
   PARA_CNT=\$#
   TRASH_DIR=$TRASH_DIR

   for i in \$*; do
        DATE=\$(date +%F%T)
        fileName=\$(basename \$i)
        mv \$i \$TRASH_DIR/\$fileName.\$DATE
   done
   EOF

   sed -i "s@$(grep 'alias rm=' $BASHRC)@alias rm='bash ${CMD_SCRIPTS}'@g" $BASHRC
   source $HOME/.bashrc

   echo "0 0 * * * rm -rf $TRASH_DIR/*" >> $CRON_FILE
   echo "删除目录:$TRASH_DIR"
   echo "删除脚本:$CMD_SCRIPTS"
   echo "请执行:source $BASHRC 来加载文件或退出当前shell重新登录"

3.3 Linux系统检测
~~~~~~~~~~~~~~~~~

::

   #!/bin/bash
   # auth:kaliarch
   # func:sys info check
   # version:v1.0
   # sys:centos6.x/7.x

   [ $(id -u) -gt 0 ] && echo "请用root用户执行此脚本！" && exit 1
   sysversion=$(rpm -q centos-release|cut -d- -f3)
   line="-------------------------------------------------"


   [ -d logs ] || mkdir logs

   sys_check_file="logs/$(ip a show dev eth0|grep -w inet|awk '{print $2}'|awk -F '/' '{print $1}')-`date +%Y%m%d`.txt"

   # 获取系统cpu信息
   function get_cpu_info() {
       Physical_CPUs=$(grep "physical id" /proc/cpuinfo| sort | uniq | wc -l)
       Virt_CPUs=$(grep "processor" /proc/cpuinfo | wc -l)
       CPU_Kernels=$(grep "cores" /proc/cpuinfo|uniq| awk -F ': ' '{print $2}')
       CPU_Type=$(grep "model name" /proc/cpuinfo | awk -F ': ' '{print $2}' | sort | uniq)
       CPU_Arch=$(uname -m)
   cat <<EOF | column -t 
   CPU信息:

   物理CPU个数: $Physical_CPUs
   逻辑CPU个数: $Virt_CPUs
   每CPU核心数: $CPU_Kernels
   CPU型号: $CPU_Type
   CPU架构: $CPU_Arch
   EOF
   }

   # 获取系统内存信息
   function get_mem_info() {
       check_mem=$(free -m)
       MemTotal=$(grep MemTotal /proc/meminfo| awk '{print $2}')  #KB
       MemFree=$(grep MemFree /proc/meminfo| awk '{print $2}')    #KB
       let MemUsed=MemTotal-MemFree
       MemPercent=$(awk "BEGIN {if($MemTotal==0){printf 100}else{printf \"%.2f\",$MemUsed*100/$MemTotal}}")
       report_MemTotal="$((MemTotal/1024))""MB"        #内存总容量(MB)
       report_MemFree="$((MemFree/1024))""MB"          #内存剩余(MB)
       report_MemUsedPercent="$(awk "BEGIN {if($MemTotal==0){printf 100}else{printf \"%.2f\",$MemUsed*100/$MemTotal}}")""%"   #内存使用率%

   cat <<EOF
   内存信息：

   ${check_mem}
   EOF
   }

   # 获取系统网络信息
   function get_net_info() {
       pri_ipadd=$(ip a show dev eth0|grep -w inet|awk '{print $2}'|awk -F '/' '{print $1}')
       pub_ipadd=$(curl ifconfig.me -s)
       gateway=$(ip route | grep default | awk '{print $3}')
       mac_info=$(ip link| egrep -v "lo"|grep link|awk '{print $2}')
       dns_config=$(egrep -v "^$|^#" /etc/resolv.conf)
       route_info=$(route -n)
   cat <<EOF | column -t 
   IP信息:

   系统公网地址: ${pub_ipadd}
   系统私网地址: ${pri_ipadd}
   网关地址: ${gateway}
   MAC地址: ${mac_info}

   路由信息:
   ${route_info}

   DNS 信息:
   ${dns_config}
   EOF
   }

   # 获取系统磁盘信息
   function get_disk_info() {
       disk_info=$(fdisk -l|grep "Disk /dev"|cut -d, -f1)
       disk_use=$(df -hTP|awk '$2!="tmpfs"{print}')
       disk_inode=$(df -hiP|awk '$1!="tmpfs"{print}')

   cat <<EOF
   磁盘信息:

   ${disk_info}
   磁盘使用:

   ${disk_use}
   inode信息:

   ${disk_inode}
   EOF


   }

   # 获取系统信息
   function get_systatus_info() {
       sys_os=$(uname -o)
       sys_release=$(cat /etc/redhat-release)
       sys_kernel=$(uname -r)
       sys_hostname=$(hostname)
       sys_selinux=$(getenforce)
       sys_lang=$(echo $LANG)
       sys_lastreboot=$(who -b | awk '{print $3,$4}')
       sys_runtime=$(uptime |awk '{print  $3,$4}'|cut -d, -f1)
       sys_time=$(date)
       sys_load=$(uptime |cut -d: -f5)

   cat <<EOF | column -t 
   系统信息:

   系统: ${sys_os}
   发行版本:   ${sys_release}
   系统内核:   ${sys_kernel}
   主机名:    ${sys_hostname}
   selinux状态:  ${sys_selinux}
   系统语言:   ${sys_lang}
   系统当前时间: ${sys_time}
   系统最后重启时间:   ${sys_lastreboot}
   系统运行时间: ${sys_runtime}
   系统负载:   ${sys_load}
   EOF
   }

   # 获取服务信息
   function get_service_info() {
       port_listen=$(netstat -lntup|grep -v "Active Internet")
       kernel_config=$(sysctl -p 2>/dev/null)
       if [ ${sysversion} -gt 6 ];then
           service_config=$(systemctl list-unit-files --type=service --state=enabled|grep "enabled")
           run_service=$(systemctl list-units --type=service --state=running |grep ".service")
       else
           service_config=$(/sbin/chkconfig | grep -E ":on|:启用" |column -t)
           run_service=$(/sbin/service --status-all|grep -E "running")
       fi
   cat <<EOF
   服务启动配置:

   ${service_config}
   ${line}
   运行的服务:

   ${run_service}
   ${line}
   监听端口:

   ${port_listen}
   ${line}
   内核参考配置:

   ${kernel_config}
   EOF
   }


   function get_sys_user() {
       login_user=$(awk -F: '{if ($NF=="/bin/bash") print $0}' /etc/passwd)
       ssh_config=$(egrep -v "^#|^$" /etc/ssh/sshd_config)
       sudo_config=$(egrep -v "^#|^$" /etc/sudoers |grep -v "^Defaults")
       host_config=$(egrep -v "^#|^$" /etc/hosts)
       crond_config=$(for cronuser in /var/spool/cron/* ;do ls ${cronuser} 2>/dev/null|cut -d/ -f5;egrep -v "^$|^#" ${cronuser} 2>/dev/null;echo "";done)
   cat <<EOF
   系统登录用户:

   ${login_user}
   ${line}
   ssh 配置信息:

   ${ssh_config}
   ${line}
   sudo 配置用户:

   ${sudo_config}
   ${line}
   定时任务配置:

   ${crond_config}
   ${line}
   hosts 信息:

   ${host_config}
   EOF
   }


   function process_top_info() {

       top_title=$(top -b n1|head -7|tail -1)
       cpu_top10=$(top b -n1 | head -17 | tail -10)
       mem_top10=$(top -b n1|head -17|tail -10|sort -k10 -r)

   cat <<EOF
   CPU占用top10:

   ${top_title}
   ${cpu_top10}

   内存占用top10:

   ${top_title}
   ${mem_top10}
   EOF
   }


   function sys_check() {
       get_cpu_info
       echo ${line}
       get_mem_info
       echo ${line}
       get_net_info
       echo ${line}
       get_disk_info
       echo ${line}
       get_systatus_info
       echo ${line}
       get_service_info
       echo ${line}
       get_sys_user
       echo ${line}
       process_top_info
   }


   sys_check > ${sys_check_file}
