# README

网上大多数都是托管到`https://readthedocs.org/`,不想注册，还是放自己服务器了~
网上的教程不能用了，自己折腾了两天，终于好了，预览地址：http://wiki.lypeng.com

## 文档运行流程

正常顺序
本地编辑 => 提交git => webhook => 服务器上自动`git pull && make html` => 访问网址预览

我这里暂时没用webhook，因为需要个PHP文件去执行shell命令，php还需要开启shell_exec函数权限！
目前用的定时任务，定时执行shell脚本去更新，`git pull && make html`

## sphinx安装配置
0. 对于python环境，安装个anaconda就好了！
1. 安装采用`pip install sphinx`即可，anaconda默认带的版本比较低，使用`pip install sphinx --upgrade`升级版本， 安装完成后运行`sphinx-quickstart`一路回车，创建项目目录结构。
2. 使sphinx支持markdown，需要安装扩展`pip install recommonmark`，然后修改conf.py，在extension里面增加'recommonmark'
3. 编辑index.rst文件，里面写你的文件名称，不要后缀
4. Windows下，执行`./make.bat html`生成HTML文件，Linux下，`make html`

## 关于右上角的edit on github

> 如果你采用rst格式，则每个文件头部定义`:github_url: https://github.com/xxx/xx/xx.rst`即可！
> 而我用的md格式，没办法定义github_url 也不想每个页面都写一个github_url，于是乎折腾开始，如下：

打开主题目录：我本地的在：`D:\Anaconda3\Lib\site-packages\sphinx_rtd_theme\`
服务器上的位置: `/usr/local/anaconda3/lib/python3.6/site-packages/sphinx_rtd_theme/`

1. 修改里面的breadcrumbs.html
```
    {% block breadcrumbs_aside %}
      <li class="wy-breadcrumbs-aside">
        {% if hasdoc(pagename) %}
            {% if check_meta and 'github_url' in meta %}
              <!-- User defined GitHub URL -->
              <a href="{{ meta['github_url'] }}" class="fa fa-github" target="_blank"> {{ _('Edit on GitHub') }}</a>
            {% else %}
              <a href="https://{{ github_host|default("github.com") }}/{{ theme_github_user }}/{{ theme_github_repo }}/{{ theme_vcs_pageview_mode|default("blob") }}/{{ theme_github_version }}/{{ conf_py_path }}{{ pagename }}{{ suffix }}" class="fa fa-github" target="_blank"> {{ _('Edit on GitHub') }}</a>
            {% endif %}
        {% endif %}
      </li>
    {% endblock %}
```

2. 修改theme.conf
```
[options]
...
github_user = lypeng29
github_repo = wiki
github_version = master
```

3. 修改文档根目录配置conf.py
注：conf.py里面的配置会覆盖掉theme.conf的配置，以后有新的直接复制下conf.py修改配置就好了~
```
html_theme = 'sphinx_rtd_theme'
html_theme_options = {
	'github_user': 'lypeng29new',
	'github_repo': 'wiki',
	'github_version': 'master'
}
```

```
@TODO
0.数据结构
1.php底层知识运行原理与扩展开发
2.单点登录sso
3.session共享实战 分布式存储，session 图片文件
4.vagrant与VMware与virtualbox docker
5.laravel phalcon
6.kafka ElasticSearch
7.跨境电商 magento shoptify wish amazon joom
8.前沿技术 区块链、ai、python、图像识别、大数据、集群架构
```