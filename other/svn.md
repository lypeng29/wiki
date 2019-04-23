# svn操作
	
## 撤销所有的svn add操作
svn revert --recursive *

## 撤销svn delete操作
svn revert workerman/README.md



## 忽略文件与目录
1. cd到temp所在的目录下：
2. 注意忽略的文件或文件夹不能在版本控制器中，否则无法忽略，使用下面命令删除
svn delete --keep-local uploads //保留本地文件夹的内容，删除版本控制器中的内容

3. svn propedit svn:ignore . //输入 uploads,保存退出
输入目录名或者文件名即可，即每个目录下面都存在一个影藏的ignore，而git在根目录下只存在一个ignore

4. svn ci -m '忽略uploads目录' //提交

2018-06-06
备注也可以在根目录建立一个.svnignore文件，里面写上忽略的文件与文件夹，如下：
```
node_modules
.project  
Thumbs.db  
.vscode
.git
*.jpg
```
然后执行 svn propset svn:ignore -R -F .svnignore .完成全局忽略，这个写法也会忽略子目录中匹配的文件
> 来自：`https://www.jianshu.com/p/c02d8b335495`

## 解决树冲突
```
svn resolve --accept working .user.ini
svn revert .user.ini
```

## 修改版本库根地址
```
svn info //可以查看到根地址
svn sw --relocate svn://localhost/s2 svn://localhost/news //修改根地址
```