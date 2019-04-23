# linux 防火墙简单整理

ubuntu: 一般两种方式：ufw与iptables.

iptables    防火墙规则的数据库，是系统实际使用的
ufw         简易化操作iptables的命令行工具
gufw        图形化的ufw工具（这个没测试）

centos: 一般用iptables和firewalld(centos7以后)

-----

## ufw管理方式

ufw默认是关闭的，一些常用操作命令如下：

```bash
ufw status; #状态
ufw enable; #启用
ufw default deny; #先拒绝所有，然后需要哪个开放哪个
ufw allow 80/tcp; #允许80端口
ufw delete allow 80/tcp; #删除允许80端口
ufw disable; #禁用
ufw reload #重载

ufw allow from 123.44.55.66 #允许此IP访问所有的本机端口
ufw deny from 123.44.55.66 #禁止此IP访问所有的本机端口
ufw deny smtp #禁止外部访问smtp服务
ufw delete allow smtp #删除上面建立的某条规则
```

一般查看状态的时候，会有两条记录，IPv4与v6，编辑/etc/default/ufw，可以关闭IPv6

linux 2.4内核以后提供了一个非常优秀的防火墙工具：netfilter/iptables,他免费且功能强大，可以对流入、流出的信息进行细化控制，它可以 实现防火墙、NAT（网络地址翻译）和数据包的分割等功能。netfilter工作在内核内部，而iptables则是让用户定义规则集的表结构。

但是iptables的规则稍微有些“复杂”，因此ubuntu提供了ufw这个设定工具，以简化iptables的某些设定，其后台仍然是 iptables。ufw 即uncomplicated firewall的简称，一些复杂的设定还是要去iptables。


## iptables管理方式
//禁用firewalld，如果是centos7之前的版本请忽略此操作！

`systemctl stop firewalld` //停止服务

`systemctl mask firewalld` //mask是屏蔽的意思，屏蔽服务启动

`yum install iptables-services` //安装iptables，如果有请忽略

相关启动命令有下面这些，选择执行：

```bash
# 启用与禁用服务
chkconfig iptables on/off/list
systemctl start iptables //开启服务
systemctl enable iptables //启用iptables
# 启动停止服务等
/etc/init.d/iptables [status|start|restart|stop|reload]
service iptables stop/start/status/restart/reload
systemctl [status|start|restart|stop|reload] iptables
# 其他一些操作
systemctl is-enabled iptables #查看服务是否开机启动
systemctl list-unit-files|grep enabled #查看已启动的服务列表
systemctl –failed #查看启动失败的服务列表
```

说明：启用服务就是在当前“runlevel”的配置文件/etc/systemd/system/multi-user.target.wants/里，建立/usr/lib/systemd/system里面对应服务配置文件的软链接；禁用服务就是删除此软链接，添加服务就是添加软连接。如下：
```bash
systemctl unmask firewalld #显示服务，等价于
cd /etc/systemd/system/multi-user.target.wants/ && ln -s firewalld.service /lib/systemd/system/firewalld.service

#屏蔽服务（让它不能启动）下面三个等价
systemctl mask firewalld
cd /lib/systemd/system/ && ln -s /dev/null /lib/systemd/system/firewalld.service 
rm /lib/systemd/system/firewalld.service #删除
```

iptables命令如下：
iptables -t tablename -[AIDLFPR] [PREROUTING|INPUT|FORWARD|OUTPUT|POSTROUTING] [规则，匹配条件] -j [DROP|ACCEPT|REJECT]

-t可以省略，默认是filter表，这里iptables共有5张表，filter,nat,mangle,raw,securiry

IAsj各选项含义：

-S  list rules
-I  insert
-A  append
-D  delete
-L  list
-F  flush 清空
-P  default 默认规则
-R  replace 替换
-n  domain2ip
-p  protocol 协议
-s  源地址
-d  目标地址
-m  模块
-j  处理方式

禁止IP
iptables -A INPUT -s 123.44.55.66 -j DROP
iptables -I INPUT -p tcp --dport 80 -j ACCEPT

通过lastb发现有几个ip总是试图登录，可以使用防火墙把它屏蔽掉。

查看恶意ip试图登录次数：
lastb | awk ‘{ print $3}’ | sort | uniq -c | sort -n
命令如下：
iptables -A INPUT -s 1.2.3.4 -j DROP
但是这样一个个写太慢了，可以用fail2ban禁用，过程见底部



## firewall管理方式
### 安装firewalld
yum install firewalld firewall-config //不存在时需要安装
firewalld默认配置文件有两个：/usr/lib/firewalld/ （系统配置，尽量不要修改）和 /etc/firewalld/ （用户配置地址）

### 运行、停止、禁用firewalld
启动：# systemctl start  firewalld
查看状态：# systemctl status firewalld 或者 firewall-cmd --state
停止：# systemctl disable firewalld
禁用：# systemctl stop firewalld

### 查看firewall服务状态
systemctl status firewalld

### 查看firewall的状态
firewall-cmd --state

### 查看防火墙规则
firewall-cmd --list-all

### 查询、开放、关闭端口

##### 查询端口是否开放
firewall-cmd --query-port=8080/tcp
##### 开放80端口
firewall-cmd --permanent --add-port=80/tcp
##### 开放一个范围的端口
firewall-cmd --permanent --zone=public --add-port=100-500/tcp
firewall-cmd --permanent --zone=public --add-port=100-500/udp
##### 移除端口
firewall-cmd --permanent --remove-port=8080/tcp

### 禁止IP
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address="123.44.55.66" drop'

### 重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload

### 参数解释
1. firwall-cmd：是Linux提供的操作firewall的一个工具；
2. --permanent：表示设置为持久；
3. --add-port：标识添加的端口；
4. zone概念：
> 硬件防火墙默认一般有三个区，firewalld引入这一概念系统默认存在以下区域（根据文档自己理解，如果有误请指正）：
drop：默认丢弃所有包
block：拒绝所有外部连接，允许内部发起的连接
public：指定外部连接可以进入
external：这个不太明白，功能上和上面相同，允许指定的外部连接
dmz：和硬件防火墙一样，受限制的公共连接可以进入
work：工作区，概念和workgoup一样，也是指定的外部连接允许
home：类似家庭组
internal：信任所有连接
对防火墙不算太熟悉，还没想明白public、external、dmz、work、home从功能上都需要自定义允许连接，具体使用上的区别还需高人指点




## fail2ban监控secure日志文件，禁止IP过程简单记录

### 环境:centos 7.3 + iptables
### download: https://github.com/fail2ban/fail2ban/archive/0.9.4.tar.gz
### 过程记录

```bash
tar -zxvf fail2ban-0.9.4.tar.gz
mv fail2ban-0.9.4 /usr/local/fail2ban
cd /usr/local/fail2ban
python setup.py install

cd /etc/fail2ban
cp jail.conf jail.local
vim jail.d/sshd.local
### 内容如下
[sshd]
enabled = true
port = ssh
logpath = /var/log/secure
maxretry = 3
bantime = 600
findtime = 3600
### ==========

grep chkconfig ./* -R --color
cp /usr/local/fail2ban/files/redhat-initd /etc/init.d/fail2ban
/etc/init.d/fail2ban start
fail2ban-client status sshd
iptables -L -n
```
