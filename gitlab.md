# centos安装gitlab简单记录

## 安装
去官网镜像下载gitlab安装包，这里我选的是gitlab_10.5.4.rpm，直接yum安装

```bash
yum install gitlab_10.5.4.rpm -y
vim /etc/gitlab/gitlab.rm

# 增加访问网址，修改nginx监听端口
external_url = "http://gitlab.d.com"
nginx['listen_port'] = 8081

# 说明：gitlab.rm相当于全局配置，修改后，执行下gitlab-ctl reconfigure将配置同步到各个服务中去
gitlab-ctl reconfigure

# 修改hosts文件
127.0.0.1 gitlab.d.com

# 关闭防火墙，或者开放8081端口
iptables -I INPUT -p tcp --dport 8081 -j ACCEPT

# 然后使用浏览器访问测试
http://gitlab.d.com:8081

#设置root管理员密码
输入新密码

#使用新密码登录
输入root与新密码，即可登录！

```

## 创建项目测试
1. 本地生成公钥，复制内容粘贴到ssh公钥位置
2. 创建项目
3. 本地使用git clone/add/commit/push

## 相关问题
### 服务器上安装完毕，外部访问添加hosts即可
192.168.0.154(服务器IP)   gitlab.d.com

### 502错误
安装完成后，页面出现502
1. 服务器上是否已经有网站环境了，检查是否80端口占用问题
2. gitlab对内存是有要求的，官方推荐至少4G，我自己服务器只有1G，跑不起来。我给虚拟机分配了2G，可以跑，但是稍显卡顿，不过可以接受～

>
> We strongly recommend the Omnibus package installation since it is quicker to install, easier to upgrade, and it contains features to enhance reliability not found in other methods. We also strongly recommend at least 4GB of free memory to run GitLab.
>
> 来自 https://about.gitlab.com/installation/?_blank

## 常见命令
```
gitlab-ctl stop/start/restart/status

[root@localhost lypeng]# gitlab-ctl status
run: gitaly: (pid 698) 1500s; run: log: (pid 697) 1500s
run: gitlab-monitor: (pid 693) 1500s; run: log: (pid 692) 1500s
run: gitlab-workhorse: (pid 695) 1500s; run: log: (pid 694) 1500s
run: logrotate: (pid 702) 1500s; run: log: (pid 696) 1500s
run: nginx: (pid 691) 1500s; run: log: (pid 690) 1500s
run: node-exporter: (pid 729) 1499s; run: log: (pid 700) 1500s
run: postgres-exporter: (pid 707) 1499s; run: log: (pid 706) 1499s
run: postgresql: (pid 705) 1499s; run: log: (pid 704) 1499s
run: prometheus: (pid 731) 1499s; run: log: (pid 726) 1499s
run: redis: (pid 712) 1499s; run: log: (pid 711) 1499s
run: redis-exporter: (pid 710) 1499s; run: log: (pid 703) 1499s
run: sidekiq: (pid 709) 1499s; run: log: (pid 708) 1499s
run: unicorn: (pid 730) 1499s; run: log: (pid 701) 1500s
```

