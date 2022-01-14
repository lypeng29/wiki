# README

发现一个比sphinx更简单的，docsify！不用生成html

参见官网地址：`https://docsify.js.org/`

## 几个命令搞定！

 * npm全局安装客户端 `npm i docsify-cli -g`
 * 初始化，自动生成 `docsify init ./docs`
 * 启动服务 `docsify serve docs `

然后就可以访问：`http://localhost:3000` 实时查看！

## index.html配置

```
  <script>
    window.$docsify = {
      name: '风轻云淡',
      basePath: '/docs/',
      repo: '', //文档git地址
      loadSidebar: true 
    }
  </script>
```

根目录创建_sidebar.md，内容如下

```
* [README](README)
* [git](dir/git)
```

新建dir目录，创建git.md文件，就可以开始写了！

添加basePath后，文档结构如下：

```
├─other.php
├─index.html
└─docs
    ├─README.md
    └─dir
        └─git.md
```

可以在index.html同级，创建其他php或HTML文件，互不干涉！