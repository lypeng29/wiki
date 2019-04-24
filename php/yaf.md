# php YAF（待完成）

待完善

```
pecl install yaf
lnmp restart
```
pecl安装很方便，自动编译并配置扩展到php.ini文件

查看PHPinfo
```
[root@VM_67_10_centos ~]# php -i | grep yaf
yaf
yaf support => enabled
Supports => http://pecl.php.net/package/yaf
yaf.action_prefer => Off => Off
yaf.environ => product => product
yaf.forward_limit => 5 => 5
yaf.library => no value => no value
yaf.lowcase_path => Off => Off
yaf.name_separator => no value => no value
yaf.name_suffix => On => On
yaf.st_compatible => Off => Off
yaf.use_namespace => Off => Off
yaf.use_spl_autoload => Off => Off
```

