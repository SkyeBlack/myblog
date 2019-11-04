---
title: VUE+ELECTRON打包桌面程序
---

# 下载electron资源
`git clone https://github.com/electron/electron-quick-start`
`注：npm install electron失败，提示删除重试，可能是node版本（过高）的问题`
# 安装依赖
在需要打包的项目中安装
## 安装electron
`npm install electron --save-dev`
{% img /image/vue_electron_install.png "vue_electron_install" %}

## 安装打包工具
`npm install electron-packager --save-dev`
{% img /image/vue_electron_packager.png "vue_electron_packager" %}

# main.js
将electron/electron-quick-start项目下的main.js拷贝到需要打包的项目的根目录下（vue2放到build目录下），不要改名
注意修改文件中loadFile的文件路径，例如mainWindow.loadFile('./dist/index.html')
{% img /image/vue_electron_main.png "vue_electron_main" %}

# 修改package.json
 - 添加"main": "main.js"
 - 添加启动命令："start": "electron ."
 - 用`npm start`启动
{% img /image/vue_electron_start.png "vue_electron_start" %}
 - 可将"start"修改为其他名字，如”electron_dev"，用`npm run electron_dev`启动
{% img /image/vue_electron_dev.png "vue_electron_dev" %}
 - 可以在启动命令中添加`npm run build`，则执行该命令时将先执行一次build

   {% img /image/vue_electron_build.png "vue_electron_build" %}
# 项目启动
按照步骤4中的命令启动，必须保证存在dist目录
# 白屏
 - vue2
{% img /image/vue_electron_white2.png "vue_electron_white2" %}
 - vue3
{% img /image/vue_electron_white3.png "vue_electron_white3" %}
# 打包
## 修改package.json
`"electron_build":"electron-packager ./dist/ --out ./exe/ --platform=win32 --arch=x64 --icon=./public/header.ico --overwrite"`
electron-packager <sourcedir> <appname> --out <targetdir> --platform=<platform> --arch=<arch> --icon=headericodir [optional flags...]
{% img /image/vue_electron_packager2.png "vue_electron_packager2" %}

## 添加文件
 - 将项目中的`main.js`和`package.js`复制到dist目录下
 - 修改main.js的`mainWindow.loadFile('./index.html')`
## 运行
运行打包命令`npm run electron_build`
{% img /image/vue_electron_packager3.png "vue_electron_packager3" %}

{% img /image/vue_electron_packager4.png "vue_electron_packager4" %}

{% img /image/vue_electron_packager5.png "vue_electron_packager5" %}

