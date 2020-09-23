---
title: vue-cli 3.0 使用教程
date: 2019-05-18 12:48:00
updated: 2019-05-18 12:50:30
tags: 
- foreach
- JS基础增强
categories: 
- js

---
## 安装 cli
```bash
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```
## 创建一个新项目
```bash
vue create hello-world
```
<!--more-->
通过空格键和上下键选择配置 挺简单的
## CLI服务
在package.json使用默认预设的项目中看到的内容
```json
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build"
  }
}
```
您可以使用npm或Yarn调用这些脚本：
```bash
npm run serve
# OR
yarn serve
```
如果您有npx可用（应与最新版本的npm捆绑在一起），您还可以直接使用以下命令调用二进制文件：
```bash
npx vue-cli-service serve
```
## vue-cli-service serve
```
Usage: vue-cli-service serve [options]
Options:
  --open    服务器启动时打开浏览器
  --copy    将URL复制到服务器启动时的剪贴板 (直接到浏览器去粘贴就OK了 http://localhost:8080/)
  --mode    指定环境模式 (默认: development)
  --host    host 地址 (default: 0.0.0.0)
  --port    端口号 (default: 8080)
  --https   使用https (default: false)
```
## vue-cli-service build
```bash
Usage: vue-cli-service build [options] [entry|pattern]
Options:
  --mode        指定环境模式 (default: production)
  --dest        指定输出目录 (default: dist)
  --modern      构建两个版本的 js 包：一个面向支持现代浏览器的原生 ES2015+ 包，以及一个针对其他旧浏览器的包。
  --target      允许您以项目库或Web组件的形式在项目内部构建任何组件 app | lib | wc | wc-async (default: app) ???
  --name        lib或者web组件库的名称 (default: "name" in package.json or entry filename)
  --no-clean    在构建项目之前不要删除输出目录(dist)
  --report      生成report.html以帮助分析包内容
  --report-json 生成report.json来帮助分析包内容
  --watch       监听 - 当有改变时 自动重新打包~
```
## vue-cli-service inspect
使用 `vue-cli-service inspect`检查`Vue CLI`项目中的`webpack`配置。有关更多详细信息，请参阅检查[Webpack配置](https://cli.vuejs.org/guide/webpack.html#inspecting-the-project-s-webpack-config)
```bash
Usage: vue-cli-service inspect [options] [...paths]
Options:
  --mode    指定环境模式 (default: development)
```

**baseUrl:**
```bash
baseUrl: process.env.NODE_ENV === 'production'? '/static' : '/',
```

**打包后的输出目录:**
```bash
outputDir: '../static',
```
**静态资源打包地址:**
```bash
assetsDir: './assets',
```
保存时是不是用`eslint-loader` 来`lint` 代码
```bash
lintOnSave: true,
```
**page 配置:**
```json
    index: {
      // 入口文件
      entry: './src/main.js',
      // 模版文件
      template: 'public/index.html',
      // 输出文件名
      filename: 'index.html'
    }
  },
```

**是否使用包含运行时编译器的Vue内核版本:**
```json
runtimeCompiler: true,
```
**使用runtime-only 还是 in-browser compiller:**
```json
compiler: false,
```

**webpack配置:**
```bash
chainWebpack: () => {},
configureWebpack: () => {},
```
**vue-loader配置:**
https://vue-loader.vuejs.org/en/options.html
```json
vueLoader: {},
```

**生产环境的sourceMap 默认 true:**
```json
productionSourceMap: true,
```
**CSS 相关:**
```json
  css: {
    // 是否提取css生成单独的文件 默认 true
    extract: true,
    // 使用 CSS source maps?
    sourceMap: false,
    // loader配置
    loaderOptions: {},
    // 使用 css Modules
    modules: false
  },
```

**使用多线程否:**
```json
parallel: require('os').cpus().length > 1,
```

**使用 autoDLLPlugin:**
```json
dll: false,

```
https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-pwa
**pwa 相关:**
```json
pwa: {},
```
**dev server相关配置:**

如果使用package devsever 配置无法热更新
```
  devServer: {
    host: '172.19.5.222',
    port: 8071,
    https: false,
    hotOnly: true,
   proxy: {
      '/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  }
```

##目录结构
```
-project
  |-public 
  |-src  // 源码
  | |-projects // 模块目录
  | | |-p1 // 项目1
  | | | |-assets
  | | | |-components
  | | | |-views
  | | | |-App.vue
  | | | |-main.js
  | | | |-router.js
  | | | |-store.js
  | | |-p2 // 项目2
  | | | |．．．．．
  |-postcss.config.js // postcss 配置文件
  |-webpack.config.js // 通用webpack配置信息
  |-package.json
```

**webpack.config.js 中pages的配置:**
```json
  pages: {
    index: {
      entry: './src/projects/p1/main.js',
      template: 'public/index.html',
      filename: 'index.html'
    },
    about: {
      entry: './src/projects/p2/main.js',
      template: 'public/index.html',
      filename: 'about.html'
    }
  },
```