# docker学习
安装与基础命令

docker是什么？
官方：容器引擎
如果你用过github以及虚拟机，对比理解起来就更容易了～

1. dockerhub相当于github有一些镜像库包括官方的与普通用户的
2. 有了docker以后，如果想在Linux系统上，在虚拟几个系统，可以直接开启docker，然后载入多个镜像文件运行即可！不需要在安装虚拟机，然后在虚拟机里面安装操作系统～方便简洁～
3. 当然docker目前也有Windows的安装版本。注意：意思是在Windows系统中可以安装docker
4. docker里面目前没有Windows的镜像。注意：意思是在docker里面无法载入Windows的系统，因为没有镜像

执行流程：
先有一个操作系统，安装docker。然后是开启docker，运行一个容器container，载入一个镜像image，执行一些命令command，提交生成新的镜像tag，发布自己的镜像push，下次可以直接载入自己的镜像去使用了。

docker用来干什么？
1. 简化了服务器上安装多个虚拟机流程，多系统操作更方便。
2. 自己配置一个系统，在上面安装配置好各种软件，发布到dockerhub。然后可以在各个地方，不管哪儿，只要装个docker就可以加载镜像，运行自己配置好的系统。


git仓库：
https://github.com/moby/moby/releases

下载地址：
tar.gz包：
https://github.com/moby/moby/archive/v17.03.2-ce.tar.gz

deb包：
https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/

我下载的deb包，直接双击安装了～

docker文件位置
/var/lib/docker

搜索镜像
docker search centos

拉取dockerhub上的镜像,或者载入下载好的包
docker pull lypeng29/centos #从dockerhub上拉取
docker load -i centos_x86_64_7.tar #提前下载好，载入

查看可用镜像
docker images

运行一个容器container，载入镜像，执行命令
docker run -i -t lypeng29/centos /bin/bash

长久运行
JOB=$(docker run -d centos /bin/sh -c "while true;do echo 'hello world'; sleep 1; done");
echo $JOB

查看日志
docker logs $JOB

查看运行的容器信息
docker ps
docker ps -a #所有容器
docker kill 容器ID #停止容器
docker stop/start/restart 容器ID
docker rm 容器ID #移除

docker commit 容器ID centos:nmap-ncat # commit相当于打tag





dockerfile创建镜像
mkdir /docker-build
cd /docker-build
touch Dockerfile
vim Dockerfile
    FROM centos
    MAINTAINER lypeng29 <893371810@qq.com>
    RUN yum install httpd -y
    ADD start.sh /usr/local/bin/start.sh
    ADD index.html /var/www/html/index.html
vim start.sh
    /etc/init.d/httpd start
vim index.html
    docker image test file.

docker build -t centos:httpd ./

发布
1. 保存到本地tar包
+ docker save -o centos_httpd_docker.tar centos:httpd
+ docker load -i centos_httpd_docker.tar
2. 发布到dockerhub
+ docker login -u username -p password -e email
+ docker push centos:httpd
+ docker pull username/centos:httpd

docker load -i centos_httpd_docker.tar
docker run -p 9000:80 centos:httpd /bin/sh -c /usr/local/bin/start.sh

docker exec -it 04938020 /bin/bash
在容器中执行命令

