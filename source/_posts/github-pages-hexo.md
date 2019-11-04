---
title: github pages + hexo 搭建个人博客
---

# 新建仓库

仓库名一定是  `用户名.github.io`
![github_newrepository](../image/github_newrepository.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)

# 开启github page
![github_page](../image/github_page.png)
默认开启
![github_page2](../image/github_page2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
选择主题
![github_page3](../image/github_page3.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
`选择完成后即可通过上面的网址访问：https://skyeblack.github.io/`

# 修改网页
添加一个index.html网页
![blog_html](../image/blog_html.png)
![bolg_html2](../image/blog_html2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
提交代码到github，刷新个人网址：`https://skyeblack.github.io/`
![blog_html3](../image/blog_html3.png)

# HEXO
## 全局安装hexo
`npm install -g hexo-cli`
![hexo_install](../image/hexo_install.png)

## 新建网站
`不要跟github.io放同一个仓库，把源码和网站分开`
![hexo_new](../image/hexo_new.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
`hexo init [folder]`

 - folder 博客源码程序目录
![hexo_new2](../image/hexo_new2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
![hexo_new3](../image/hexo_new3.png)
## 启动服务
`hexo server`
![hexo_server](../image/hexo_server.png)
![hexo_blog](../image/hexo_blog.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)

## 安装依赖
`npm install hexo-deployer-git --save`
## 修改_config.yml
`配置到博客仓库目录去，不是源码仓库`
![hexo_config](../image/hexo_config.png)

`提交到另一项目用下面这种方式`

![hexo_config](../image/hexo_config2.png)

 - repo 自己的博客路径
 - branch 博客的分支，由于github限制必须在master，所以不能改
## 提交
`源码用git提交，但是博客代码不要用github的提交`
`hexo d -g`
![hexo_commit](../image/hexo_commit.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
![hexo_commit2](../image/hexo_commit2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlZF9za3k=,size_16,color_FFFFFF,t_70)
