# redis-cluster构建
详细参见：https://www.cnblogs.com/diegodu/p/9183356.html
不同点：我这里使用最新的redis-5.0.5版本,服务器centos7.6，redis-cli命令代替了redis-trib.rb

## 1.安装Redis
```
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar -zxf redis-5.0.5.tar.gz
cd redis-5.0.5
make
```

初次安装报错，`make[3]: gcc: Command not found`，没有安装gcc命令

安装下：`yum -y install gcc`

再来 `make`
报错：`fatal error: jemalloc/jemalloc.h: No such file or directory`

原因：`https://blog.csdn.net/lgh1117/article/details/48270085`，不是很明白，不要紧，继续安装！
`make MALLOC=libc`,编译通过 `make install` 成功！

测试下：
```
[root@centos76 src]# ./redis-server &
[root@centos76 src]# ./redis-cli
127.0.0.1:6379> set a 'bbb'
OK
127.0.0.1:6379> get a
"bbb"
```
测试通过！

yum install tcl

## 2.修改配置，启动集群
修改配置，创建目录，复制文件到目录，修改端口号

```
bind 0.0.0.0
daemonize yes
port 7000
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
```


```
[root@centos76 ~]# mkdir cluster-redis
[root@centos76 ~]# cd cluster-redis/
[root@centos76 cluster-redis]# mkdir 7000 7001 7002 7003 7004 7005
[root@centos76 cluster-redis]# ls
7000  7001  7002  7003  7004  7005
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7000/
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7001/
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7002/
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7003/
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7004/
[root@centos76 cluster-redis]# cp ../redis-5.0.5/redis.conf 7005/
[root@centos76 cluster-redis]# sed -i "s/7000/7001/g" 7001/redis.conf
[root@centos76 cluster-redis]# sed -i "s/7000/7002/g" 7002/redis.conf
[root@centos76 cluster-redis]# sed -i "s/7000/7003/g" 7003/redis.conf
[root@centos76 cluster-redis]# sed -i "s/7000/7004/g" 7004/redis.conf
[root@centos76 cluster-redis]# sed -i "s/7000/7005/g" 7005/redis.conf
```

逐个启动，这里注意必须进入目录里面执行，外面执行失败
`redis-server ../7001/redis.conf ` 这样写没有报错，但7001没有启动
`cd 7001 && redis-server redis.conf` 成功启动7001

`pidfile /var/run/redis_6379.pid`
`logfile "/var/log/redis.log"`



```
[root@centos76 cluster-redis]# redis-trib.rb create --replicas 0.0.0.0:7000 0.0.0.0:7001 0.0.0.0:7002 0.0.0.0:7003 0.0.0.0:7004 0.0.0.0:7005
/usr/bin/env: ruby: No such file or directory
[root@centos76 cluster-redis]# yum install ruby ruby-devel rubygems rpm-build
```

再次启动集群，警告如下
```
WARNING: redis-trib.rb is not longer available!
You should use redis-cli instead.

All commands and features belonging to redis-trib.rb have been moved
to redis-cli.
In order to use them you should call redis-cli with the --cluster
option followed by the subcommand name, arguments and options.

Use the following syntax:
redis-cli --cluster SUBCOMMAND [ARGUMENTS] [OPTIONS]

Example:
redis-cli --cluster create 0.0.0.0:7001 0.0.0.0:7002 0.0.0.0:7003 0.0.0.0:7004 0.0.0.0:7005 --cluster-replicas 0.0.0.0:7000

To get help about all subcommands, type:
redis-cli --cluster help

```
命令已经被redis-cli取代，需要加参数--cluster，可以查看help有更多集群相关命令

好，再次启动下：
```
[root@centos76 7005]# redis-cli --cluster create 0.0.0.0:7000 0.0.0.0:7001 0.0.0.0:7002 0.0.0.0:7003 0.0.0.0:7004 0.0.0.0:7005 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 0.0.0.0:7004 to 0.0.0.0:7000
Adding replica 0.0.0.0:7005 to 0.0.0.0:7001
Adding replica 0.0.0.0:7003 to 0.0.0.0:7002
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: c8e03733a0d2abfb9d923a97ded2616c4cfe4efc 0.0.0.0:7000
   slots:[0-5460] (5461 slots) master
M: 0b3adac0cd6f3fdcac04e1e4de51d1e2bd960cfd 0.0.0.0:7001
   slots:[5461-10922] (5462 slots) master
M: 77391a2cd27de69cc0f56e27b68d67b10ea5a35b 0.0.0.0:7002
   slots:[10923-16383] (5461 slots) master
S: afdffaacba010290f4f336a7097f6dca35cd9c4a 0.0.0.0:7003
   replicates 77391a2cd27de69cc0f56e27b68d67b10ea5a35b
S: 764e82a7674de2677ba84056b39788c8c1f0d80e 0.0.0.0:7004
   replicates c8e03733a0d2abfb9d923a97ded2616c4cfe4efc
S: 9dbe46eb3c461697e019572cae2033f8dc8e6d16 0.0.0.0:7005
   replicates 0b3adac0cd6f3fdcac04e1e4de51d1e2bd960cfd
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 0.0.0.0:7000)
M: c8e03733a0d2abfb9d923a97ded2616c4cfe4efc 0.0.0.0:7000
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 0b3adac0cd6f3fdcac04e1e4de51d1e2bd960cfd 127.0.0.1:7001
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: 77391a2cd27de69cc0f56e27b68d67b10ea5a35b 127.0.0.1:7002
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: afdffaacba010290f4f336a7097f6dca35cd9c4a 127.0.0.1:7003
   slots: (0 slots) slave
   replicates 77391a2cd27de69cc0f56e27b68d67b10ea5a35b
S: 9dbe46eb3c461697e019572cae2033f8dc8e6d16 127.0.0.1:7005
   slots: (0 slots) slave
   replicates 0b3adac0cd6f3fdcac04e1e4de51d1e2bd960cfd
S: 764e82a7674de2677ba84056b39788c8c1f0d80e 127.0.0.1:7004
   slots: (0 slots) slave
   replicates c8e03733a0d2abfb9d923a97ded2616c4cfe4efc
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
到这里就启动成功了，可以看到slot信息，主从信息等

## 3.测试下
```
[root@centos76 7005]# redis-cli --cluster help
Cluster Manager Commands:
  create         host1:port1 ... hostN:portN
                 --cluster-replicas <arg>
  check          host:port
                 --cluster-search-multiple-owners
  info           host:port
  fix            host:port
                 --cluster-search-multiple-owners
  reshard        host:port
                 --cluster-from <arg>
                 --cluster-to <arg>
                 --cluster-slots <arg>
                 --cluster-yes
                 --cluster-timeout <arg>
                 --cluster-pipeline <arg>
                 --cluster-replace
  rebalance      host:port
                 --cluster-weight <node1=w1...nodeN=wN>
                 --cluster-use-empty-masters
                 --cluster-timeout <arg>
                 --cluster-simulate
                 --cluster-pipeline <arg>
                 --cluster-threshold <arg>
                 --cluster-replace
  add-node       new_host:new_port existing_host:existing_port
                 --cluster-slave
                 --cluster-master-id <arg>
  del-node       host:port node_id
  call           host:port command arg arg .. arg
  set-timeout    host:port milliseconds
  import         host:port
                 --cluster-from <arg>
                 --cluster-copy
                 --cluster-replace
  help
For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.
```
create创建已经用过，其他的也都可以试试~ check比info提供的信息稍微多一些！
reshard 重新分配，删除主节点前执行，移动已有数据到别的节点
add-node,del-node 添加删除节点

fix rebalance call set-timeout import 还没有使用过！

现在的结构：
7000-->7004
7001-->7005
7002-->7003

7000挂掉，7004成功补位
```
0.0.0.0:7001 (0b3adac0...) -> 1 keys | 5462 slots | 1 slaves.
127.0.0.1:7004 (764e82a7...) -> 1 keys | 5461 slots | 0 slaves.
127.0.0.1:7002 (77391a2c...) -> 1 keys | 5461 slots | 1 slaves.
```

不同点：
7000重新启动，并没有自动加入集群中,（备注：我错了，会自动加入，只不过估计需要5-10秒，立即check是看不到的）

使用add-node增加节点！
```
add-node       new_host:new_port existing_host:existing_port
                 --cluster-slave
                 --cluster-master-id <arg>
```

```
[root@centos76 7000]# redis-cli --cluster add-node 0.0.0.0:7000 0.0.0.0:7004 --cluster-slave --cluster-master-id 764e82a7674de2677ba84056b39788c8c1f0d80e
>>> Adding node 0.0.0.0:7000 to cluster 0.0.0.0:7004

>>> Send CLUSTER MEET to node 0.0.0.0:7000 to make it join the cluster.
Waiting for the cluster to join

>>> Configure node as replica of 0.0.0.0:7004.
[OK] New node added correctly.
```
新增成功！


