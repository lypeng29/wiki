# windows中的bat命令

## bat_mysql

```
d:
cd D:\phpStudy\MySQL\bin
mysqldump -uroot -proot "v-meanhow"> F:/baksql/"v-meanhow_%date:~0,4%%date:~5,2%%date:~8,2%%time:~0,2%%time:~3,2%%time:~6,2%.sql"
pause
```

## winrar压缩文件夹
```
@echo off
c:
cd C:\Program Files\WinRAR
WinRAR.exe a -r -ep1 G:\filebak\meanhow_uploads_%date:~0,4%%date:~5,2%%date:~8,2%.zip F:\www\meanhow\uploads\
pause
```

## 下载文件

### 方式一、使用powershell
```
start powershell
$client = new-object System.Net.WebClient
$client.DownloadFile('http://www.tt.com/yujian.mp3','F:\b.mp3')
pause
```

### 方式二、使用windows版的wget

下载windows_bat.zip,解压到D盘【下载地址：`http://t.dpshop.net/download/windows_wget.zip`】

```
@echo off
d:
cd wget
wget http://sqlbak.v-zm.net/meanhow_Uploads_%date:~0,4%%date:~5,2%%date:~8,2%.zip -O E:\sqlbak\meanhow_Uploads__%date:~0,4%%date:~5,2%%date:~8,2%.zip
pause
```