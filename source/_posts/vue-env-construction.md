---
title: VUE环境搭建
---

# 安装node.js

下载安装：[https://nodejs.org/zh-cn/](https://nodejs.org/zh-cn/)
cmd查看版本：`node -v`
# 更新npm
可选操作
更新npm：`npm i npm to update`
cmd查看版本：`npm -v`
# 安装cnpm
可选操作
cmd安装：`npm install -g cnpm –registry=http://registry.npm.taobao.org`
# 安装vue-cli脚手架构建工具
cmd安装：`npm install -g vue-cli`
卸载vue-cli：`npm uninstall vue-cli -g`
`安装vue-cli3.0(若已全局安装vue-cli 1.x,2.x，需卸载)：npm install -g @vue/cli 
vue3.0要求nodejs>=8.9`
# 用vue-cli构建项目
vue2.x：vue init webpack your-project-name
vue3.x：vue create your-project-name
# 安装项目所需的依赖
`npm install`或者`cnpm instsall`
安装完成之后，会在项目目录文件夹中多出一个node_modules文件夹，这里边就是我们项目需要的依赖包资源。
# 运行项目
v2.x: `npm run dev`
v3.x: `npm run serve`
# VUE调试工具
[https://github.com/vuejs/vue-devtools](https://github.com/vuejs/vue-devtools)

 {% img /image/vue_tool.png "vue_tool" %}

 {% img /image/vue_tool2.png "vue_tool2" %}

