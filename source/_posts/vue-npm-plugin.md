---
title: npm插件开发(基于vue-cli3.x)
---

# 创建项目
`vue create your-project-name`
# 调整目录
 1. 将src修改为examples存放示例
 2. 新建packages存放组件
{% img /image/vue_npm_path.png "vue_npm_path" %}
# 配置项目以支持新的目录结构
我们通过上一步的目录改造后，会遇到两个问题。
 1. src 目录更名为 examples ，导致项目无法运行
 2. 新增 packages 目录，该目录未加入 webpack 编译
`注：cli3 提供一个可选的 vue.config.js 配置文件。如果这个文件存在则他会被自动加载，所有的对项目和webpack的配置，都在这个文件中。`
## 重新配置入口，修改配置中的 pages 选项
新版 Vue CLI 支持使用 vue.config.js 中的 pages 选项构建一个多页面的应用。
这里使用 pages 修改入口到 examples

```bash
module.exports = {
    // 修改 src 为 examples
    pages: {
        index: {
            entry: "examples/main.js",
            template: "public/index.html",
            filename: "index.html"
        }
    },
};
```
 - 支持对 packages 目录的处理，修改配置中的 chainWebpack 选项
packages 是我们新增的一个目录，默认是不被 webpack 处理的，所以需要添加配置对该目录的支持。
chainWebpack 是一个函数，会接收一个基于 webpack-chain 的 ChainableConfig 实例。允许对内部的 webpack 配置进行更细粒度的修改。

```bash
// 扩展 webpack 配置，使 packages 加入编译
    chainWebpack: config => {
        config.module
            .rule('js')
            .include
            .add('/packages/')
            .end()
            .use('babel')
            .loader('babel-loader')
            .tap(options => {
                // 修改它的选项...
                return options
            })
    }

```
# 编写组件
{% img /image/vue_npm_component.png "vue_npm_component" %}
如图，menu就是一个组件。每个组件都要新建一个文件夹，然后添加index.js以及src文件夹。
{% img /image/vue_npm_component2.png "vue_npm_component2" %}

# 导出组件
{% img /image/vue_npm_component3.png "vue_npm_component3" %}
## 将packages中的所有组件导出
在packages文件夹下新建index.js

```bash
// 导入组件
import tedMenu from "./menu/index.js";

// 存储组件列表-数组方式
// const components = [tedMenu]

// 存储组件列表-对象方式
const components = {
  tedMenu,
  tedMenuItem: tedMenu.Item,
  tedSubMenu: tedMenu.Sub
};

// 定义 install 方法，接收 Vue 作为参数。如果使用 use 注册插件，则所有的组件都将被注册
const install = function (Vue) {
  // 判断是否安装
  if (install.installed) return;

  // 遍历注册全局组件-数组方式
  // components.forEach(component => {
  //   Vue.component(component.name, component)
  // });

  // 遍历注册全局组件-对象方式
  Object.keys(components).forEach(key => {
    Vue.component(key, components[key]);
  });
};

// 判断是否是直接引入文件
if (typeof window !== "undefined" && window.Vue) {
  install(window.Vue);
}

export default {
  // 导出的对象必须具有 install，才能被 Vue.use() 方法安装
  install,
  // 以下是具体的组件列表
  components: components
};
```
# 在项目中引入组件
{% img /image/vue_npm_component4.png "vue_npm_component4" %}
使用 Vue.use() 全局注册后，即可在任意页面直接使用了，而不需另外引入。当然也可以按需引入。
{% img /image/vue_npm_component5.png "vue_npm_component5" %}

`npm run serve`执行后即可看到效果。

# 配置编译
## package.js 中新增一条编译为库的命令
在库模式中，Vue是外置的，这意味着即使在代码中引入了 Vue，打包后的文件也是不包含Vue的。
以下我们在 scripts 中新增一条命令 npm run lib
 - --target : 构建目标，默认为应用模式。这里修改为 lib 启用库模式。
 - --dest : 输出目录，默认 dist 。这里我们改成 lib
 - [entry] : 最后一个参数为入口文件，默认为 src/App.vue 。这里我们指定编译 packages/ 组件库目录。

```bash
"script": {
	"lib": "vue-cli-service build --target lib --name tedview --dest lib packages/index.js"
}
```
## 执行编译库命令
`npm run lib`
{% img /image/vue_npm_lib.png "vue_npm_lib" %}

## 配置 package.json 文件中发布到 npm 的字段
{% img /image/vue_npm_lib2.png "vue_npm_lib2" %}
# 发布
# 包名类似，拒绝发布
把包名中的标点符号去掉并与现有的包进行比较，相同则不允许发布
例如：react-native已经存在，那么诸如
 - reactnative
 - react_native
 - react.native
就不可以再发布了
`注：包版本不一定要增量，可以是任意不曾使用过的版本号`
## 使用作用域
如果因为你起的包名与现有的包名太相近而被阻止发布这个包，那么找到一个独一无二包名最简单方法就是使用自己的作用域。你可以使用@+你的npm用户名加在包名前面将包划到你的npm账户作用域下。比如，我的npm用户名是skyeblack，所以我的作用域是@skyeblack。
所以在package.json文件里把

```bash
"name": "ted-view-vue",
```
修改成

```bash
"name": "@skyeblack/ted-view-vue",
```
然后我要发布这个包。被划了作用域的包默认是私有的，所以要通过`—access=public`让它变为公有的包：

```bash
npm publish --accesss=public
```
## 添加.npmignore 文件，设置忽略发布文件
我们发布到 npm 中，只有编译后的 lib 目录、package.json、README.md才是需要被发布的。所以我们需要设置忽略目录和文件。
和 .gitignore 的语法一样，具体需要提交什么文件，看各自的实际情况。
{% img /image/vue_npm_publish.png "vue_npm_publish" %}

## 登录到 npm
首先需要到 npm 上注册一个账号，注册过程略。
如果配置了淘宝镜像，先设置回npm镜像：
{% img /image/vue_npm_publish2.png "vue_npm_publish2" %}
然后在终端执行登录命令，输入用户名、密码、邮箱即可登录。
`npm login`

## 发布到 npm
执行发布命令，发布组件到 npm
`npm publish`
执行后显示发布成功即可在npm官网上找到自己的包，如果没有发布成功，有可能是包名称和社区其他包重复了，改个名字即可。
被划了作用域的包默认是私有的，所以要通过`—access=public`让它变为公有的包
{% img /image/vue_npm_publish3.png "vue_npm_publish3" %}

# 删除上传的包
注意:根据规范，只有在发包的24小时内才允许撤销发布的包（ unpublish is only allowed with versions published in the last 24 hours）
终端打开包项目，输入`npm unpublish --force`：
{% img /image/vue_npm_force.png "vue_npm_force" %}

## 删除指定版本的包
`npm unpublish @skyeblack/ted-view-vue@1.1.1`
注：删除后的包名，若再次上传依然会被视为包已存在，需要修改成不同的版本号
# 引用
{% img /image/vue_npm_use.png "vue_npm_use" %}

{% img /image/vue_npm_use2.png "vue_npm_use2" %}

{% img /image/vue_npm_use3.png "vue_npm_use3" %}

{% img /image/vue_npm_use4.png "vue_npm_use4" %}

# 其他
## 初始化npm项目
实际是生成package.json文件
{% img /image/vue_npm_init.png "vue_npm_init" %}
```bash
{
  "name": "menu-bar",
  "version": "1.0.0",
  "description": "adaptive menu",
  "main": "index.js",
  "scripts": {
    "test": "start test"
  },
  "keywords": [
    "menu",
    "hear",
    "bar"
  ],
  "author": "SkyeBlack",
  "license": "ISC"
}
```
# 参考
 - [https://www.jianshu.com/p/72d303449abc](https://www.jianshu.com/p/72d303449abc)
 - [https://www.jianshu.com/p/aa109584983f](https://www.jianshu.com/p/aa109584983f)