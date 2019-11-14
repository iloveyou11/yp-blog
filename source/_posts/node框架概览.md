---
title: 浅析node
date: 2019-09-26
categories: Node
author: yangpei
tags:
  - Node
  - koa
  - webpack
comments: true
cover_picture: /images/banner.jpg
---

#### 导言
Koa2和Koa1的区别，和express的区别？
1. 异步流程控制
Express 采用 callback 来处理异步，Koa v1 采用 generator，Koa v2 采用 async/await。
generator 和 async/await 使用同步的写法来处理异步，明显好于 callback 和 promise，async/await 在语义化上又要比 generator 更强。
2. 错误处理
Express 使用 callback 捕获异常，对于深层次的异常捕获不了，Koa 使用 try catch，能更好地解决异常捕获。

#### koa

##### 快速搭建项目
首先需要安装koa，新建js文件即可运行web服务：

```
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```
或者可以直接使用koa提供的脚手架创建项目。

##### koa洋葱模型
经典的洋葱模型图：
![洋葱模型.png](https://i.loli.net/2019/09/26/ZH2TNdJCfyxn9LW.png)
1. 一个请求到一旦到后端，就开始接触洋葱的最外层。
2. 遇到一个next()，就进入下一层。不过值得提醒的是，异步函数的next(),与同步函数的next(),不是在同一个空间的，我们可以假想一个“异步空间栈”，后入先出。
3. 什么时候到洋葱中心？就是遇到的第一个没有next的中间件,或者遇到一个中间件报错，就会把这个中间件当成中心，因为遇到错误了，不会再继续往里面走。这个时候，就开始向洋葱的外层开始走了。如果第一个中间件就没有next，直接返回的。那么就不存在洋葱模型。
4. 一层一层外面走的时候，就先走位所有的同步中间件，再依次走“异步空间栈”的中间件。
##### koa常用中间件

【koa-views】
koa-views对需要进行视图模板渲染的应用是个不可缺少的中间件，支持ejs, nunjucks等众多模板引擎。

【koa-json】
JSON pretty-printed response middleware

【koa-router】
koa-router提供了全面的路由功能，比如类似Express的app.get/post/put的写法，URL命名参数、路由命名、支持加载多个中间件、嵌套路由等

【koa-bodyparser】
POST数据处理,koa.js并没有内置Request Body的解析器，当我们需要解析请求体时需要加载额外的中间件，官方提供的koa-bodyparser是个很不错的选择
支持x-www-form-urlencoded, application/json等格式的请求体，但不支持form-data的请求体，需要借助 formidable 这个库
也可以直接使用 koa-body 或 koa-better-body,import koaBody from 'koa-body';

【koa-session】
HTTP是无状态协议，为了保持用户状态，我们一般使用Session会话，koa-session提供了这样的功能，既支持将会话信息存储在本地Cookie，也支持存储在如Redis, MongoDB这样的外部存储设备。

【koa-jwt】
随着网站前后端分离方案的流行，越来越多的网站从Session Base转为使用Token Base，JWT(Json Web Tokens)作为一个开放的标准被很多网站采用，koa-jwt这个中间件使用JWT认证HTTP请求。

【@koa/cors】
解决跨域问题

【koa-helmet】
网络安全得到越来越多的重视，helmet 通过增加如Strict-Transport-Security, X-Frame-Options, X-Frame-Options等HTTP头提高Express应用程序的安全性，koa-helmet为koa程序提供了类似的功能，参考Node.js安全清单。

【koa-static】
Node.js除了处理动态请求，也可以用作类似Nginx的静态文件服务，在本地开发时特别方便
可用于加载前端文件或后端Fake数据，可结合 koa-compress 和 koa-mount 使用。

【koa-compose】
合并中间件，简化app.use的写法

【koa-compress】
当响应体比较大时，我们一般会启用类似Gzip的压缩技术减少传输内容，koa-compress提供了这样的功能，可根据需要进行灵活的配置。

【koa-logger】
koa-logger提供了输出请求日志的功能，包括请求的url、状态码、响应时间、响应体大小等信息，对于调试和跟踪应用程序特别有帮助，koa-bunyan-logger 提供了更丰富的功能。

【koa-onerror】
捕获错误
const onerror=require('koa-onerror')
onerror(app)

还有很多，可根据项目需求自行探索……

##### request和req的区别
```
ctx.req——Node 的 request 对象.
ctx.res——Node 的 response 对象.
ctx.request——koa 的 Request 对象.
ctx.response——koa 的 Response 对象.
```
#### koa项目的webpack配置

配置webpack进行打包（支持es6语法、统一打包为1个文件）

首先需要安装的开发依赖：

```
    "devDependencies": {
        "@babel/core": "^7.6.2",
        "@babel/node": "^7.6.2",
        "@babel/preset-env": "^7.6.2",
        "babel-loader": "^8.0.6",
        "clean-webpack-plugin": "^3.0.0",
        "cross-env": "^6.0.0",
        "nodemon": "^1.19.2",
        "webpack": "^4.41.0",
        "webpack-cli": "^3.3.9",
        "webpack-node-externals": "^1.7.2"
    }
```
webpack.config.js 配置：
```
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const {
    CleanWebpackPlugin
} = require('clean-webpack-plugin')

const config = {
    target: 'node',
    mode: 'development',
    entry: {
        server: path.join(__dirname, './app.js')
    },
    output: {
        filename: '[name].bundle.js',
        path: path.join(__dirname, './dist')
    },
    devtool: 'eval-source-map',
    module: {
        rules: [{
            test: /\.(js|jsx)$/,
            use: {
                loader: 'babel-loader'
            },
            exclude: [path.join(__dirname, './node_modules')]
        }]
    },
    externals: [nodeExternals()],
    plugins: [
        new CleanWebpackPlugin()
    ],
    node: {
        console: true,
        global: true,
        process: true,
        Buffer: true,
        __filename: true,
        __dirname: true,
        setImmediate: true,
        path: true
    }
}

module.exports = config
```

.babelrc 配置：
```
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}
```

运行`npx webpack`即可打包，也可配置`"build": "webpack"`，运行`npm run build`

运行`nodemon --exec babel-node app.js`即可实现开发热加载，也可配置`"watch": "nodemon --exec babel-node app.js"`，运行`npm run watch`

##### 调试webpack
`npx node --inspect-brk .\node_modules\.bin\webpack --inline --progress`,
打开`chrome://inspect/#devices`，点击`inspect`，即可进行调试。

同样可配置`"webpack:debug": "npx node --inspect-brk .\\node_modules\\.bin\\webpack --inline --progress"`，运行`npm run webpack:debug`即可。

##### vscode调试
1. 添加配置
2. 可选择“node”、“nodemon”……等调试脚本
3. 修改配置内容（文件启动入口等）
举个栗子：

```
  "configurations": [{
    "type": "node",
    "request": "launch",
    "name": "nodemon",
    // 命令所在入口
    "runtimeExecutable":
    "${workspaceFolder}/node_modules/.bin/nodemon",
    // 调试文件
    "program": "${workspaceFolder}/app.js",
    "restart": true,
    "console": "integratedTerminal",
    "internalConsoleOptions": "neverOpen",
    // 使用babel-node支持ES6语法
    "runtimeArgs": ["--exec", "babel-node"]
  }]
```
4. 在vscode中打断点，进行断点调试即可

##### 如何更新npm包
使用`npm-check-updates`，`npm install -g npm-check-updates`全局安装后，使用`ncu`检查当前`package.json`中是否存在可更新的npm包，运行`ncu -u`即可更新包版本。

##### 整合中间件
使用`koa-compose`

```
import compose from 'koa-compose'

const middlewares = compose([
    helmet(),
    koaBody(),
    cors(),
    bodyparser({
        enableTypes: ['json', 'form', 'text']
    }),
    json(),
    mystatic(__dirname + '/public'),
    views(__dirname + '/views', {
        extension: 'ejs'
    }),
    router()
])

app.use(middlewares)
```
**tips**：以上为ES6语法，启动时应采用`babel-node`，如运行`nodemon --exec babel-node app.js`即可

##### webpack config分离与合并
按照开发环境和生产环境可以配置出不同的 webpack config 文件，因此需要将其分离更加组织管理。

我们可以建立config文件，存放`webpack.config.base.js` `webpack.config.dev.js` `webpack.config.prod.js`三个文件，需要使用到`webpack-merge`插件，下面逐一介绍：

【webpack.config.base.js】

DefinePlugin中定义一些全局常量，以便代码中使用，如下，添加了process.env常量
```
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const webpack = require('webpack')

const config = {
    target: 'node',
    entry: {
        server: path.join(__dirname, './app.js')
    },
    output: {
        filename: '[name].bundle.js',
        path: path.join(__dirname, './dist')
    },

    module: {
        rules: [{
            test: /\.(js|jsx)$/,
            use: {
                loader: 'babel-loader'
            },
            exclude: [path.join(__dirname, './node_modules')]
        }]
    },
    externals: [nodeExternals()],
    plugins: [
        new CleanWebpackPlugin(),
        // 新增
        // 创建process.env常量
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: (process.env.NODE_ENV === 'production' || process.env.NODE_ENV === 'prod') ? "'production'" : "'development'"
            }
        })
    ],
    node: {
        console: true,
        global: true,
        process: true,
        Buffer: true,
        __filename: true,
        __dirname: true,
        setImmediate: true,
        path: true
    }
}

module.exports = config
```

【webpack.config.dev.js】

```
const webpackMerge = require('webpack-merge')
const baseConfig = require('./webpack.config.base')

const config = webpackMerge(baseConfig, {
    devtool: 'eval-source-map',
    mode: 'development',
    stats: {
        children: false
    }
})

module.exports = config
```
【webpack.config.prod.js】

terser-webpack-plugin是进行js压缩的插件，详见[TerserWebpackPlugin](https://webpack.js.org/plugins/terser-webpack-plugin/#root)，其中有详细的配置

```
const webpackMerge = require('webpack-merge')
const baseConfig = require('./webpack.config.base')
const TerserPlugin = require('terser-webpack-plugin');

const config = webpackMerge(baseConfig, {
    devtool: 'eval-source-map',
    mode: 'production',
    stats: {
        children: false,
        warnings: false
    },
    optimization: {
        minimize: true,
        minimizer: [new TerserPlugin()],
    },
})

module.exports = config
```
##### 项目可优化部分
1、提取公共模块，使用 [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)

```
module.exports = {
  //...
  optimization: {
    splitChunks: {
      cacheGroups: {
        commons: {
          name: 'commons',
          chunks: 'initial',
          minChunks: 2
        }
      }
    }
  }
};
```
使用`cross-env NODE_ENV=production webpack --config config/webpack.config.prod.js`进行打包，或者在package.json中配置命令亦可。

2、另外在koa项目中，不建议在routes路由内部写大量的业务逻辑代码，而是将其提炼为单独controller，使用函数控制路由内部实现。

3、在app.js中，判断当前环境为生产环境时，进行中间件压缩，需要使用到`koa-compress`模块。

```
import compress from 'koa-compress'
const isDev = process.env.NODE_ENV === 'production' ? false : true
// 生产环境下压缩中间件
if (!isDev) {
    app.use(compress())
}
```
[github demo地址（koa的webpack配置）](https://github.com/iloveyou11/koa-webpack)

#### NodeMailer邮件服务
我们可以使用nodemailer模块实现发送邮件的服务。
`npm install nodemailer`进行安装即可。

基础示例：

```
'use strict';
const nodemailer = require('nodemailer');
async function main() {
    let transporter = nodemailer.createTransport({
        host: 'smtp.qq.com',
        port: 587,
        secure: false, //
        auth: {
            user: 'xxx@qq.com', //发送人邮箱
            pass: 'xxx' //授权码
        }
    });
    let info = await transporter.sendMail({
        from: '"name" <xx@qq.com>', // 发送人姓名和邮箱
        to: 'xx@qq.com', //接收人邮箱
        subject: 'Hello',
        text: '你爱我吗',
        html: '<b>你爱我吗？</b>'
    });
    console.log('Message sent: %s', info.messageId);
}
main().catch(console.error);
```
