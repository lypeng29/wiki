# 腾讯云centos服务器安装docker

## 安装
`yum -y install docker-ce`

正在安装:
 docker-ce                 x86_64         3:18.09.7-3.el7            docker-ce-stable          19 M
为依赖而安装:
 container-selinux         noarch         2:2.99-1.el7_6             extras                    39 k
 containerd.io             x86_64         1.2.6-3.3.el7              docker-ce-stable          26 M
 docker-ce-cli             x86_64         1:18.09.7-3.el7            docker-ce-stable          14 M
 libseccomp                x86_64         2.3.1-3.el7                os                        56 k

安装很慢，于是一个一个安装
```
yum install container-selinux #成功
yum install libseccomp #成功
yum install containerd.io #很慢，但也成功了
yum install docker-ce-cli #失败
```

单独下载文件，上传到服务器安装~成功~
rpm -i docker-ce-cli.xxx.rpm
rpm -i docker-ce.xxx.rpm


## 启动
`service docker start`
```
docker -v
docker info
```
测试通过

## 实践
```
mkdir docker_demo
cd docker_demo
vim Dockerfile #内容参见：http://www.easyswoole.com/cn/Introduction/install.html
docker build -t my_swoole:v1 .
```

# 安装dnmp

# 安装laravel环境

# win10 docker

1. hyper-v目录设置问题，//打开regedit
2. docker is starting. //打开任务管理器，关闭所有docker相关进程，服务，然后重启
3. Docker拉取镜像报错no matching manifest for unknown in the manifest list entries
`https://blog.csdn.net/z69183787/article/details/86741760`
