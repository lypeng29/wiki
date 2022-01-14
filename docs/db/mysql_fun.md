# MySQL函数

MySQL函数包括数学函数、字符串函数、日期和时间函数、条件判断函数、系统信息函数、加密函数、格式化函数等。

## 一、数学函数

主要用于处理数字，包括整型、浮点数等。数学函数包括绝对值函数、正弦函数、余弦函数、获取随机数的函数等。
sin,cos,pi,tan等等这些不常用，常用count，sum,avg,min,max基础常见的就不说了。

```
select rand(); //随机数，返回0,1之间的随机数，16位小数

select abs(-32); //绝对值

select mod(15,7); //取余数,结果1，等价于 select 15%7;

select floor(1.23) //去尾法，不大于X的最大整数值。返回1

select ceil(1.23);//进一法，不小于X的最小整数值。ceiling 等价于 ceil，返回2

select round(1.58);//取整数，四舍五入。返回2

select format(1.999,2);//保留小数，四舍五入。返回 2.00

select truncate(1.999,2);//保留小数，直接截取，返回 1.99
```

## 二、字符串函数
```
replace(str,from_str,to_str) 替换
select replace('uploads/img/a.jpg', 'img', 'images'); //返回 uploads/images/a.jpg

repeat(str,count):重复，如果count <= 0，返回一个空字符串。如果str或count是NULL，返回NULL。
select repeat('MySQL', 3); //返回MySQLMySQLMySQL

reverse(str):反转字符串
select reverse('abc'); //返回cba

insert(str,pos,len,newstr):返回字符串str，在位置pos起始的子串且len个字符长的子串由字符串newstr代替。
select insert(‘whoisyou', 4, 2, ‘ are '); // 返回 who are you 替换is为‘ are ’

ascii(str): 返回字符串str的最左面字符的ascii代码值。如果str是空字符串，返回0。如果str是NULL，返回NULL。
select ascii('a');//97,A=65
select ascii('box');//98

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
```

## 三、日期和时间函数 

日期与时间戳转换
```
select unix_timestamp('2020-04-01 00:00:00')
select from_unixtime(1567267200,'%Y-%m-%d %H:%i:%s')
```
from_unixtime第二个参数不写，默认是年月日时分秒，全的~


## 四、流程控制与比较函数  

case ... when ... then ... when ... then ... else ... end

CASE value WHEN [compare-value] THEN result [WHEN [compare-value] THEN result ...] [ELSE result] END CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END 
在第一个方案的返回结果中， value=compare-value。而第二个方案的返回结果是第一种情况的真实结果。如果没有匹配的结果值，则返回结果为ELSE后的结果，如果没有ELSE 部分，则返回值为 NULL。
```
select CASE 11 WHEN 1 THEN 'one' WHEN 2 THEN 'two' ELSE 'more' END;
select CASE WHEN 1 > 0 THEN 'true' ELSE 'false' END;
select CASE BINARY 'B' WHEN 'a' THEN 1 WHEN 'b' THEN 2 END;
```

```
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
```

## 五、系统信息函数

获取MySQL版本号、连接数、数据库名的函数

VERSION() 返回数据库的版本号；
CONNECTION_ID() 返回服务器的连接数，也就是到现在为止MySQL服务的连接次数；
DATABASE()和SCHEMA() 返回当前数据库名。

获取用户名的函数
USER()、SYSTEM_USER()、SESSION_USER()、CURRENT_USER()和CURRENT_USER这几个函数可以返回当前用户的名称。

获取字符串的字符集和排序方式的函数
CHARSET(str) 返回字符串str的字符集，一般情况这个字符集就是系统的默认字符集；
COLLATION(str) 返回字符串str的字符排列方式。

获取最后一个自动生成的ID值的函数
LAST_INSERT_ID() 返回最后生成的AUTO_INCREMENT值。


## 六、加密函数

加密函数PASSWORD(str)
PASSWORD(str)函数可以对字符串str进行加密。

加密函数MD5(str)
MD5(str)函数可以对字符串str进行加密。

加密函数ENCODE(str,pswd_str)
ENCODE(str,pswd_str)函数可以使用字符串pswd_str来加密字符串str。加密的结果是一个二进制数，必须使用BLOB类型的字段来保存它。

解密函数DECODE(crypt_str,pswd_str)
函数可以使用字符串pswd_str来为crypt_str解密。crypt_str是通过ENCODE(str,pswd_str)加密后的二进制数据。字符串pswd_str应该与加密时的字符串pswd_str是相同的。


## 七、其它函数

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

## 八、LeetCode

单表可以多次利用

`select t1.xxx,t2.xxx from p as t1,p as t2 where ... `

查询person的信息，left join
`select FirstName, LastName, City, State from Person left join Address on Person.PersonId=Address.PersonId`


删除重复email，并保留小ID

`DELETE p1 FROM Person p1, Person p2 WHERE p1.Email = p2.Email AND p1.Id > p2.Id`

温度两天内连续上升的ID

`select w2.Id from Weather as w1 join Weather as w2 on w1.Temperature<w2.Temperature and datediff(w2.RecordDate,w1.RecordDate)=1`

对调同一字段的两个值

`update salary set sex=if(sex='f','m','f');`
`update salary set sex = char(ascii('f')+ascii('m')-ascii(sex));`

部门最高工资的员工
`select d.Name as Department,e.Name as Employee,e.Salary from Employee as e join Department as d on e.DepartmentId=d.Id and e.Salary>=(select max(Salary) from Employee as e2 where e2.DepartmentId=e.DepartmentId);`

