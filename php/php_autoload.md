# php 自动加载机制

> include 和 require 是PHP中引入文件的两个基本方法。在小规模开发中直接使用 include 和 require 没哟什么不妥，但在大型项目中会造成大量的 include 和 require 堆积。这样的代码既不优雅，执行效率也很低，而且维护起来也相当困难。

> 为了解决这个问题，部分框架会给出一个引入文件的配置清单，在对象初始化的时候把需要的文件引入。但这只是让代码变得更简洁了一些，引入的效果仍然是差强人意。PHP5 之后，随着 PHP 面向对象支持的完善，`__autoload` 函数才真正使得自动加载成为可能。

## 1. `__autoload`
实现自动加载最简单的方式就是使用  `__autoload` 魔术方法。当需要使用的类没有被引入时，这个函数会在PHP报错前被触发，未定义的类名会被当作参数传入。至于函数具体的逻辑，这需要用户自己去实现。

在类的实例化过程中，系统所做的工作大致是这样的：

```php
/* 模拟系统实例化过程 */
function instance($class)
{
    // 如果类存在则返回其实例
    if (class_exists($class, false)) {
        return new $class();
    }
    // 查看 autoload 函数是否被用户定义
    if (function_exists('__autoload')) {
        __autoload($class); // 最后一次引入的机会
    }
    // 再次检查类是否存在
    if (class_exists($class, false)) {
        return new $class();
    } else { // 系统：我实在没辙了
        throw new Exception('Class Not Found');
    }
}
```

明白了 `__autoload` 函数的工作原理之后，那就让我们来用它去实现自动加载。

test.php文件如下
```php
<?php
// __autoload，当类不存在时触发该函数，参数是类名
function __autoload($class){
	$file = $class.'.php';
	if(file_exists($file)){
		include($file);	//载入类文件
	}
}
$c = new Hello();
$c->test();
?>
```
Hello.php文件如下
```
<?php
class Hello{
	function test(){
		echo 'test function!';
	}
}
?>
```
当运行`php test.php`时，系统找不到Hello类，触发__autoload函数include类文件。

## 2. spl_autoload_register
> spl_autoload_register 函数的功能就是把传入的函数（参数可以为回调函数或函数名称形式）注册到 `SPL __autoload` 函数队列中，并移除系统默认的 `__autoload()` 函数。
即> 一旦调用 `spl_autoload_register()` 函数，当调用未定义类时，系统就会按顺序调用注册到 `spl_autoload_register()` 函数的所有函数，而不是自动调用 `__autoload()` 函数。
即spl_autoload_register可以注册多个函数
spl_autoload_register(funa());
spl_autoload_register(funb());
spl_autoload_register(func());


例如将上面的hello.php加上命名空间 `namespace app;`,然后就可以用这个函数映射机制去加载
```
spl_autoload_register(function ($class) {

    /* 限定类名路径映射 */
    $class_map = array(
        // 限定类名 => 文件路径
        'app\\Hello' => './Hello.php',
    );

    /* 根据类名确定文件名 */
    $file = $class_map[$class];

    /* 引入相关文件 */
    if (file_exists($file)) {
        include $file;
    }
});
```
这里我们使用了一个数组去保存类名与文件路径的关系，这样当类名传入时，自动加载器就知道该引入哪个文件去加载这个类了。

但是一旦文件多起来的话，映射数组会变得很长，这样的话维护起来会相当麻烦。如果命名能遵守统一的约定，就可以让自动加载器自动解析判断类文件所在的路径。接下来要介绍的PSR-4 就是一种被广泛采用的约定方式。

## 3. psr-4

PSR-4 是关于由文件路径自动载入对应类的相关规范，规范规定了一个完全限定类名需要具有以下结构：

`\<顶级命名空间>(\<子命名空间>)*\<类名>`

如果继续拿上面的例子打比方的话，顶级命名空间相当于公司，子命名空间相当于职位，类名相当于人名。那么李彦宏标准的称呼为 "百度公司 CEO 李彦宏"。

PSR-4 规范中必须要有一个顶级命名空间，它的意义在于表示某一个特殊的目录（文件基目录）。子命名空间代表的是类文件相对于文件基目录的这一段路径（相对路径），类名则与文件名保持一致（注意大小写的区别）。

举个例子：在全限定类名 \app\view\news\Index 中，如果 app 代表 C:\Baidu，那么这个类的路径则是 C:\Baidu\view\news\Index.php

我们就以解析 \app\view\news\Index 为例，编写一个简单的 Demo：

```php
$class = 'app\view\news\Index';

/* 顶级命名空间路径映射 */
$vendor_map = array(
    'app' => 'C:\Baidu',
);

/* 解析类名为文件路径 */
$vendor = substr($class, 0, strpos($class, '\\')); // 取出顶级命名空间[app]
$vendor_dir = $vendor_map[$vendor]; // 文件基目录[C:\Baidu]
$rel_path = dirname(substr($class, strlen($vendor))); // 相对路径[/view/news]
$file_name = basename($class) . '.php'; // 文件名[Index.php]

/* 输出文件所在路径 */
echo $vendor_dir . $rel_path . DIRECTORY_SEPARATOR . $file_name;
```

通过这个 Demo 可以看出限定类名转换为路径的过程。那么现在就让我们用规范的面向对象方式去实现自动加载器吧。

首先我们创建一个文件 Index.php，它处于 \app\mvc\view\home 目录中：

```php
namespace app\mvc\view\home;

class Index
{
    function __construct()
    {
        echo '<h1> Welcome To Home </h1>';
    }
}
```

接着我们在创建一个加载类（不需要命名空间），它处于 \ 目录中：

```php
class Loader
{
    /* 路径映射 */
    public static $vendorMap = array(
        'app' => __DIR__ . DIRECTORY_SEPARATOR . 'app',
    );

    /**
     * 自动加载器
     */
    public static function autoload($class)
    {
        $file = self::findFile($class);
        if (file_exists($file)) {
            self::includeFile($file);
        }
    }

    /**
     * 解析文件路径
     */
    private static function findFile($class)
    {
        $vendor = substr($class, 0, strpos($class, '\\')); // 顶级命名空间
        $vendorDir = self::$vendorMap[$vendor]; // 文件基目录
        $filePath = substr($class, strlen($vendor)) . '.php'; // 文件相对路径
        return strtr($vendorDir . $filePath, '\\', DIRECTORY_SEPARATOR); // 文件标准路径
    }

    /**
     * 引入文件
     */
    private static function includeFile($file)
    {
        if (is_file($file)) {
            include $file;
        }
    }
}
```
最后，将 Loader 类中的 autoload 注册到 spl_autoload_register 函数中：

```php
include 'Loader.php'; // 引入加载器
spl_autoload_register('Loader::autoload'); // 注册自动加载

new \app\mvc\view\home\Index(); // 实例化未引用的类

/**
 * 输出: <h1> Welcome To Home </h1>
 */
```

示例中的代码其实就是 ThinkPHP 自动加载器源码的精简版，它是 ThinkPHP 5 能实现惰性加载的关键。

至此，自动加载的原理已经全部讲完了，如果有兴趣深入了解的话，可以参考 ThinkPHP 源码。

文件地址：\thinkphp\library\think\Loader.php

原文地址：https://www.cnblogs.com/woider/p/6443854.html?_blank 有改动~

