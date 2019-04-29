# git学习笔记
工作区--》暂存区--》本地版本库--》远程版本库

## 配置
使用普通用户创建

```
git config --global user.name "lypeng"
git config --global user.email "893371810@qq.com"

// 区分大小写（需要在git项目根目录执行）
git config core.ignorecase false
```

创建ssh-key
执行`ssh-keygen -t rsa -C "893371810@qq.com"`，一路回车，然后将id_rsa.pub复制到github中

远程操作
先创建ssh key之后再来远程操作，采用ssh方式克隆，而不是https，这样就不用每次都输入密码了
```
git remote -v //查看远程信息是不是ssh方式的，如果不是删了，重建
git clone git@github.com:lypeng29/baike_spider
git push
```

## 常规操作
```
git status //查看当前git状态 
git add a.txt //添加文件
git rm b.txt //删除文件
git commit -m 'some message' //提交到本地仓库
git push origin master //推送到远程仓库
```

## 版本回退

### 执行了git add，没有执行git commit
`git reset filename` 或者批量：`git reset *`

### 执行了git add，并且执行git commit
```
回退所有内容到上一个版本　　　　
git reset HEAD^

回退a.py这个文件的版本到上一个版本
git reset HEAD^ a.py

向前回退到第3个版本
git reset --soft HEAD~3

将本地的状态回退到和远程的一样
git reset --hard origin/master

回退到某个版本
git reset 057d

回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit
git reset HEAD
```

### 远程的回退（执行了commit与push）
```
git push origin HEAD --force
//或者
git reset --hard HEAD~1
git push --force
```

## 文件删除
```
//从工作区删除文件 -f强制删除
rm a.txt 与 git rm a.txt -f 等价

//如果删错了，使用checkout从版本库还原 
git checkout -- a.txt

//如果确要删除，commit即可 
git commit -m "del a.txt" 
git commit -a //提交所有，包括删除的~

git rm a.txt --cached //从暂存区删除文件
```

## 远程操作
```
//添加远程主机名为origin，也可以为其他 
git remote add origin git@github.com:ypengshop/tpfcms.git

//写错了可以删除远程主机名
git remote rm origin

//查看远程信息
git remote -v

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

强制用远程文件覆盖本地文件
git fetch --all
git reset --hard origin/master

//关于创建tag
git tag v1.0
git tag -a v1.0 -m "first version" //普通带注释的tag
git tag -d v1.0 //删除
git push origin --tag	//tag信息，需单独推送
```

## 关于分支

```
git branch //查看分支列表
git checkout -b dev master //创建dev分支
git push origin local_branch:remote_branch 
//推送，如果remote_branch不存在则会自动创建分支。
类似，git push origin :remote_branch，local_branch留空的话则是删除远程remote_branch分支。

git checkout master //切换到master分支
git merge --no-ff dev //对Dev分支进行合并
```

> 默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考Benjamin Sandofsky的《Understanding the Git Workflow》http://sandofsky.com/blog/git-workflow.html。


功能分支

为了开发某种特定功能，从Develop分支上面分出来的。开发完成后，要再并入Develop。
功能分支的名字，可以采用feature-* 的形式命名。

```
// 创建一个功能分支：
git checkout -b feature-x develop

// 开发完成后，将功能分支合并到develop分支：
git checkout develop
git merge --no-ff feature-x

// 删除feature分支：
git branch -d feature-x
```

预发布分支release

指发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。
预发布分支是从Develop分支上面分出来的，预发布结束以后，必须合并进Develop和Master分支。它的命名，可以采用release-* 的形式。

```
// 创建一个预发布分支：
git checkout -b release-1.2 develop

// 确认没有问题后，合并到master分支：
git checkout master
git merge --no-ff release-1.2

// 对合并生成的新节点，做一个标签
git tag -a 1.2

// 再合并到develop分支：
git checkout develop
git merge --no-ff release-1.2

// 最后，删除预发布分支：
git branch -d release-1.2
```

bug分支
```
// 创建一个修补bug分支：
git checkout -b fixbug-0.1 master

// 修补结束后，合并到master分支：
git checkout master
git merge --no-ff fixbug-0.1
git tag -a 0.1.1

// 再合并到develop分支：
git checkout develop
git merge --no-ff fixbug-0.1

// 最后，删除"修补bug分支"：
git branch -d fixbug-0.1
```

## git 修改已提交的注释
原文地址：`https://www.cnblogs.com/zhangjianbin/p/10126190.html`

在git中，其commit提供了一个--amend参数，可以修改最后一次提交的信息
修改最后一次提交注释 git commit --amend
然后在出来的编辑界面，直接编辑注释的信息,保存退出

git rebase -i HEAD~3
git使用amend选项提供了最后一次commit的反悔。但是对于历史提交呢，就必须使用rebase了。
修改push后的历史提交注释

这个命令出来之后，会出来三行东东：
```
pick:*******

pick:*******

pick:*******  
```
如果你要修改哪个，就把那行的pick改成edit，然后保存退出。
git commit --amend
修改注释

git rebase --continue
合并注释

git log
查看提交记录

提交修改后的注释
git pull origin master
git push origin master