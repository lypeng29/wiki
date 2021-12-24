# README

发现一个比sphinx更简单的，docsify！不用生成html

官网地址：`https://docsify.js.org/`

## 文档运行流程

本地编辑 => 提交git => 触发webhook => 服务器上的脚本文件自动更新代码 => 访问网址预览

webhook直接指向一个php脚本文件，`http://xxx.xx.com/upwiki.php`

upwiki.php 脚本内容如下
```
exec("cd /home/wwwroot/wiki && git pull 2>&1",$result,$status);
file_put_contents('/xxx/gitup.log', date("Y-m-d H:i:s",time())." wiki status: ".$status." result: ".json_encode($result, JSON_UNESCAPED_UNICODE).PHP_EOL, FILE_APPEND);
```
