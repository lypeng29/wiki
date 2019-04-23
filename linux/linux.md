# linux 常用命令

find命令
find /etc -name init
find /etc -name init???
find /etc -name init*
find /etc -iname init*
find /etc -user lypeng
find /etc -size +204800
find /etc -size +204800 -a -size -409600
find /etc -name init* -a -type f
-a -o
find /etc -mmin -30 	#30分钟内修改内容的文件
find /etc -size +204800 -exec ls -l {} \;	#列出大小超过100M的文件 100Mb=102400Kb=204800 block #1Block = 0.5Kb = 512byte
find /etc -size +204800 -ok rm {} \;
ls -i
find /etc -inum 20315 -ok rm {} \;

df、du、dump2fs命令
df -h	#统计文件+系统+命令大小
du -sh /etc/ #统计文件总大小

dumpe2fs	#查看磁盘信息 block innode等

查看端口监听信息
netstat -tlunp
-t tcp
-l listening
-u udp
-n numver
-p program

vim快捷键

map快捷键设置
ab关键词替换

:map ^_ I# <ESC> 定义Ctrl+/ 给行首添加#注释
:1,4s/^/#/g 添加#注释
:1,4s/^#//g 取消注释
:ab mail 893371810@qq.com

进程内存内核查看管理
ps top free kill killall uname

ps aux
a------all
u------user user-oriented format
x------processes without controlling ttys

pstree -p
pstree -u

top
M-------以内存排序 mem
P-------以CPU排序
q-------退出

kill -l(小写L) 进程的信号，例如9

kill -1 重启进程
kill -9 强制结束进程
kill -15 正常结束进程，默认15

killall -9 httpd  终止所有httpd进程

pkill -9 -t pts/0 按 终端号清理已经登录的用户

tar -zcf etc.tar.gz /etc & 把进程放在后台运行
&
ctrl+z
jobs 查看后台有哪些进程
fg %1 把第一个进程恢复到前台执行
bg %2 把第一个进程恢复到前台执行

vmstat 检测系统状态包括 内存 cpu
dmesg | grep CPU

cat /proc/cpuinfo 查看cpu信息

内存=可用内存+缓存（利于读取）+缓冲（利于写入）

uname 查看系统内核
uname -a 
uname -r

file /bin/ls 查看文件信息，会显示32bit or 64bit

lsb_release -a 查看操作系统版本

硬盘 内存 swap 缓存 缓冲关系

硬盘---》读取数据---》写入缓存---》不够用--》swap

数据---》写入缓冲---》积攒到一定量一次写入硬盘

缓存+缓冲==》内存





计划任务
crontab -e #编辑
crontab -l #查看
crontab -r #删除

*/1 * * * * /bin/bash echo `date`>>/tmp/test.txt
*/1 * * * * /test.sh

五个星表示如下：分钟 小时 天 月 周

分钟---》每小时的第几分钟
小时---》每天的几点
天--》每月的第几天
月--》每年的第几月
周--》每周几（0-7）07都表示周日

30 05 * * * 每天05点30分
0 05 1,10 * * 每月1号，10号的05点
0 05 * * 2 每周二05点


日志查看
lastb		#登录失败日志，读取的是btmp文件
last 		#
lastlog		#上次登录日志




