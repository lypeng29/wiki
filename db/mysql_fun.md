# MySQL函数

MySQL数据库中提供了很丰富的函数。

MySQL函数包括数学函数、字符串函数、日期和时间函数、条件判断函数、系统信息函数、加密函数、格式化函数等。MySQL函数可以对表中数据进行相应的处理，以便得到用户希望得到的数据。这些函数可以使MySQL数据库的功能更加强大。

select语句及其条件表达式都可以使用这些函数。同时，INSERT 、UPDATE、DELECT语句及其条件表达式也可以使用这些函数。例如，表中的某个数据是负数，现在需要将这个数据显示为正数。这就可以使用绝对值函数。


一、数学函数

数学函数是MySQL中常用的一类函数。主要用于处理数字，包括整型、浮点数等。数学函数包括绝对值函数、正弦函数、余弦函数、获取随机数的函数等。
sin,cos,pi,tan等等这些不常用，常用count，sum,avg,min,max基础常见的就不说了。

select rand(); //随机数，返回0,1之间的随机数，16位小数

select abs(-32); //绝对值

select mod(15,7); //取余数,结果1，等价于 select 15%7;

select floor(1.23) //去尾法，不大于X的最大整数值。返回1

select ceil(1.23);//进一法，不小于X的最小整数值。ceiling 等价于 ceil，返回2

select round(1.58);//取整数，四舍五入。返回2

select format(1.999,1);//保留小数，四舍五入。返回 2.0

select truncate(1.999,1);//保留小数，直接截取，返回 1.9


二、字符串函数

replace(str,from_str,to_str):返回字符串str，其字符串from_str的所有出现由字符串to_str代替。 
select replace('uploads/img/a.jpg', 'img', 'images'); //返回 uploads/images/a.jpg

repeat(str,count):返回由重复countTimes次的字符串str组成的一个字符串。如果count <= 0，返回一个空字符串。如果str或count是NULL，返回NULL。
select repeat('MySQL', 3); //返回MySQLMySQLMySQL

reverse(str):返回颠倒字符顺序的字符串str。
select reverse('abc'); //返回cba

insert(str,pos,len,newstr):返回字符串str，在位置pos起始的子串且len个字符长的子串由字符串newstr代替。
select insert(‘whoisyou', 4, 2, ‘ are '); // 返回 who are you 替换is为‘ are ’

ascii(str): 返回字符串str的最左面字符的ascii代码值。如果str是空字符串，返回0。如果str是NULL，返回NULL。
select ascii('a');//97,A=65
select ascii('box');//98
select ascii(0);//48

concat(str1,str2,...):返回来自于参数连结的字符串。如果任何参数是NULL，返回NULL。可以有超过2个的参数。一个数字参数被变换为等价的字符串形式。
select concat('My', 'S', 'QL');
select concat('My', NULL, 'QL')
select concat(14.3); // 相当于类型转换，float->string

length(str):返回字符串str的长度。
select length('text'); //4
select length('汉字'); //6
select charset('汉字'); //返回utf8

locate(substr,str):返回子串substr在字符串str第一个出现的位置，如果substr不是在str里面，返回0. 
select locate('a','geeafadsf'); //返回4,与PHP函数：in_array(value,array)，参数位置相同，所以记住locate

instr(str,substr):同locate，但参数位置互换。TMGT，忘记instr，重点记忆locate
select instr('nihaomdfa','i'); //返回2

left(str,len):返回字符串str的最左面len个字符。
select left('foobarbar', 5);//fooba

right(str,len):返回字符串str的最右面len个字符。 
select right('foobarbar', 3);//bar

substring(str,pos):从字符串str的起始位置pos返回一个子串。 
select substring('howareyou',4);//areyou

trim(str),rtrim(str),ltrim。去空格
select trim(' bar ');


三、日期和时间函数 

当前时间日期
now(),日期加时间，年月日时分秒
select now();//2018-07-25 10:15:08
select now()+0;//20180725101508

current_date()/curdate(),当前日期，年月日
select curdate();//2018-07-25

current_time/curtime(),当前时间，时分秒
select curtime();//10:16:15

时间加减
DATE_ADD(date,INTERVAL expr type) ,进行日期增加的操作，可以精确到秒
DATE_SUB(date,INTERVAL expr type) ，进行日期减少的操作，可以精确到秒
select "1997-12-31 23:59:59" + INTERVAL 1 SECOND;
select INTERVAL 1 DAY + "1997-12-31";
select "1998-01-01" - INTERVAL 1 SECOND;
select DATE_ADD("1997-12-31 23:59:59", INTERVAL 1 SECOND);
select DATE_ADD("1997-12-31 23:59:59", INTERVAL "1:1" MINUTE_SECOND);
select DATE_SUB("1998-01-01 00:00:00", INTERVAL "1 1:1:1" DAY_SECOND);
select DATE_SUB("1998-01-02", INTERVAL 31 DAY);

expr中间不一定用冒号分开，也可以是空格或其他
expr位数等于或小于type，如果expr多于type则返回null

//示例如下
| expr | type | 注释 |
| --- |---|---|
|"1 8 5 1" |   day_second |   //正常,加(减)1天8时5分1秒|
|"8 5 1"    |  day_second  |  //小于,加(减)0天8时5分1秒|
|"2 1 8 5 1" | day_second  |  //多于,返回null，不会加(减)2月1天8时5分1秒，因为后面定义的是：天_秒,即 天时分秒 只有四位|

其中type可以是下列值：
单个词：
MICROSECOND SECOND MINUTE HOUR DAY WEEK MONTH QUARTER YEAR
复合词：
SECOND_MICROSECOND MINUTE_MICROSECOND HOUR_MICROSECOND DAY_MICROSECOND
MINUTE_SECOND HOUR_SECOND DAY_SECOND
HOUR_MINUTE DAY_MINUTE
DAY_HOUR YEAR_MONTH


dayof开头的 (dayofweek/dayofyear) 和week

dayofweek(date)(1=星期天,7=星期六)。
select dayofweek('2018-07-24');  // 返回3，即星期二，还有一个weekday(0=星期一,6=星期天)，推荐用dayofweek吧，记住这个就好了。

dayofyear(date):返回date在一年中的日数, 在1到366范围内。即这一年的第几天
select dayofyear('2018-07-24'); //返回205,即2018年的第205天，2018已经过去一半多了

week(date,type=0) //一年当中的第几周，范围0-52，第二个参数默认0，即一周从星期天开始，1=从周一开始
week('2018-07-24'); //返回29

name结尾的（dayname/monthname）
select dayname("2018-07-24");//返回 Tuesday
select monthname(2018-07-24); //返回July。

常规单词
quarter(date) 季度（1-4）
year(date):返回date的年份，范围在1000到9999。
hour(time):返回time的小时，范围是0到23。//可以是time或者datetime，如果只有date返回0
minute(time):返回time的分钟，范围是0到59。
second(time):回来time的秒数，范围是0到59。

//使用示例
select quarter(now());  //返回3
select year(now());     //返回2018

> 关于year范围有误，year('9-05-03')//返回9,当然一般不会查询公元9年
一位数与三位数的返回本身
1-9 	//返回1-9
100-999 //返回100-999
> 两位数的特殊对待
10-69 //返回20xx，即year('18-05-03');//返回2018
70-99 //返回19xx，即year('80-05-03');//返回1980

> 当然现在是21世纪，如果到22世纪，这个两位数的结果应该会发生变化,year('18-05-03');返回2118，而不是2018，只是猜测。
> 实践证实下，将电脑时间改为2118年，结果MySQL挂了，启动不起来了。时间改回2018年，启动成功，why？
> 取中间值反复测试，2038-1-19 11:14:00之前启动成功，之后启动失败。具体多少秒没办法测，时间在流逝没法固定。网上查的到07秒。

解释：
timestamp范围1970-01-01 00:00:01 - 2038-01-19 03:14:07（UTC）
为什么是2038年，timestamp类型是4个字节，2038年01月19日03时14分07秒（二进制：01111111 11111111 11111111 11111111），二进制第一位0表示正数，1表示负数，最大值是2的31次方减1，也就是2147483647，转换成北京时间就是2038-01-19 11:14:07
。其后一秒，二进制数字会变为10000000 00000000 00000000 00000000，等价于-2147483648 = -0， 发生溢出错误，造成系统将时间误解为1901年12月13日20时45分52秒。这很可能会引起软件故障，甚至是系统瘫痪。

>过了这个时间之后怎么办？
>1) 使用datetime；
>2) 等过了再说。
>最后补充一个彩蛋：北京时间2038-01-19 11:14:07，如果某些系统还在使用MySQL的Timestamp，或者系统使用的编程语言用4字节补码整型（例如Java的int）来表示时间戳的，这些系统都会跪掉，说不定会搞出点什么大新闻（例如飞机航班系统挂掉、银行某系统跪掉），好吧，我乱猜的，等着那一天吧。（https://segmentfault.com/q/1010000004418596?_blank）

>哈哈哈，20年后，各种工具版本更新换代，应该会出现新的东西，各大技术媒体也会提前发布这种文章防范的～但不排除意外，估计会有～20年后，我在哪里？我是谁？谁知道呢？


四、流程控制与比较函数  

case ... when ... then ... when ... then ... else ... end

CASE value WHEN [compare-value] THEN result [WHEN [compare-value] THEN result ...] [ELSE result] END CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END 
在第一个方案的返回结果中， value=compare-value。而第二个方案的返回结果是第一种情况的真实结果。如果没有匹配的结果值，则返回结果为ELSE后的结果，如果没有ELSE 部分，则返回值为 NULL。
select CASE 11 WHEN 1 THEN 'one' WHEN 2 THEN 'two' ELSE 'more' END;
select CASE WHEN 1 > 0 THEN 'true' ELSE 'false' END;
select CASE BINARY 'B' WHEN 'a' THEN 1 WHEN 'b' THEN 2 END;

if(expr1,expr2,expr3) 
如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2;否则返回值则为 expr3。
select if(1 < 2,'yes ','no');
select if(strcmp('2018-07-24','2018-06-02'),'yes','no');

ifnull(A,B) // A ? A : B
select ifnull((select Salary from table order by Salary desc limit 1,1),null) as SecondSalary;

isnull(expr) //expr=null ? 1 : 0
select isnull(1+1); // 0
select isnull(1/0); // 1

nullif //expr1=expr2 ? 1 : expr1
select nullif(2,2); // 1
select nullif(3,2); // 3

strcmp(str1,str2):如果str1 > str2返回1,str1=str2反回0，str1 < str2返回-1) //平衡检测，大小比较
select strcmp(10, 8); //返回1
true = 1
false = 0
与null比较 //返回null


五、系统信息函数

系统信息函数用来查询MySQL数据库的系统信息。例如，查询数据库的版本，查询数据库的当前用户等。本小节将详细讲解系统信息函数的作用和使用方法。

获取MySQL版本号、连接数、数据库名的函数
VERSION()函数返回数据库的版本号；
CONNECTION_ID()函数返回服务器的连接数，也就是到现在为止MySQL服务的连接次数；
DATABASE()和SCHEMA()返回当前数据库名。

获取用户名的函数
USER()、SYSTEM_USER()、SESSION_USER()、CURRENT_USER()和CURRENT_USER这几个函数可以返回当前用户的名称。

获取字符串的字符集和排序方式的函数
CHARSET(str)函数返回字符串str的字符集，一般情况这个字符集就是系统的默认字符集；COLLATION(str)函数返回字符串str的字符排列方式。

获取最后一个自动生成的ID值的函数
LAST_INSERT_ID()函数返回最后生成的AUTO_INCREMENT值。


六、加密函数

加密函数是MySQL中用来对数据进行加密的函数。因为数据库中有些很敏感的信息不希望被其他人看到，就应该通过加密方式来使这些数据变成看似乱码的数据。例如用户的密码，就应该经过加密。本小节将详细讲解加密函数的作用和使用方法。
下面是各种加密函数的名称、作用和使用方法。

加密函数PASSWORD(str)
PASSWORD(str)函数可以对字符串str进行加密。一般情况下，PASSWORD(str)函数主要是用来给用户的密码加密的。

加密函数MD5(str)
MD5(str)函数可以对字符串str进行加密。

加密函数ENCODE(str,pswd_str)
ENCODE(str,pswd_str)函数可以使用字符串pswd_str来加密字符串str。加密的结果是一个二进制数，必须使用BLOB类型的字段来保存它。

解密函数DECODE(crypt_str,pswd_str)
函数可以使用字符串pswd_str来为crypt_str解密。crypt_str是通过ENCODE(str,pswd_str)加密后的二进制数据。字符串pswd_str应该与加密时的字符串pswd_str是相同的。


七、其它函数

不同进制的数字进行转换的函数
BIN(x)返回x的二进制编码；HEX(x)返回x的十六进制编码；OCT(x)返回x的八进制编码；CONV(x,f1,f2)将x从f1进制数变成f2进制数。

IP地址与数字相互转换的函数
INET_ATON(IP)函数可以将IP地址转换为数字表示；INET_NTOA(n)函数可以将数字n转换成IP的形式。其中，INET_ATON(IP)函数中IP值需要加上引号。这两个函数互为反函数。

加锁函数和解锁函数
GET_LOCT(name,time)函数定义一个名称为name、持续时间长度为time秒的锁。如果锁定成功，返回1；如果尝试超时，返回0；如果遇到错误，返回NULL。
RELEASE_LOCK(name)函数解除名称为name的锁。如果解锁成功，返回1；如果尝试超时，返回0；如果解锁失败，返回NULL；
IS_FREE_LOCK(name)函数判断是否使用名为name的锁。如果使用，返回0；否则，返回1。

重复执行指定操作的函数
BENCHMARK(count,expr)函数将表达式expr重复执行count次，然后返回执行时间。该函数可以用来判断MySQL处理表达式的速度。

改变字符集的函数
CONVERT(s USING cs)函数将字符串s的字符集变成cs

CAST(x AS type)和CONVERT(x,type)这两个函数将x变成type类型。
这两个函数只对BINARY、CHAR、DATE、DATETIME、TIME、SIGNED INTEGER、UNSIGNED INTEGER这些类型起作用。但两种方法只是改变了输出值的数据类型，并没有改变表中字段的类型。

参考
1. https://blog.csdn.net/shenpengchao/article/details/52048340?_blank
2. https://www.cnblogs.com/xuyulin/p/5468102.html?_blank

2018-07-17
MySQL进阶

最近才看到leetcode网站，感觉自己落后了，急需追赶～
看看算法，再练习下MySQL～

单表可以多次利用

join查询
left join   //查询左边表全部记录，右边的去补，没有则是null
right join  //与left相反
inner join = join   //公共记录，不会去补null，记录数 <= left/right join

ifnull(A,B) // A ? A : B
select ifnull((select Salary from table order by Salary desc limit 1,1),null) as SecondSalary;

ISNULL //null ? 1 : 0
select isnull(1+1); // 0
select isnull(1/0); // 1

NULLIF //expr1==expr2 ? 1 : expr1
SELECT NULLIF(2,2); // 1
SELECT NULLIF(3,2); // 3

查询person的信息，left join
select FirstName, LastName, City, State from Person left join Address on Person.PersonId=Address.PersonId

删除重复email，并保留小ID
DELETE p1 FROM Person p1, Person p2 WHERE p1.Email = p2.Email AND p1.Id > p2.Id

温度两天内连续上升的ID
select w2.Id from Weather as w1 join Weather as w2 on w1.Temperature<w2.Temperature and datediff(w2.RecordDate,w1.RecordDate)=1

对调同一字段的两个值
// update salary set sex=if(sex='f','m','f')
update salary set sex = char(ascii('f')+ascii('m')-ascii(sex)); //牛

部门最高工资的员工
select d.Name as Department,e.Name as Employee,e.Salary from Employee as e join Department as d on e.DepartmentId=d.Id and e.Salary>=(select max(Salary) from Employee as e2 where e2.DepartmentId=e.DepartmentId);

