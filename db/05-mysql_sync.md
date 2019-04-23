# db 05-MySQL主从同步

systemctl restart network //这个好使
//systemctl restart NetworkManager //这个不起作用


vim /etc/NetworkManager/NetworkManager.conf
[main]
plugins=ifcfg-rh
dns=none

vim /etc/resolv.conf
nameserver=180.76.76.76
nameserver=114.114.114.114

route add default  gw 192.168.1.1

2.挂载共享文件夹
mount /dev/sr0 /media/cdrom
mount /dev/sr1 /media/system
mount -t vboxsf 共享文件夹名 /media/downloads

yum -y install kernel-devel


### 版本与名称信息

| 名称 |系统|数据库版本|IP地址|server-id|
|---|---|---|---|---|
| 物理机（主库，master）| Ubuntu16.04 |  mysql_5.7.22 | 192.168.1.129| 1 |
| 虚拟机（从库，slave） | Centos7.4  |   mysql_5.6.40 | 192.168.1.137| 2 |


### 主库操作
#### 配置(设置server-id,开启binlog,设置同步数据库)
```
server-id=1
log-bin=mysql-bin
binglog-format=mixed
binlog-do-db=mydb
```

#### 创建用户与授权
`create user lypeng@192.168.1.137 identified 'lypeng123'`
`grant ALL privileges on mydb.* to lypeng@192.168.1.137`

或者将上面两条合并，创建用户同时授予权限

`grant all privileges on mydb.* to lypeng@192.168.1.137 identified 'lypeng123';`

#### 刷新权限
`flush privileges;`

#### 重置master，binlog信息
`reset master`
作用：刚开始你可以有很多binlog,例如mysql-bin.000001，mysql-bin.000002，000003,000004,000005等等，reset之后，将重新从000001开始记录，删除其他的～

#### 查看主库binlog状态
`show master status;`

### 从库操作
#### 安装MySQL
略过，参见：https://www.cnblogs.com/panzhaohui/p/7169905.html?_blank

#### 配置(设置server-id)
```
server-id=2
read-only = 1
```
不用开启binlog,开了也无所谓，不影响什么，同步时会生成，relaylog，互相不冲突～


#### 设置master信息
change master to master_host='192.168.1.129',master_user='lypeng', master_password='lypeng123',master_log_file='mysql-bin.000002',master_log_pos=120;

#### 启动同步
start slave;

### 继续主库
按上面的操作完毕后，在主库建立数据库mydb,然后建表，增删改查，

### 回到从库
查看数据是否已经同步过来了
```
show databases;
use mydb;
select * from user;
```
如果没有，可以查看状态，然后根据错误提示去调整
`show slave status\G;`

如果要停止同步，执行：`stop slave;`

或者重置slave，清除从库角色，即取消从master的同步信息：`reset slave all;`


