# db 04-MySQL读写分离

## 环境版本信息
|IP地址|说明|系统|MYSQL|
|--|--|--|--|
|192.168.1.129| 主库master|物理机-Ubuntu16.04|mysql_5.7.22|
|192.168.1.130| 从库slave|虚拟机-centos7_desktop|mysql-5.6.40|
|192.168.1.131| mysql-proxy|虚拟机-centos7_mini|只需要安装代理，可以不装MySQL|
|192.168.1.132| 测试client|虚拟机-centos7_mini|mysql-5.6.40|

![](http://img.lypeng.com/uploads/image/5b431b29909b0.jpg)

一台物理机（总内存是8G），三个虚拟机（每个的内存都是1G）

## 图片说明(测试流程)
测试机安装MySQL，链接代理机器，使用创建的代理用户，执行写与读操作。然后观察master（129）上的数据是否新增成功，读取的数据是否来自slave（130），如果是即完成目标：读(从库)写(主库)分离～

![](http://img.lypeng.com/uploads/image/5b4333a8158a2.jpg)
可以说是分为server-proxy-client三层～

## 流程简要说明
#### 先按照上篇文章配置主从同步
参见上篇文章，在主库与从库上创建一个测试用户
`GRANT ALL PRIVILEGES ON mydb.* TO 'proxy'@'%' identified by 'proxy123';`

#### 安装lua
```bash
wget http://www.lua.org/ftp/lua-5.3.4.tar.gz
tar -zxf lua-5.3.4.tar.gz
cd lua-5.3.4
make linux
```
报错了：致命错误，没有readline.h文件，百度一下，执行下面命令即可。
`yum install libtermcap-devel ncurses-devel libevent-devel readline-devel -y`
继续安装
```bash
`make clean` #清理之前的编译
`make linux ` #重新编译
`make install` #继续安装
```
安装完成后测试
```bash
lua -v #查看版本返回Lua 5.3.4 版权等信息
vim hello.lua #里面写上 print(&quot;hello world!&quot;)
lua hello.lua #执行
```
![](http://img.lypeng.com/uploads/image/5b431ec303337.jpg)

#### 安装mysql-proxy
去官网下载：https://downloads.mysql.com/archives/proxy/?_blank
解压安装略过，移动到/usr/local/mysql-proxy目录
配置 vim /etc/mysql-proxy.cnf
```
[mysql-proxy]
user=root 
admin-username=proxy
admin-password=proxy123
proxy-address=192.168.1.131:4040
proxy-read-only-backend-addresses=192.168.1.130
proxy-backend-addresses=192.168.1.129
proxy-lua-script=/usr/local/mysql-proxy/rw-splitting.lua
admin-lua-script=/usr/local/mysql-proxy/admin-sql.lua
log-file=/usr/local/mysql-proxy/logs/mysql-proxy.log
log-level=info
daemon=true
keepalive=true
```
复制rw-splitting.lua与admin-sql.lua文件
`cp /usr/local/mysql-proxy/share/doc/mysql-proxy/rw-splitting.lua /usr/local/mysql-proxy/`
`cp /usr/local/mysql-proxy/share/doc/mysql-proxy/admin-sql.lua /usr/local/mysql-proxy/`
修改rw-spitting.lua文件
![](http://img.lypeng.com/uploads/image/5b432664cee9f.jpg)
#### 启动mysql-proxy
`/usr/local/mysql-proxy/bin/mysql-proxy --defaults-file=/etc/mysql-proxy.cnf`

#### 测试

![](http://img.lypeng.com/uploads/image/5b43224da7f95.jpg)

再从库上执行`stop slave;` 关闭同步，然后可以创建张表test（实际情况不能这么做），分别insert与select查看效果。
insert user 	成功添加到主库，因为从库关闭了同步，没有同步过来
select user 	的数据来自从库，数据与主库是有差异的
insert test 	报错，因为insert test会添加到主库中（主库不存在test表，所以报错）
select test 	正常，可以看到数据来自从库！

注：我开始测试的时候，不管读还是写，都是主库，好像从库就没有起作用，后来断开重连试下好了，恢复正常～


参考：
http://www.cnblogs.com/luckcs/articles/2543607.html?_blank
http://www.cnblogs.com/phpstudy2015-6/p/6687480.html#4003710?_blank
https://blog.csdn.net/xinzhi8/article/details/72598455?_blank (修改函数没尝试)
