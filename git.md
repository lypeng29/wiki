# git学习笔记
工作区--》暂存区--》本地版本库--》远程版本库

## 配置
使用普通用户创建

```
git config --global user.name "lypeng"
git config --global user.email "893371810@qq.com"

#区分大小写
git config core.ignorecase false
```

//创建ssh key
$ ssh-keygen -t rsa -C "893371810@qq.com"
将id_rsa.pub复制到github中

//远程操作
先创建ssh key之后再来远程操作
不用每次都输入密码，采用ssh方式克隆，把rsa_pub文件拷贝到github
git remote -v //查看远程信息
git clone git@github.com:lypeng29/baike_spider
git push

## 常规命令
git status //查看当前git状态 
git add a.txt 
git add -A //添加所有文件，A大写 
git commit -m 'add a file' 
git log 
git reflog 
git diff HEAD -- a.txt cat a.txt //查看文件内容 

## 版本回退

### 撤销本地所有修改（没有执行commit）
git checkout .
撤销git add * 操作
git reset *

### 版本回退（执行了commit）
回退所有内容到上一个版本　　　　
git　　reset　　HEAD^　　　　　　
回退a.py这个文件的版本到上一个版本　　　　　　
git　　reset　　HEAD^　　a.py　　　　　　
向前回退到第3个版本　　　　　　
git　　reset　　--soft　　HEAD~3　　　　　　
将本地的状态回退到和远程的一样　　　　　　
git　　reset　　--hard　　origin/master　　　　　　
回退到某个版本　　　　　　
git　　reset　　057d　　　　　　
回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit　　　　　　
git　　reset　　HEAD

### 远程的回退（执行了commit与push）
```
git push origin HEAD --force
//或者
git reset --hard HEAD~1
git push --force
```

## 文件删除 
rm a.txt 与 git rm a.txt -f 等价 //工作区删除文件 -f强制删除
git rm a.txt --cached //从暂存区删除文件
//如果删错了，使用checkout从版本库还原 
git checkout -- a.txt //download a.txt from origin//从本地版本库恢复文件 
//如果确要删除，commit即可 
git commit -m "del a.txt" 
git commit -a //提交所有，包括删除的~

## 远程操作
git remote rm origin //删除远程主机名 
git remote add origin git@github.com:ypengshop/tpfcms.git //添加远程主机名为origin，也可以为其他 
git push <远程主机名> <本地分支名>:<远程分支名> 
git push origin master:master //推送本地版本库到远程master分支,后面的:master可以不写， 
git push origin master //即远程与本地分支名相同均为master 
git push origin :master//推送空的本地分支到远程master分支，即删除远程master分支 
git push origin --delete master//删除远程master分支 -u 
//-u参数指将origin设置为默认远程主机，下次推送可直接使用git push,例如下面两条 
git push -u origin master //第一次推送 
git push //第二次推送

//pull
git pull origin master //从远程拉取文件
git branch --set-upstream-to=origin/master master //绑定分支进行追踪
git pull //简写

强制覆盖本地文件
git fetch --all
git reset --hard origin/master

//关于创建tag，相当于版本记录
git tag v1.0
git tag -a v1.0 -m "first version" //普通带注释的tag
git tag -s v1.0 -m "first version" //有签名的tag，前提是你有GPG私钥！GPG类似ssh，有公钥和私钥，公钥加密，私钥解密。
git tag -d v1.0 //删除

## 实际应用
git add test.txt
git commit -m 'upd test file'
git push origin master
git tag -a v1.0 -m "first version"
git push origin --tag	//tag信息，需单独推送

## 关于分支
git branch//查看分支列表
git checkout -b dev master //创建dev分支
git push origin local_branch:remote_branch 
//推送，如果remote_branch不存在则会自动创建分支。
类似，git push origin :remote_branch，local_branch留空的话则是删除远程remote_branch分支。

git checkout master //切换到master分支
git merge --no-ff dev 
//对Dev分支进行合并//默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考Benjamin Sandofsky的《Understanding the Git Workflow》http://sandofsky.com/blog/git-workflow.html。
四、 功能分支
接下来，一个个来看这三种"临时性分支"。
第一种是功能分支，它是为了开发某种特定功能，从Develop分支上面分出来的。开发完成后，要再并入Develop。
功能分支的名字，可以采用feature-*的形式命名。
创建一个功能分支：
　　git checkout -b feature-x develop
开发完成后，将功能分支合并到develop分支：
　　git checkout develop
　　git merge --no-ff feature-x
删除feature分支：
　　git branch -d feature-x
五、预发布分支release
第二种是预发布分支，它是指发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。
预发布分支是从Develop分支上面分出来的，预发布结束以后，必须合并进Develop和Master分支。它的命名，可以采用release-*的形式。
创建一个预发布分支：
　　git checkout -b release-1.2 develop
确认没有问题后，合并到master分支：
　　git checkout master
　　git merge --no-ff release-1.2
　　# 对合并生成的新节点，做一个标签
　　git tag -a 1.2
再合并到develop分支：
　　git checkout develop
　　git merge --no-ff release-1.2
最后，删除预发布分支：
　　git branch -d release-1.2
六、关于bug分支
创建一个修补bug分支：
　　git checkout -b fixbug-0.1 master
修补结束后，合并到master分支：
　　git checkout master
　　git merge --no-ff fixbug-0.1
　　git tag -a 0.1.1
再合并到develop分支：
　　git checkout develop
　　git merge --no-ff fixbug-0.1
最后，删除"修补bug分支"：
　　git branch -d fixbug-0.1