# README

## 文档运行流程

本地编辑 =》提交git =》webhook =》 服务器上git pull，make html =》nginx展示

## sphinx安装配置
1. 安装采用默认即可，安装完成后`sphinx-quickstart`一路回车
2. 让支持markdown，`pip install recommonmark`，修改conf.py,extension里面增加'recommonmark'

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
              <a href="{{ meta['github_url'] }}" class="fa fa-github"> {{ _('Edit on GitHub') }}</a>
            {% else %}
              <a href="https://{{ github_host|default("github.com") }}/{{ theme_github_user }}/{{ theme_github_repo }}/{{ theme_vcs_pageview_mode|default("blob") }}/{{ theme_github_version }}/{{ conf_py_path }}{{ pagename }}{{ suffix }}" class="fa fa-github"> {{ _('Edit on GitHub') }}</a>
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