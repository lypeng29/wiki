# README

发现一个比sphinx更简单的，docsify！不用生成html

参见官网地址：`https://docsify.js.org/`

几个命令搞定！

```
npm i docsify-cli -g
docsify init ./docs
docsify serve docs 
```

index.html配置

```
  <script>
    window.$docsify = {
      name: '风轻云淡',
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
