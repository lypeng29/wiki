# hadoop笔记

简单介绍
hadoop是Apache的一个项目，主要是用来对海量的数据进行存储和运算~
存储使用：HDFS(Hadoop Distributed File System，分布式文件系统)
运算使用：MR(MapReduce)

在Ubuntu中体验
安装：
1.安装JDK
hadoop是用Java编写的，运行时需要调用Java的一些东西，所以需要先安装jdk环境！
下载jdk.tar.gz-->tar -zxvf解压-->mv jdk /usr/soft/ -->增加JAVA_HOME,修改PATH
2.安装Hadoop
下载hadoop-->tar-->mv-->修改PATH

测试：
source environment	//使环境变量生效
reboot
-------------
java -version
hadoop version
---------------------

再来理解一下存储与运算，为什么学习Hadoop，文件系统主要操作有哪些，运算的操作呢？
回答下：（附：我目前的理解）
1.为了存储大量的数据及处理运算，数据有哪些，例如：图片，样式，代码，数据库等等
2.文件的操作：复制，粘贴，新建，删除，读，写
3.运算：对数据进行加减乘除，拆分，合并，拼接，计算

namenode(名称节点)
datanode(数据节点)

配置
伪分布模式配置，一个主机
安装ssh
core-site.xml
hdfs-site.xml
mapred-site.xml
yarn-site.xml

<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>localhost</value>
	</property>
</configuration>

ssh通信，公钥与私钥
ssh-keygen

sudo apt-get install ssh
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat id_rsa.pub >> authorized_keys
ssh localhost(192.168.1.108)
exit	//断开连接

//格式化
hadoop(hdfs) namenode -format

//导入环境变量
export HADOOP_CONF_DIR=$HADOOP_INSTALL/etc/hadoopwfb

//启动后台进程，守护进程
//逐个启动
start-dfs.sh --config $HADOOP_INSTALL/etc/hadoopwfb	//fs(namenode,datanode)
start-yarn.sh --config $HADOOP_INSTALL/etc/hadoopwfb	//yarn(resource manager,node manager)

//一次启动所有,3.0.0版本里面不存在，提示已过时，请使用上面两个命令代替
start-all.sh --config $HADOOP_INSTALL/etc/hadoopwfb

jps查看启动的进程
jps -lv查看类(java)

http://localhost:50070(名称节点)
http://localhost:8088(资源管理器)
http://localhost:19888(历史服务器)

hadoop fs -mkdir /user


停止
stop-yarn.sh
stop-dfs.sh
stop-all.sh



start hdfs-all.sh --config=''
hadoop fs -ls /
hadoop fs -mkdir /user


完全分布模式配置
ln -s hadoop_cluster hadoop

从主机s0克隆三台虚拟机
s1 s2 s3

1.修改s0的hosts文件
192.168.11.0 s0
192.168.11.1 s1
192.168.11.2 s2
192.168.11.3 s3

远程拷贝
sudo scp /etc/hosts root@192.168.11.1:/etc/hosts
ssh s1

2.修改那几个配置文件，core-site.xml
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://s0</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>

<configuration>
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>s0</value>
	</property>
</configuration>

数据节点，修改slaves文件
s1
s2

3.将配置文件远程复制到其他主机
scp -r hadoop_cluster ubuntu@s1:/usr/soft/hadoop-3.0.0/etc/
//查看是否复制完成
ssh s1 cat /usr/soft/hadoop-3.0.0/etc/hadoop_cluster/core-site.xml

4.集群启动
hdfs namenode -format//先格式化

//RAID
RAID - 0
两块硬盘 同时存，同时读
缺点：容易丢失

RAID - 1
两块硬盘 A + B(备份)
A繁忙，从B读取，B损坏，从A读取

缺点：冗余大

RAID - 5
三块硬盘
A+B--打散存储
C----文件校验,校验发生错误，替换A/B中的文件

打散存储

RAID -10
四块硬盘
A+B------RAID 1
|
|-RAID-0
|
C+D------RAID 1

//namenode
只有一个，可以用raid
//datanode
不需要raid，有很多副本
一个存，两个副本
A	B		C
存	备份	备份

//namenode
1.记录日志
2.datanode损坏时，创建block副本


启动脚本分析

\sbin\start-dfs.cmd
|
|--\libexec\hadoop-config.cmd
	|--hadoop-env.cmd
	|--classpath opts
|--\bin\hadoop.cmd --namenode
	|--libexec\hadoop-config.cmd
	|--\bin\hdfs.cmd namenode
		|--\libexec\hadoop-config.cmd
		|--\etc\hadoop-env.cmd
		|--java classpath
	|--\bin\mapred.cmd
		|--\libexec\hadoop-config.cmd
		|--\etc\mapred-env.cmd
		|--classpath opts
	|--java -classpath classpath\classname
|--\bin\hadoop.cmd --datanode


\sbin\start-yarn.cmd --config etc
|
|--libexec\yarn-config.cmd
	|--\libexec\hadoop-config.cmd
	|--YARN_CONF_DIR
|--bin\yarn.cmd ResourceManager
	|--\libexec\yarn-config.cmd
	|--etc\yarn-env.cmd
	|--classpath
	|--java -classpath classpath\classname
|--bin\yarn.cmd NameManager


MapReduce

输入数据
map
//1.添加行号
//2.输出key-value对
shuffle
//分组及排序
reduce
//处理
//输出

编码
java脚本


执行
1.上传jar包源码
2.上传数据
3.执行

cp /mnt/hgfs/downloads/.jar ~/Downloads/

//上传数据
hadoop fs -put /mnt/hgfs/downloads/19*.gz /user/data/

hadoop jar HadoopDemo2.jar /user/data/ /user/out
















