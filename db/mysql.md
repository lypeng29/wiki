# mysql笔记

很早前记录的，比较乱，回过来看看还是有所收获的！之后有时间再重新整理

-----------------------

mysql事务隔离级别

未提交读
已提交读
可重复读（mysql_inodb）
可串行化

具体怎么设置没记住~


锁表

读锁（共享锁）
lock table TABLENAME read;
读锁，所有线程只能读，不能写

lock table TABLENAME read local;
加local后，其他线程可以insert，不能update

读锁主要用于读取数据时，防止读取结果不正确，让其他线程不能进行写操作。

Select sum(total) from orders;
Select sum(subtotal) from order_detail;
这时，如果不先给两个表加锁，就可能产生错误的结果，因为第一条语句执行过程中，order_detail表可能已经发生了改变。因此，正确的方法应该是：
Lock tables orders read local, order_detail read local;
Select sum(total) from orders;
Select sum(subtotal) from order_detail;
Unlock tables;

lock table actor as a read,actor as b read;//如果使用别名，别名也要锁定


写锁（排它锁）
lock table TABLENAME write; 
本线程可以读取写入当前表，不能读写其他表；其他线程不能读写当前表，可以读写别的表
...
unlock tables;

show engine innodb status
show create table TABLENAME;
show index from TABLENAME;
show table status like 'TABLENAME'\G
show engines;


索引

KEY类型：
PRI主键约束
UNI唯一约束
MUL可以重复


创建索引
alter table user add index index_name(`name`);
alter table user add primary key (`id`);
alter table user add unique (`phone`);
alter table user add fulltext (`intro`);
alter table user add name_phone (`name`,`phone`);

删除索引
alter table user drop index index_name;

普通索引(create语句不是很好记，还是alter table好用)
create index cid using btree on article(cid);
create index index_name on table_name(field_name);



联合索引
create index index_name on table_name(name,age,phoneNum);
B+树是按照从左到右的顺序来建立搜索树的。如（'张三',18,'18668247652'）来检索数据的时候，B+树会优先匹配name来确定搜索方向，name匹配成功再依次匹配age、phoneNum，最后检索到最终的数据。
name like 'aaa%'//会用到索引

为什么field2或者field2,field3字段上没有索引。这是由于BTree索引因要遵守最左前缀原则

id与name都建立单列索引
order by name desc 不会使用索引
(id,name)建立联合索引
最后需要注意mysql对排序记录的大小有限制：max_length_for_sort_data 默认为1024；也就意味着如果需要排序的数据量大于1024，则order by不会使用索引，而是使用using filesort

全文索引
alter table article add fulltext index fulltext_article (title, content);
select * from article where match(title,content) against('2012');

如何用好索引
（1）依据where查询条件建立索引；
（2）使用联合索引，而不是多个单列索引；
例如：select * from tab_a where b=? and c=?这个SQL，对b c字段建立联合索引的效率比单列的索引效率更高。

（3）联合索引中索引的顺序根据区分度排，区分度大的放在前面。区分度是指字段值的种类，字段值种类越多的字段要放在前面，例如：idx_smp(name,gender)的效率要比idx_smp(gender,name)的效率高

（4）联合索引能为前缀单列、复列查询提供帮助；

例如：
有idx_smp(a,b,c)这样的索引，where a=?或者where a=? and b=?都可以使用该索引，但是where c=?就无法使用该索引。
（5）同样的，要合理创建联合索引，避免冗余
例如建立了idx_smp（a,b,c）就不需要建立idx_smp(a)、idx_smp(a,b)索引了。
（6）order by group by distinct等需要排序的操作，在没有索引的大数据量情况下需要排序，对IO和CPU性能消耗很大。如果有类似排序需求，则需要对相关字段建立索引，这样利用索引的有序特性不需要排序，直接按着索引顺序扫描即可。
（7）select …where .. like ‘%xx’;这种%放在头部的，是无法走索引的。
（8）select ＊ 不建议使用，因为会读取大量数据，也不利于使用索引覆盖技术。索引字段能够完全在索引中获取， 就不要使用select ＊（因为会导致回表），无法完整在索引中获取，也是建议select具体字段。


mysql对于排序,使用了两个变量来控制sort_buffer_size和  max_length_for_sort_data
show processlist

存储引擎
myisam
表锁
一般情况下如果查询多建议使用myIsam 。

innodb
延迟写入，一次写入
行锁，支持事务
InnoDB表的行锁也不是绝对的，如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表，例如update table set num=1 where name like “"2%”

InnoDB不支持FULLTEXT类型的索引。

对于AUTO_INCREMENT类型的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中，可以和其他字段一起建立联合索引。
待验证

InnoDB 中不保存表的具体行数，也就是说，执行select count(*) from table时，InnoDB要扫描一遍整个表来计算有多少行，但是MyISAM只要简单的读出保存好的行数即可。注意的是，当count(*)语句包含 where条件时，两种表的操作是一样的。


create table TABLENAME(id int(11),c varchar(20))engine=innodb; 
begin;
rollback;
commit;

archive
1.只支持select and insert，不支持update and delete

csv
csv可编辑

memory
HASH索引 等值查询 地区 城市
btree

federated


创建权限
grant select,update,delete,insert on database.tablename to username@'127.0.0.1' identified by '123456';

connetion='mysql://user:123456@127.0.0.1:3306/database/tablename'

全局设置
set global name=value;

32位
mysql进程使用内存最大3G

为每个线程分配
排序缓存内存
sort_buffer_size
连接
join_buffer_size

内存溢出，超过总内存大小
数据库专用服务器

基准测试
工具生成数据

压力测试
真实数据及逻辑


mysql二进制日志
(说明，我是在windows下操作的)
statement（基于段落的日志），row（基于行的日志）

1. 配置my.ini
[mysqld]
log_bin=mysql-bin
binlog_format=statement

2. 打开mysql命令行
show variables like 'binlog_format';//查询记录方式是否statement（基于段落的日志）
flush logs;//刷新日志，mysql的data目录就会多一个mysql-bin开头的日志文件
然后做一些增删改查操作，mysql就会记录到日志文件中
重写打开一个cmd，进入bin目录
mysqlbinlog ../data/mysql-bin.000002//即可查看日志信息
mysqlbinlog ../data/mysql-bin.000002 > test.sql //即可导出为sql文件

3. 行格式
set session binlog_format=row;
show variables like 'binlog_row_image'//row的三个参数，full,minimal,noblob
CURD操作
查看日志

索引
btree-------听的模棱两可
order_sn='123456789';//全值匹配
order_sn like '123%'
> < 
order by
限制
内容占用大，mysql--》全局
not in <>

hash------
等值查询
每一行有一个hash码
限制
读取两次，1查询行，2，查询记录


慢查询日志
配置
slow_query_log=ON
slow_query_log_file="D:/phpStudy/MySQL/logs/slow.txt"
long_query_time=0.001//超过1毫秒的查询做日志记录
log_queries_not_using_indexes=1//是否记录不使用索引的查询
分析工具
1.自带mysqldumpslow
2.pt-query-digest

系统表实时查看
select id,user,host,db,command,time,state,info from information_schema.processlist;


MySQL执行顺序
1.发送请求
2.是否有缓存--------》有则返回
3.解析，预处理，优化器生成执行计划
4.存储引擎API查询数据
5.返回客户端

开启查询缓存
query_cache_type=ON 
query_cache_size=0（1024倍数）


查询优化器
表的关联顺序
min()函数，btree左端记录
count() myisam常数
提前终止结果

explain sql语句;//生成sql的优化器处理结果（即执行计划）

in()
排序----》二分查找

mysql查询各个阶段所消耗的时间

profile
session操作
set profiling=1
show profiles;
show profile for query N;

5.6->
performance_schema
启动
update setup_instruments set enabled='YES',TIMED='YES' WHERE NAME like 'stage%';

update setup_consumers set enabled='yes' where name like 'events%';

`select count(*) from sakila.film;`

pt-online-schema-change \修改表结构

--alter="modify table " --t='table' --d='database' --user
评论数统计
汇总表
create table p_comment_cnt(pid int,cnt int)//每天更新
union all



mysql优化

优化MYSQL数据库的方法:

1,选取最适用的字段属性,尽可能减少定义字段长度,尽量把字段设置NOT NULL,例如'省份,性别',最好设置为ENUM

2，使用join代替子查询

3，使用联合(UNION)来代替手动创建的临时表

4，事务处理（保证数据完整性,例如添加和修改同时,两者成立则都执行,一者失败都失败）

5，适当建立索引（如何建立索引？索引的利与弊？）

6，优化sql语句

7，explain可以看到mysql执行计划

8，分表（垂直分表，水平分表？）

执行时间，长的

开启慢查询日志，分析具体sql







