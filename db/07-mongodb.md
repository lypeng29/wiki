# db 07.mongodb

## 基础概念
mongodb是一种基于文档的数据库，库表都会自动创建，不用提前创建，一张表称为一个集合（collections），一条记录称为一篇文档（json格式），每篇文档默认都有一个_id列，CURD操作：insert、remove、update、find

mongodb和mysql类似，有数据库，表/集合，行/文档，索引，主键/自动创建_id,等概念

系统默认包含的数据库admin,local,config(同mysql有保留数据库：information_schema,performance_schema,mysql)
数据库名全部小写，特殊字符不能用等

文档（记录）
文档是一个键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

元数据
在MongoDB数据库中名字空间 <dbname>.system.* 是包含多种系统信息的特殊集合(Collection)，如下:
dbname.system.namespaces	列出所有名字空间。
dbname.system.indexes	列出所有索引。
dbname.system.profile	包含数据库概要(profile)信息。
dbname.system.users	列出所有可访问数据库的用户。
dbname.local.sources	包含复制对端（slave）的服务器信息和状态。
对于修改系统集合中的对象有如下限制。
在{{system.indexes}}插入数据，可以创建索引。但除此之外该表信息是不可变的(特殊的drop index命令将自动更新相关信息)。
{{system.users}}是可修改的。 {{system.profile}}是可删除的。

数据类型
String	字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。
Integer	整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。
Boolean	布尔值。用于存储布尔值（真/假）。
Double	双精度浮点值。用于存储浮点值。
Min/Max keys	将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。
Arrays	用于将数组或列表或多个值存储为一个键。
Timestamp	时间戳。记录文档修改或添加的具体时间。
Object	用于内嵌文档。
Null	用于创建空值。
Symbol	符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。
Date	日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。
Object ID	对象 ID。用于创建文档的 ID。
Binary Data	二进制数据。用于存储二进制数据。
Code	代码类型。用于在文档中存储 JavaScript 代码。
Regular expression	正则表达式类型。用于存储正则表达式。
（数据类型还是很多的，数组，时间戳，代码，正则感觉不错，目前暂不知道长度有没有限制，以前的text类型现在该用哪个，是不是对象？暂且往后看~）
字符串，整型，浮点值，text（对象），时间戳，有这几个平时使用基本够了！

MongoDB连接命令格式
mongodb://admin:123456@localhost/test

## 常用命令
```bash
show dbs;
use mydb;
show collections;
db.col.insert([{'name':'lilei','age':19},{'name':'liming','age':19}])
db.remove({'age':29});//删除所有=29
db.remove({'age':19,true});//删除一行
db.col.update({"name":"lilei"},{$set : {"age":22}},{'multi':true})    //multi:true全部修改
db.col.update({"name":"lilei"},{$inc : {"age":2}})
db.col.update({"name":"lilei"},{$rename : {"name":'username'}})
db.col.find();
db.col.findOne();

db.col.find({"age":19})
db.col.find({"age":{$ne : 19}})
db.col.find({"age":{$in : [19,20,21,22,29]}})
db.col.find({"age":{$nin : [19,20]}})
db.col.find({"age":{$exists:1},'name':'lilei'})
db.col.find({"$where":"this.age==19 && this.name=='lilei'"})

cursor = find().limit(3);
while(cursor.hasNext()){
	printjson(cursor.next());
}
cursor.forEach(function(obj){
    print(obj.name + ' age is : ' + obj.age);
})
```

```
db.goods.insert([
{'_id':4,'cat_id':7,'price':23},
{'_id':5,'cat_id':4,'price':24},
{'_id':6,'cat_id':6,'price':25},
{'_id':7,'cat_id':7,'price':26},
{'_id':8,'cat_id':6,'price':22}
])

db.goods.aggregate([
{"$match":{}},{$group:{"_id":"$cat_id","pj":{$avg:"$price"}}}
])

var map = function(){
	emit(this.cat_id,this.price)
}

var reduce = function(cat_id,price){
	return Array.avg(price);
}

db.goods.mapReduce(map,reduce,{out:"res"})

mongoimport.exe --file=dz.csv --type=csv --headerline -d mydb -c dz

var map = function(){
	var wd = parseInt(this.wd/3)*3 ;
	var jd = parseInt(this.jd/3)*3 ;
	var area=wd + ":" + jd ;
	emit(area,1);
}
var reduce = function(area,num){
	return Array.sum(num);
}
db.dz.mapReduce(map,reduce,{out:"dzres"})

var cursor=db.dzres.find();
var row;
cursor.forEach(function(obj){
	row=obj._id.split(":");
	db.reli.insert({"lng":row[1],"lat":row[0],"count":obj.value})
})

mongoexport.exe -d mydb -c reli -o dz.json
```

## 用户权限
目前所用版本：mongodb 4.0.5

默认是不需要权限认证的，也不存在任何账户

1.启动服务端
mongod --dbpath=D:\\MongoDB\\data\\db
2.启用客户端，添加用户
```
#启动客户端
mongo
#创建超级管理员账户
use admin
db.createUser(user:'root',pwd:'root',roles:[{'role':'userAdminAnyDatabase','db':'admin'}]})

#给local数据库创建账户
use local
db.createUser(user:'test',pwd:'test888',roles:[{'role':'readWrite','db':'local'}]})

#Ctrl+C 退出
```

3.重启服务端（注意多了参数--auth）
mongod --dbpath=D:\\MongoDB\\data\\db --auth

4.客户端重新连接
```
mongo
use local #切换到local数据库
db.auth('test','test888') #用户认证 #认证通过，愉快的去操作
```

5.如果你想看其他数据库信息，则需要退出切换到admin数据库，用root账户认证
```
show dbs; //报错，没有权限
db.auth('root','root') //继续报错，认证失败

use admin
db.auth('root','root') //认证通过，返回1
```

说明：一个连接，只允许一个认证，不允许一会儿切换到root，一会儿切换到test。若要切换用户，必须断开重新连接，或重启一个cmd窗口


## php操作MongoDB
```
<?php
/**
1.将mongodb添加到windows服务
// mongod.exe --logpath=d:\mongodb\logs\mongo.log --logappend --dbpath=d:\mongodb\db --serviceName=mongodb --install
添加为服务的好处：默认在后台运行，使用时直接连接，以后不用每次开机都进cmd开个服务挂在那里！
2.mongo.exe回车，默认连接到127.0.0.1:27017/test数据库了，然后就可以用命令操作了！
3.用php来操作，基本配置
添加扩展文件，修改php.ini，重启phpstudy，查看phpinfo是否出现mongodb支持（若不出现，可以将php的bin目录添加到path，然后重启电脑，我这里就是，配置完了，再怎么重启phpstudy都不出来，重启电脑好了！）
4.代码操作
不管网上哪儿的教程，都不如看文档里面的详细：绕了一大圈，最后回到这里：英文不懂没关系，下面有例子：
文档地址：http://php.net/manual/en/mongocollection.batchinsert.php

5.php操作mongodb的demo，下面仅是一些常用实例

*/

// echo MongoClient::VERSION;
$con    = new MongoClient('mongodb://127.0.0.1:27017'); //VERSION >= 1.3.0
// $con    = new Mongo('mongodb://127.0.0.1:27017'); //VERSION < 1.3.0
$db     = $con->selectDB("mydb");//或者$db=$con->mydb;
$col    = $db->users;


// 增加

// //单条插入
// $data = array( 
//   "name" => "one",
//   "age" => 1
// );

// 插入的时候可以指定_id
// $data = array( 
//     "_id"=>2,
//     "name" => "two",
//     "age" => 2
// );
// $result=$col->insert($data);
// if($result['ok']){
//     echo 'insert success!';
// }

//批量插入方式一
// $users[]=array('name'=>"li","age"=>20);
// $users[]=array('name'=>"wang","age"=>22);
// $users[]=array('name'=>"liu","age"=>21);
// $users[]=array('name'=>"zhang","age"=>22);

// $result=$col->batchInsert($users);

//批量插入方式二
// $usera=array('name'=>'ma',"age"=>22);
// $userb=array('name'=>'han',"age"=>21);
// $result=$col->batchInsert(
//     array($usera, $userb),
//     array('continueOnError' => true)
// );


//删除

// var query = db.foo.find().limit(5);
// query.remove();
// db.foo.find().limit(200).remove();

//删除全部符合条件的
//$result = $col->remove( array('age'=> 21 ) );
//只删除一条
//$result = $col->remove( array('age'=> 21 ) , array("justOne" => true));

//修改
// $result = $col->update(
//     array('name'=>'li'),
//     array('$set'=> array('age'=>'23')),
//     array("upsert" => true)
// );

/*查询*/
 // $result = $col->find();
 // $result = $col->find( array('name'=>'li' , 'age'=>'21') );
 // $result = $col->find( array('age'=>array('$gt'=>5,'$lt'=>25)) );
 // $result = $col->find( array('_id'=>2 , 'sub.uid'=>5 ) );
    
//获取总数
// $count = $col->find(array('age'=>array('$gt'=>21)))->count();
// echo $count;
// echo '<hr/>';
//越过多少
//$result = $col->find()->skip(2);
//排序
//$result = $col->find()->sort(array("age" => 1)）;//age asc
//返回字段，find第二个参数
//$result = $col->find( array(), array('content') );
//$result = $col->find( array(), array('content' => 0 ) ); //忽略字段


// $cursor = $col->find(array(),array('age'))->sort(array("age"=>1));
// foreach ($cursor as $document) {
//     // echo $document["_id"] ."  ". $document["age"];
//     echo $document["age"] . "<br/>";
// }
?>
```