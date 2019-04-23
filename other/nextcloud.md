# nextcloud私有网盘

1.nextcloud是什么？
类似百度网盘，SkyDrive，owncloud，还有个什么，用来存储文件视频音乐等等的，有网页版，客户端，Android端，iOS端等等

2.要求PHP>5.6.0
我的环境：lnmp,centos+nginx+mysql+php7.1.15
nextcloud要求PHP>5.6.0,编译安装一个7.1.15还是很快的，在php.net下载tar包，解压，编译，安装，一会儿就完了，具体不说了～

3.500错误
刚把下载好的代码放到www目录，访问网址报错500！
解决：开启config目录写权限，还好装owncloud的时候，开始就提示config没有写权限

4.其他目录权限问题
开启apps写权限，新建你的文件存储目录，默认是data，但是data文件夹不存在，需要手动创建，并且给写权限

5.需要开启pathinfo(nginx)
见6
6.https配置
申请了证书，下载下来放到/usr/local/nginx/conf/ssl/目录中，给server增加443配置
```bash
server
   {
       listen 443 ssl;
       server_name www.dpshop.net;
       index index.html index.php ;
       root  /home/wwwroot/nextcloud;
        location ~ [^/]\.php(/|$)
        {
            fastcgi_pass  unix:/tmp/php-cgi.sock;
            fastcgi_index index.php;
            include fastcgi.conf;
			#pathinfo配置
            fastcgi_split_path_info ^(.+?\.php)(/.*)$;
			set $path_info $fastcgi_path_info;
			fastcgi_param PATH_INFO       $path_info;
			try_files $fastcgi_script_name =404;
        }
		# https配置
       ssl on;
       ssl_certificate ssl/1_www.dpshop.net_bundle.crt;
       ssl_certificate_key ssl/2_www.dpshop.net.key;
       ssl_session_timeout 5m;
       ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
       ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
       ssl_prefer_server_ciphers on;

}
```

7.英文版转中文版
网页版的是英文的，看别人有中文版的，目前还没解决，不过影响不大，大致都能看懂～

8.没有找见Linux的客户端安装包，里面是appimage
直接用owncloud的deb包，下载地址：https://www.dpshop.net/index.php/s/AKk2w3mNfYZ2zqe?_blank

9.刚开始，还不知道有没有其他什么问题

10.附录，下载地址
总的下载地址，里面有各种终端与版本：https://download.nextcloud.com/?_blank

我使用的版本如下：

server端
https://download.nextcloud.com/server/releases/nextcloud-13.0.0.zip?_blank

android端
https://download.nextcloud.com/android/nextcloud-30000299.apk?_blank

Ubuntu客户端
使用owncloud的连上了～

附图
![](http://img.lypeng.com/uploads/image/5a9fb617e3fb3.jpg)

![](http://img.lypeng.com/uploads/image/5a9fb65d2b817.jpg)

![](http://img.lypeng.com/uploads/image/5a9fb63244c6e.jpeg)

![](http://img.lypeng.com/uploads/image/5a9fb63e50a09.jpeg)