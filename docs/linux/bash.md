# bash shell笔记
## hello world
将以下内容保存为test.sh
```
#！/bin/bash
# 这里是注释内容
echo "hello world"
```

给脚本添加执行权限,以下三个任选一个都可以，-rwxrwxrwx,权限顺序ugo,a是所有，省略为a
```
chmod u+x test.sh
chmod +x test.sh
chmod a+x test.sh
```

执行文件
```
./test.sh
#或使用 sh test.sh
```
## 获取用户输入用read
```
#!/bin/bash
echo "please input your age:"
read AGE
echo "your age is $AGE"
```
执行如下
```
[lypeng@localhost 桌面]$ ./t.sh
please input your age:
88
your age is 88 
```

## 变量定义
1.变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样
2.定义变量不要（$）,使用变量的时候才要（$）
3.使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。
4.unset删除变量，被删除后不能再次使用；unset 命令不能删除只读变量。
```
myUrl="http://www.aaa.com"
myNum=100
echo $myUrl
echo ${myUrl} #变量名可以加大括号
readonly myUrl
myUrl="http://www.xxx.com"
echo $myUrl
#执行结果如下，报了一个警告，只读变量
[lypeng@localhost 桌面]$ ./t.sh
http://www.aaa.com
http://www.aaa.com
./t.sh: line 7: myUrl: readonly variable
http://www.aaa.com
```

## 注释

```
# 单行注释

<<comment
这里是多行注释里面的内容
comment

```

## 特殊变量

$0	当前脚本的文件名

$n	传递给脚本或函数的参数。n 是一个数字，表示第几个参数。

$#	传递给脚本或函数的参数个数。

$*	传递给脚本或函数的所有参数。

$@	传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。

$?	上个命令的退出状态，或函数的返回值。

$$	当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。

$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以"$1" "$2" … "$n" 的形式输出所有参数。
但当它们被双引号(" ")包含时，`"$*"` 会将所有的参数作为一个整体，以"$1 $2 … 

$n"的形式输出所有参数；"$@" 会将各个参数分开，以"$1" "$2" … "$n" 的形式输出所有参数。

退出状态

$? 可以获取上一个命令的退出状态。所谓退出状态，就是上一个命令执行后的返回结果。
退出状态是一个数字，一般情况下，大部分命令执行成功会返回 0，失败返回 1。（奇葩）
不过，也有一些命令返回其他值，表示不同类型的错误。

一般使用`echo $?`查看上一个命令是否执行成功

## 变量替换
-e，使用后替换\n \r\t\v等等
```
[root@centos ~]# echo "some \n words"
some \n words
[root@centos ~]# echo -e "some \n words"
some
 words
[root@centos~]# printf "some \n words"
some
 words[root@centos ~]#

# echo -e 会转义字符串中的\n与字符串末尾的影藏字符\r
# printf  只转义字符串中的\n

#命令结果保存到变量里面，如下
m=`pwd`
echo "$m"

```

```
${var}	变量本来的值
${var:-word}	如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。
${var:=word}	如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。
${var:?message}	如果变量 var 为空或已被删除(unset)，显示message,否则显示变量var。
若此替换出现在Shell脚本中，那么脚本将停止运行。
${var:+word}	如果变量 var 被定义，那么返回 word，但不改变 var 的值。
可以这么理解，-不改变原来的值，=赋值，+和？相反
```

## 运算使用expr
```
`+-*/%` (加减乘除取于)，注意
val=`expr 2 + 2`
echo "Total value : $val"

三点注意：
表达式和运算符之间要有空格
例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
完整的表达式要被 ` ` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。
乘法必须写成：val=`expr 12 \* 2`，加反斜线

```

## 字符串操作
字符串拼接
```
name="ming"
a="hello, "$name" !"
b="hello, ${name} !"
# 放到双引号里面或者外面都可以
```

字符提取与查找
```
str="0123456789"
echo ${str:1:6}	#返回 123456

str="Alibaba is a great company"
echo `expr index "$str" b` #返回 4

```
注意：
提取的时候，从0开始；查找的时候，从1开始，查找不到返回0

## 数组
```
arr=('555' '666' '9999999999')
echo "first is :"${arr[0]}
echo "all is :"${arr[*]}
echo "all is :"${arr[@]}
# 取得数组元素的个数
lengtha=${#arr[@]}
# 或者
lengthb=${#arr[*]}
# 取得数组单个元素的长度
lengthc=${#arr[2]}
echo $lengtha
echo $lengthb
echo $lengthc
```


## if语句
```
if ...then... fi 语句；
if ...then... else ... fi 语句；
if ...then... elif ...then... else ... fi 语句。
```

注意事项：
if [ ... ] []方括号两边必须留一个空格
整数比较用 -eq -gt -lt -ge -le -ne
字符串比较 == > < >= <= !=
逻辑关系：
```
-a and
-o or
-n string   测试是否为空，空为假
-e file     测试文件是否存在
-d dirname  测试是否目录
-r File     测试是否可读
-w file     测试是否可写
-z string   测试是否为空，空为真与n相反
```

```
# 用户登录
if [ $name == 'root' -a $pass -eq 123456 ]; then
   echo 'login success'
else
   echo 'fail'
fi

# 文件是否存在(注目录也是文件)
if [ ! -e './abc' ];then
   mkdir abc
   #或者 touch abc
else
   echo 'abc not exist'
fi
```


## case,esac
```
//==========示例==========
#！/bin/bash
option="${1}"
case ${option} in
   -f) FILE="${2}"
      echo "File name is $FILE"
      ;;
   -d) DIR="${2}"
      echo "Dir name is $DIR"
      ;;
   *) 
      echo "`basename ${0}`:usage: [-f file] | [-d directory]"
      exit 1 # Command to come out of the program with status 1
      ;;
esac
//==========结果===============
$./test.sh
test.sh: usage: [ -f filename ] | [ -d directory ]

$ ./test.sh -f index.htm
$ vi test.sh
$ ./test.sh -f index.htm
File name is index.htm
$ ./test.sh -d unix
Dir name is unix
```
这个例子，开始真没看懂～评论说$1,$2是参数，还没反映过来，看他的运行结果，懂了！

## for
```
for loop in 1 2 3 45 6
do
echo $loop
done
```

