---
title: github pages + hexo 搭建个人博客
---

# 新建仓库

仓库名一定是  `用户名.github.io`

 {% img /image/github_newrepository.png "github_newrepository" %}

# 开启github page

 {% img /image/github_page.png "github_page" %}

默认开启

{% img /image/github_page2.png "github_page2" %}
选择主题

{% img /image/github_page3.png "github_page3" %}
`选择完成后即可通过上面的网址访问：https://skyeblack.github.io/`

# 修改网页

添加一个index.html网页

{% img /image/blog_html.png "blog_html" %}

{% img /image/blog_html2.png "blog_html2" %}

提交代码到github，刷新个人网址：`https://skyeblack.github.io/`

{% img /image/blog_html3.png "blog_html3" %}

# HEXO

HEXO官网： https://hexo.io/ 

## 全局安装hexo
`npm install -g hexo-cli`

{% img /image/hexo_install.png "hexo_install" %}

## 新建网站
`不要跟github.io放同一个仓库，把源码和网站分开`

{% img /image/hexo_new.png "hexo_new" %}
`hexo init [folder]`

 - folder 博客源码程序目录
    {% img /image/hexo_new2.png "hexo_new2" %}

  {% img /image/hexo_new3.png "hexo_new3" %}
## 启动服务
`hexo server`

{% img /image/hexo_server.png "hexo_server" %}

{% img /image/hexo_blog.png "hexo_blog" %}

## 安装依赖
`npm install hexo-deployer-git --save`
## 修改_config.yml
`配置到博客仓库目录去，不是源码仓库`

{% img /image/hexo_config.png "hexo_config" %}

`提交到另一项目用下面这种方式`

{% img /image/hexo_config2.png "hexo_config2" %}

 - repo 自己的博客路径
 - branch 博客的分支，由于github限制必须在master，所以不能改
## 提交
`源码用git提交，但是博客代码不要用github的提交`
`hexo d -g`
{% img /image/hexo_commit.png "hexo_commit" %}

{% img /image/hexo_commit2.png "hexo_commit2" %}