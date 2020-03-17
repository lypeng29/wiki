# sed命令
*** 使用-i后会真正的修改源文件，否则只是在屏幕上显示***

`sed [-nie] ‘command’ 文本`

常用选项：
```
-n  只有经过sed处理的那一行(或者动作)才会被打印出来。*** 如果没有-n,会打印出被修改的以及所有内容 ***
-i  直接修改读取的档案内容，而不是由萤幕输出。
-e  直接在指令列模式上进行 sed 的动作编辑；
```
常用命令：(pdiacs)
```
p   ∶打印，亦即将某个选择的资料印出。通常 p 会与参数 sed -n 一起运作
d   ∶删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i   ∶插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
a   ∶新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)
c   ∶取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
s   ∶替换，可搭配正则表达式！如 1,20s/old/new/g
r   :读入，读取外部文件到匹配行的下面
w   :写出，匹配到的内容写出到新文件中
```
举例：（假设我们有一文件名为a.txt，为了测试，随便写点东西进去）

`vim a.txt`

```
222 have a good day
333 enjoy every day
44444 two
555555555 efghiljk;mnopqrstuvwxyz
#test lianxi
66666666 ip 127.0.0.1
#test0.0.1
9999988888888
#test
99999999999
#test
bye!
```


删除某行

```
sed '1d' a.txt              #删除第一行 
sed '$d' a.txt              #删除最后一行
sed '1,2d' a.txt           #删除第一行到第二行
sed '2,$d' a.txt           #删除第二行到最后一行
```

显示某行

```
sed -n '1p' a.txt           #显示第一行 
sed -n '$p' a.txt           #显示最后一行
sed -n '1,2p' a.txt        #显示第一行到第二行
sed -n '2,$p' a.txt        #显示第二行到最后一行
```

使用模式进行查询

```
sed -n '/ruby/p' a.txt    #查询包括关键字ruby所在所有行
sed -n '/\$/p' a.txt        #查询包括关键字$所在所有行，使用反斜线\屏蔽特殊含义
```

增加一行或多行字符串

```
sed '1a drink tea' a.txt  #第一行后增加字符串"drink tea"
sed '1,3a drink tea' a.txt #第一行到第三行后增加字符串"drink tea"
sed '1a drink tea\nor coffee' a.txt   #第一行后增加多行，使用换行符\n
```

代替一行或多行

```
sed '1c Hi' a.txt                #第一行代替为Hi
sed '1,2c Hi' a.txt             #第一行到第二行代替为
```

替换一行中的某部分

> 格式：sed 's/old/new/g'   （要替换的字符串可以用正则表达式）

```
sed 's/ruby/bird/g' a.txt  #直接替换
sed -n '/ruby/p' a.txt | sed 's/ruby/bird/g'    #先查找再替换ruby为bird
sed -n '/ruby/p' a.txt | sed 's/ruby//g'        #先查找再删除ruby
```

插入（如果没有指定-i参数，文件本身不会发生变化）
```
sed -i '$a bye' a.txt         #在文件a.txt中最后一行（用$指示）直接输入"bye"
```

删除匹配行

```
sed -i '/匹配字符串/d'  filename  （注：若匹配字符串是变量，则需要“”，而不是‘’。记得好像是）
```

替换old字符串为new并插入
```
sed -i 's/old/new' filename
```

替换匹配行中的某个字符串

```
sed -i '/匹配字符串/s/替换源字符串/替换目标字符串/g' filename

#将匹配字符串123789，匹配成功后的内容里面的所有789替换为456
sed -i '/123789/s/789/456/g' filename
```

编辑-e 一行可以有多个-e将命令串联起来
`sed -e '1,5d' -e 's/test/check/' a.txt`
先删除1-5行，然后替换test为check


r 读
cc.txt里的内容被读进来，显示在与222匹配的行后面，如果匹配多行，则cc.txt的内容将显示在所有匹配行的下面：
`sed -i '/22/r cc.txt' a.txt`

a.txt内容为
1
22
333abcd
4444abcd
cc.txt内容
aaa
bbbbbbbbbbbbbbbbb

执行后`sed -i '/22/r cc.txt' a.txt`后，a.txt变为
1
22
aaa
bbbbbbbbbbbbbbbbb
333abcd
4444abcd


w 写
在a.txt文件中所有包含abcd的行都被写入cc.txt里：
`sed -n '/abcd/w cc.txt' a.txt`

