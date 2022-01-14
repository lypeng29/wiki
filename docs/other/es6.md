# front es6
es6也称为es2015 全称EMCAScript2015

详情请阅读：http://es6.ruanyifeng.com/#docs/intro


## 1.let 与 var const
{
	let a=2;
	var b=3;
}
a ReferenceError：a is not defined
b //返回3

{
	let a=5;
	a //返回5
}

let只在定义的代码块中起作用

## 2.变量是有作用域的
```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

## 3. 字符串拼接
```
s1='aaa';s2='bbb';
s3 = s1+'dfdf'+s2
s4 = `===${s1}===${s2}===` //反引号的可以执行，双引号不行
```

## 4.解构赋值

## 5.map对象

## 6.数组

## 7.循环
for in 索引
for of value

//for in循环与for of循环的区别
var arr = ['apple','banana','orange','pear'];
for(var i in arr){
// i打印出来的就是arr数组对应的索引
// 0 1 2 3
console.log(i);
}
for(var i of arr){
// i值打印出来的就是我们想要的数组具体的值
// apple banana orange pear
console.log(i);
}

## 8.箭头函数

## 9.对象的简洁语法
```
//传统对象_单体模式写法 key-value模式
var person = {
    name:'krry',
    age:21,
    showName:function(){
        return this.name;
    },
    showAge:function(){
        return this.age;
    }
};
// 调用
console.log(person.showName()); // krry
console.log(person.showAge()); // 21

//ES6_单体模式写法  不需要写key
var name = 'krry';
var age = 21;
var person = {
    name,
    age,
    showName(){
        return this.name;
    },
    showAge(){
        return this.age;
    }
};
// 调用
console.log(person.showName()); // krry
console.log(person.showAge()); // 21
```

## 10.类和继承
传统写法
```
//传统写法原型继承
function Person(name,age){ // 类、构造函数
    this.name = name;
    this.age = age;
}
Person.prototype.showName = function(){
    return this.name;
};
Person.prototype.showAge = function(){
    return this.age;
};
// 工人类
function Worker(name,age){
    // 属性继承过来
    Person.apply(this,arguments);
}
// 原型继承
Worker.prototype = new Person();
var p1 = new Person('allen',28);
var w1 = new Person('worker',1000);
console.log(w1.showName()); // 确实继承过来了 result：worker
```

es6写法
```
class Person{
    // 构造器
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    showName(){
        return this.name;
    }
    showAge(){
        return this.age;
    }
}
class Worker extends Person{
    constructor(name,age,job='啦啦啦'){
        // 继承超父类的属性
        super(name,age);
        this.job = job;
    }
    showJob(){
        return this.job;
    }
}
var p1 = new Person('aaa',18);
var w1 = new Person('www',36);
var w2 = new Worker('wwwwwwww',90);
console.log(w1.showName()); // www
console.log(w2.showJob()); // 默认给的值 ‘啦啦啦’
```

## 11.模块化 export 和 import

在一个文件中：
```
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}
```
然后在另一个文件中这样引用：
```
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3));
```

总结
```
//mod.js
// 第一种模块导出的书写方式(一个个的导出)
// 导出普通值
export let a = 12;
export let b = 5;
// 导出json
export let json = {
    a,
    b
};
// 导出函数
export let show = function(){
    return 'welcome';
};
// 导出类
export class Person{
    constructor(){
        this.name = 'jam';
    }
    showName(){
        return this.name;
    }
}

//index.js
//导出模块如果用default了，引入的时候直接用，若没有用default，引入的时候可以用{}的形式
// 导入模块的方式
import {
    a,
    b,
    json,
    show,
    Person
} from './mod.js';
console.log(a); // 12
console.log(b); // 5
console.log(json.a); // 12
console.log(json.b); // 5
console.log(show()); // welcome
console.log(new Person().showName()); // jam

//mod1.js
// 第二种模块导出的书写方式
let a = 12;
let b = 5;
let c = 10;
export {
    a,
    b,
    c as cc // as是别名，使用的时候只能用别名，特别注意下
};

//index1.js
// 导入模块的方式
import {
    a,
    b,
    cc // cc是导出的，as别名
} from './mod1.js';
console.log(a); // 12
console.log(b); // 5
console.log(cc); // 10

//mod2.js
// 第三种模块导出的书写方式 ---> default
// default方式的优点，import无需知道变量名，就可以直接使用，如下
// 每个模块只允许一个默认出口
var name = 'jam';
var age = '28';
export default {
    name,
    age,
    default(){
        console.log('welcome to es6 module of default...');
    },
    getName(){
        return 'bb';
    },
    getAge(){
        return 2;
    }
};

//index2.js
// 导入模块的方式
import mainAttr from './mod2.js';
var str = ' ';
// 直接调用
console.log(`我的英文名是:${mainAttr.name}我的年龄是${mainAttr.age}`);
mainAttr.default(); // welcome to es6 module of default...
console.log(mainAttr.getName()); // bb
console.log(mainAttr.getAge()); // 2

//mod3.js
var name = 'jam';
var age = '28';
export function getName(){
    return name;
};
export function getAge(){
    return age;
};

//index3.js
// 导入模块的方式
import * as fn from './mod3.js';
// 直接调用
console.log(fn.getName()); // jam
```




