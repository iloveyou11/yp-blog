---
title: 从零搭建react环境
date: 2019-10-30
categories: 前端
author: yangpei
comments: true
---
目前react、vue项目都提供了对应的脚手架工具，让大家都能够轻易地创建项目，直接进行开发，省去了繁琐的打包配置问题。但是，这些脚手架生成的初始化项目中，到底做了哪些工作呢？这些webpack配置、性能优化等等知识都是前端工程师学习发展路上需要掌握的内容。因此我们是有必要学会不依靠脚手架工具亲自搭建vue、react项目环境的。

<!-- more -->

以下内容均来自大佬brickspert的[《从零搭建React全家桶框架教程》](https://github.com/brickspert/blog/issues/1)，详细内容点击原文链接进行阅读。

#### 1. webpack
`npm init`初始化项目，安装webpack`npm install --save-dev webpack@3`，新建`webpack.dev.config.js`

```js
const path = require('path')

module.exports = {
    entry: path.join(__dirname, './src/index.js'),
    output: {
        path: path.join(__dirname, './dist'),
        filename: 'bundle.js'
    }
}
```
在`src/index.js`随便写入一串js代码用来测试：`document.getElementById('app').innerHTML = "Webpack works"`

在`package.json`中写入打包命令：`"dev": "webpack --config webpack.dev.config.js"`，运行`npm run dev`观察是否打包成功。

在dist目录下新建`index.html`，引入`bundle.js`，打开`index.html`，观察js是否执行成功。

#### 2. babel
配置babel，可以用ES6、 ES7等来编写代码，Babel会把他们统统转为ES5

几个常用babel库：
- babel-core 调用Babel的API进行转码
- babel-loader
- babel-preset-es2015 用于解析 ES6
- babel-preset-react 用于解析 JSX
- babel-preset-stage-0 用于解析 ES7 提案

`babel-preset-state-0,babel-preset-state-1,babel-preset-state-2,babel-preset-state-3`有什么区别？每一级包含上一级的功能，比如 state-0包含state-1的功能，以此类推。state-0功能最全。

依次安装这些库：`npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react babel-preset-stage-0`

新建`.babelrc`:
```
  {
      "presets": [
        "es2015",
        "react",
        "stage-0"
      ],
      "plugins": []
    }
```
修改webpack.dev.config.js，增加babel-loader:

```js
/*src文件夹下面的以.js结尾的文件，要使用babel解析*/
/*cacheDirectory是用来缓存编译结果，下次编译加速*/
    module: {
        rules: [{
            test: /\.(js|jsx)$/,
            use: ['babel-loader?cacheDirectory=true'],
            include: path.join(__dirname, 'src')
        }]
    }
```
在js中就可以放心地写es6、es7、jsx的代码了，可以测试一下~

#### 3. react
安装依赖包`npm install --save react react-dom`

接下来编写测试文件：

src/Hello.jsx:
```jsx
import React, { Component } from 'react'

export default class Hello extends Component {
  render() {
    return (
      <div>hello</div>
    )
  }
}
```

src/index.jsx:
```jsx
import React from 'react';
import ReactDom from 'react-dom';
import Hello from './Hello.jsx'

ReactDom.render(
    <Hello />,
    document.getElementById('app')
);
```
注意此时要检查webpack配置，其中entry是否为`index.jsx`,babel编译的文件应该支持js和jsx……否则会打包失败。

#### 4. react-router
安装依赖包`npm install --save react-router-dom`，在src下新建`router/router.js`:

```jsx
import React from 'react';
import { BrowserRouter as Router, Route, Switch, Link } from 'react-router-dom';
import Home from '../pages/Home'
import User from '../pages/User'

const getRouter = () => (
  <Router>
    <div>
      <ul>
        <li><Link to="/">首页</Link></li>
        <li><Link to="/user">用户</Link></li>
      </ul>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/user" component={User} />
      </Switch>
    </div>
  </Router>
);

export default getRouter;
```
对应的两个组件简单写一下：

```jsx
// Home.jsx
import React, { Component } from 'react'

export default class Home extends Component {
  render() {
    return (
      <div>Home</div>
    )
  }
}

// User.jsx
import React, { Component } from 'react'

export default class User extends Component {
  render() {
    return (
      <div>User</div>
    )
  }
}

```
修改index.jsx:

```jsx
import React from 'react';
import ReactDom from 'react-dom';
import getRouter from './router/router';

ReactDom.render(
  getRouter(),
  document.getElementById('app')
);
```
注意：以上的代码import引入的时候都省略了后缀名.jsx，支持这种省略是需要在webpack中进行配置的，添加extensions即可，我们还可以配置alias别名：

```js
    resolve: {
        extensions: ['.js', '.jsx', '.json'],
        alias: {
            pages: path.join(__dirname, 'src/pages'),
            component: path.join(__dirname, 'src/component'),
            router: path.join(__dirname, 'src/router')
        }
    }
```
接下来打包测试页面，点击没有反应，为什么呢？因为file这种路径，不是我们想象中的路由那样的路径http://localhost:3000~我们需要配置一个简单的WEB服务器，有下面两种方法来实现：
- Nginx, Apache, IIS等配置启动一个简单的的WEB服务器
- 使用webpack-dev-server来配置启动WEB服务器

#### 5. webpack-dev-server
安装`npm install webpack-dev-server@2 --save-dev`，修改webpack配置：
```js
    devServer: {
        port: 3000,
        contentBase: path.join(__dirname, './dist'),
        historyApiFallback: true
    }
```
修改script命令：`"dev": "webpack-dev-server --config webpack.dev.config.js --color --progress"`，运行`npm run dev`即可。

**devServer配置项**
```
contentBase：URL的根目录。如果不设定的话，默认指向项目根目录
color（CLI only） console中打印彩色日志
historyApiFallback 任意的404响应都被替代为index.html。有什么用呢？你现在运行
npm start，然后打开浏览器，访问http://localhost:8080,然后点击Page1到链接http://localhost:8080/page1，
然后刷新页面试试。是不是发现刷新后404了。为什么？dist文件夹里面并没有page1.html,当然会404了，所以我们需要配置
historyApiFallback，让所有的404定位到index.html。
host 指定一个host,默认是localhost。如果你希望服务器外部可以访问，指定如下：host: "0.0.0.0"。比如你用手机通过IP访问。
hot 启用Webpack的模块热替换特性。
port 配置要监听的端口。默认就是我们现在使用的8080端口。
proxy 代理
progress（CLI only） 将编译进度输出到控制台
```

#### 6. 模块热更新（HMR）
模块热更新：当我们修改代码的时候，浏览器会自动刷新

首先，修改命令行添加hot参数：`"dev": "webpack-dev-server --config webpack.dev.config.js --color --progress --hot"`

src/index.js 增加module.hot.accept()

```js
import React from 'react';
import ReactDom from 'react-dom';

import getRouter from './router/router';

if (module.hot) {
    module.hot.accept();
}

ReactDom.render(
    getRouter(), 
    document.getElementById('app')
);
```
接下来，我们打开对应地址，修改jsx文件，发现页面也自动刷新了，有点惊喜。目前还有一种热更新的方法，但是较为麻烦一些，了解一下也可以：

```js
const webpack = require('webpack');

devServer: {
    hot: true
}

plugins:[
     new webpack.HotModuleReplacementPlugin()
]
```
**如何保存react中state的状态？**

虽然目前实现了热更新功能，But！上面的配置对react模块的支持不是很好，当模块热替换的时候，state会重置，这不是我们想要的。为了在react模块更新的同时，能保留state等页面中其他状态，我们需要引入react-hot-loader~

安装依赖：`npm install react-hot-loader@next --save-dev`

.babelrc 增加 react-hot-loader/babel

```js
{
  "presets": [
    "es2015",
    "react",
    "stage-0"
  ],
  "plugins": [
    "react-hot-loader/babel"
  ]
}
```
webpack.dev.config.js入口增加react-hot-loader/patch

```js
    entry: [
        'react-hot-loader/patch',
        path.join(__dirname, 'src/index.jsx')
    ]
```
src/index.js修改如下

```js
import React from 'react';
import ReactDom from 'react-dom';
import {AppContainer} from 'react-hot-loader';

import getRouter from './router/router';

/*初始化*/
renderWithHotReload(getRouter());

/*热更新*/
if (module.hot) {
    module.hot.accept('./router/router', () => {
        const getRouter = require('./router/router').default;
        renderWithHotReload(getRouter());
    });
}

function renderWithHotReload(RootElement) {
    ReactDom.render(
        <AppContainer>
            {RootElement}
        </AppContainer>,
        document.getElementById('app')
    )
}
```
现在，执行`npm run dev`。修改页面的时候，state不更新了。

#### 7. redux
接下来开始集成redux。
先安装redux：`npm install --save redux`

初始化目录结构：
```
cd src
mkdir redux
cd redux
mkdir actions
mkdir reducers
touch reducers.js
touch store.js
touch actions/counter.js
touch reducers/counter.js
```
先来写action创建函数。通过action创建函数，可以创建action，`src/redux/actions/counter.js`:

```js
export const INCREMENT = "counter/INCREMENT";
export const DECREMENT = "counter/DECREMENT";
export const RESET = "counter/RESET";

export function increment() {
    return {type: INCREMENT}
}

export function decrement() {
    return {type: DECREMENT}
}

export function reset() {
    return {type: RESET}
}
```
再来写reducer,reducer是一个纯函数，接收action和旧的state,生成新的state.`src/redux/reducers/counter.js`：

```js
import {INCREMENT, DECREMENT, RESET} from '../actions/counter';

const initState = {
    count: 0
};

export default function reducer(state = initState, action) {
    switch (action.type) {
        case INCREMENT:
            return {
                count: state.count + 1
            };
        case DECREMENT:
            return {
                count: state.count - 1
            };
        case RESET:
            return {count: 0};
        default:
            return state
    }
}
```
一个项目有很多的reducers,我们要把他们整合到一起,`src/redux/reducers.js`：

```js
import counter from './reducers/counter';

export default function combineReducers(state = {}, action) {
    return {
        counter: counter(state.counter, action)
    }
}
```
**核心思想**：reducer就是纯函数，接收state 和 action，然后返回一个新的 state。

**store** 就是把它们联系到一起的对象。store 有以下职责：
- 维持应用的 state；
- 提供 getState() 方法获取 state；
- 提供 dispatch(action) 触发reducers方法更新 state；
- 通过 subscribe(listener) 注册监听器;
- 通过 subscribe(listener) 返回的函数注销监听器。
`src/redux/store.js`：

```js
import {createStore} from 'redux';
import combineReducers from './reducers.js';

let store = createStore(combineReducers);

export default store;
```
集成好redux后，我们编写jsx文件，使用redux进行测试。

首先需要安装react-redux，`npm install --save react-redux`,新建一个页面测试一下：

```js
import React, {Component} from 'react';
import {increment, decrement, reset} from 'actions/counter';

import {connect} from 'react-redux';

class Counter extends Component {
    render() {
        return (
            <div>
                <div>当前计数为{this.props.counter.count}</div>
                <button onClick={() => this.props.increment()}>自增
                </button>
                <button onClick={() => this.props.decrement()}>自减
                </button>
                <button onClick={() => this.props.reset()}>重置
                </button>
            </div>
        )
    }
}

const mapStateToProps = (state) => {
    return {
        counter: state.counter
    }
};

const mapDispatchToProps = (dispatch) => {
    return {
        increment: () => {
            dispatch(increment())
        },
        decrement: () => {
            dispatch(decrement())
        },
        reset: () => {
            dispatch(reset())
        }
    }
};

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```
在`src/index.js`中引入store，修改部分如下：

```js
//新增
import {Provider} from 'react-redux';
import store from './redux/store';

function renderWithHotReload(RootElement) {
    ReactDom.render(
        <AppContainer>
        
            //新增Provider和store
            <Provider store={store}>
                {RootElement}
            </Provider>
            
        </AppContainer>,
        document.getElementById('app')
    )
}
```
#### 8. 异步action
举个栗子：
`src/redux/actions/userInfo.js`:
```js
export const GET_USER_INFO_REQUEST = "userInfo/GET_USER_INFO_REQUEST";
export const GET_USER_INFO_SUCCESS = "userInfo/GET_USER_INFO_SUCCESS";
export const GET_USER_INFO_FAIL = "userInfo/GET_USER_INFO_FAIL";

function getUserInfoRequest() {
    return {
        type: GET_USER_INFO_REQUEST
    }
}

function getUserInfoSuccess(userInfo) {
    return {
        type: GET_USER_INFO_SUCCESS,
        userInfo: userInfo
    }
}

function getUserInfoFail() {
    return {
        type: GET_USER_INFO_FAIL
    }
}

export function getUserInfo() {
    return function(dispatch) {
        dispatch(getUserInfoRequest());

        return fetch('http://localhost:8080/api/user.json')
            .then((response => {
                return response.json()
            }))
            .then((json) => {
                dispatch(getUserInfoSuccess(json))
            }).catch(
                () => {
                    dispatch(getUserInfoFail());
                }
            )
    }
}
```

为了让action创建函数除了返回action对象外，还可以返回函数，我们需要引用redux-thunk：`npm install --save redux-thunk`,简单的说，中间件就是action在到达reducer，先经过中间件处理。使用中间件来处理
函数形式的action，把他们转为标准的action给reducer。这是redux-thunk的作用。

我们来引入redux-thunk中间件:`src/redux/store.js`:

```js
import {createStore, applyMiddleware} from 'redux';
import thunkMiddleware from 'redux-thunk';
import combineReducers from './reducers.js';

let store = createStore(combineReducers, applyMiddleware(thunkMiddleware));

export default store;
```
可以编写一个组件验证一下，就不具体展开了。

值得注意的一点是，redux提供了一个combineReducers函数来合并reducer，上述的reducers.js可以通过combinReducers优化，不用我们自己合并，写法更加简单。

```js
import {combineReducers} from "redux";

import counter from 'reducers/counter';
import userInfo from 'reducers/userInfo';


export default combineReducers({
    counter,
    userInfo
});
```

#### 9. devtool优化
现在有一个问题，代码哪里写错了，浏览器报错只报在build.js第几行。`webpack.dev.config.js`增加`devtool:'inline-source-map'`即可。我们在srouce里面能看到我们写的代码，也能打断点调试。

#### 10. 编译css
如果需要引入css文件，需要安装css-loader、style-loader，`npm install css-loader style-loader --save-dev`，并在webpack配置中添加：

```js
{
   test: /\.css$/,
   use: ['style-loader', 'css-loader']
}
```
通过`import 'xx.css'`即可引入css代码了。如果像支持stylus、less、sass等css预处理语言，可以安装相关的依赖包，并在webpack中作相应的配置，这里不再赘述了。

#### 11. 编译图片
`npm install --save-dev url-loader file-loader`

`webpack.dev.config.js` rules增加

```js
{
    test: /\.(png|jpg|gif)$/,
    use: [{
        loader: 'url-loader',
        options: {
            limit: 8192
        }
    }]
}
```
options limit 8192意思是，小于等于8K的图片会被转成base64编码，直接插入HTML中，减少HTTP请求。

举个栗子：

```js
import React, {Component} from 'react';
import './Page1.css';
import image from './images/brickpsert.jpg';

export default class Page1 extends Component {
    render() {
        return (
            <div className="page-box">
                this is page1~
                <img src={image}/>
            </div>
        )
    }
}
```
**扩充**：webpack引入图片的几种方式？
- 在js 中创建图片来引入
- 在css中引入background('url')
- `<img src=" " alt=" ">`

1. 在js创建图片引入

```js
import './index.css';
import logo from './22.jpg';   //返回的结果 logo 是一个新的图片的地址
let image=new Image();  //创建新的图片对象
image.src=logo;      //就是一个普通的字符串
// 将new 的图片插入到body 的后面
document.body.appendChild(image);
```
2. 在css中引入
```css
//index.css
body{
    background-color: yellow;
}
div{
    width: 900px;
    height: 800px;
    background: url('../myImages/11.png');
}
```
3. 使用`<img src=" " alt=" ">`
- 安装loader:yarn add html-withimg-loader -D
- webpack.config.js添加配置

```js
  module:{    
        rules:[ //规则
            {
                test: /.css$/,
                use: ["style-loader","css-loader"]
            },
           {    //使用file-loader 可以图片 嵌入 到css中
            test: /\.(png|svg|jpg|gif)$/,
               use: ["file-loader"]
           },
           {
            test: /.html$/, //所有html结尾的文件添加此 loader 处理
            use: ["html-withimg-loader"]
           },
        ]
    },
```
html中引入图片即可： `<img src="../myImages/11.png" alt="">`

#### 12. 按需加载
打包完后，所有页面只生成了一个build.js,当我们首屏加载的时候，就会很慢。如果每个页面都打包了自己单独的JS，在进入自己页面的时候才加载对应的js，那首屏加载就会快很多。

在 react-router 2.0时代， 按需加载需要用到的最关键的一个函数，就是require.ensure()，它是按需加载能够实现的核心。在4.0版本，官方放弃了这种处理按需加载的方式，选择了一个更加简洁的处理方式。

安装依赖：`npm install bundle-loader --save-dev`，在router文件夹下新建bundle.js：

```js
import React, {Component} from 'react'

class Bundle extends Component {
    state = {
        // short for "module" but that's a keyword in js, so "mod"
        mod: null
    };

    componentWillMount() {
        this.load(this.props)
    }

    componentWillReceiveProps(nextProps) {
        if (nextProps.load !== this.props.load) {
            this.load(nextProps)
        }
    }

    load(props) {
        this.setState({
            mod: null
        });
        props.load((mod) => {
            this.setState({
                // handle both es imports and cjs
                mod: mod.default ? mod.default : mod
            })
        })
    }

    render() {
        return this.props.children(this.state.mod)
    }
}

export default Bundle;
```
改造路由`src/router/router.js`:

```js
import React from 'react';

import {BrowserRouter as Router, Route, Switch, Link} from 'react-router-dom';

import Bundle from './Bundle';

import Home from 'bundle-loader?lazy&name=home!pages/Home/Home';
import Page1 from 'bundle-loader?lazy&name=page1!pages/Page1/Page1';
import Counter from 'bundle-loader?lazy&name=counter!pages/Counter/Counter';
import UserInfo from 'bundle-loader?lazy&name=userInfo!pages/UserInfo/UserInfo';

const Loading = function () {
    return <div>Loading...</div>
};

const createComponent = (component) => (props) => (
    <Bundle load={component}>
        {
            (Component) => Component ? <Component {...props} /> : <Loading/>
        }
    </Bundle>
);

const getRouter = () => (
    <Router>
        <div>
            <ul>
                <li><Link to="/">首页</Link></li>
                <li><Link to="/page1">Page1</Link></li>
                <li><Link to="/counter">Counter</Link></li>
                <li><Link to="/userinfo">UserInfo</Link></li>
            </ul>
            <Switch>
                <Route exact path="/" component={createComponent(Home)}/>
                <Route path="/page1" component={createComponent(Page1)}/>
                <Route path="/counter" component={createComponent(Counter)}/>
                <Route path="/userinfo" component={createComponent(UserInfo)}/>
            </Switch>
        </div>
    </Router>
);

export default getRouter;
```
此时，进入新的页面，都会加载自己的JS。为了让输出的js名称更为清晰，可在`webpack.dev.config.js`加个`chunkFilename`:

```js
    output: {
        path: path.join(__dirname, './dist'),
        filename: 'bundle.js',
        chunkFilename: '[name].js'
    }
```
#### 13. 缓存
每次代码更新后，都需要更新客户端缓存，需要将打包生成的名字设置得不一样，可以在每次打包都用增加hash：

```js
    output: {
        path: path.join(__dirname, './dist'),
        filename: '[name].[hash].js',
        chunkFilename: '[name].[chunkhash].js'
    }
```
But！dist/index.html里面引用js名字还是bundle.js老名字，需要引入新的js文件。但是手动修改真的好吗？

#### 14. HtmlWebpackPlugin
这个插件，每次会自动把js插入到你的模板index.html里面去。安装依赖包`npm install html-webpack-plugin --save-dev`。新建模板src/index.html：

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<div id="app"></div>
</body>
</html>
```
修改`webpack.dev.config.js`，增加plugin：

```js
var HtmlWebpackPlugin = require('html-webpack-plugin');

    plugins: [new HtmlWebpackPlugin({
        filename: 'index.html',
        template: path.join(__dirname, 'src/index.html')
    })],
```
目前就可以自动将js插入html页面了。

#### 15. 提取公共代码
打包的代码中，有一些是公共库的代码不会改变的，但是，他们合并在bundle.js里面，每次项目发布，重新请求bundle.js的时候，相当于重新请求了，造成了资源浪费。

我们可以把react这些不会改变的公共库提取出来，用户缓存下来。从此以后，用户再也不用下载这些库了，无论是否发布项目。

`webpack.dev.config.js`:

```js
    var webpack = require('webpack');

    entry: {
        app: [
            'react-hot-loader/patch',
            path.join(__dirname, 'src/index.js')
        ],
        vendor: ['react', 'react-router-dom', 'redux', 'react-dom', 'react-redux']
    }
    
        /*plugins*/
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor'
        })
```
把react等库生成打包到vendor.hash.js里面去。

但是你现在可能发现编译生成的文件app.[hash].js和vendor.[hash].js生成的hash一样的，这里是个问题，因为你每次修改代码,都会导致vendor.[hash].js名字改变，那我们提取出来的意义也就没了。其实文档上写的很清楚：

```js
   output: {
        path: path.join(__dirname, './dist'),
        filename: '[name].[hash].js', //这里应该用chunkhash替换hash
        chunkFilename: '[name].[chunkhash].js'
    }
```
但是，如果用chunkhash，会报错。和webpack-dev-server --hot不兼容。等会我们配置正式版webpack.config.js的时候要解决这个问题。

#### 16. 生产坏境构建
> 开发环境(development)和生产环境(production)的构建目标差异很大。在开发环境中，我们需要具有强大的、具有实时重新加载(live reloading)或热模块替换(hot module replacement)能力的 source map 和 localhost server。而在生产环境中，我们的目标则转向于关注更小的 bundle，更轻量的 source map，以及更优化的资源，以改善加载时间。由于要遵循逻辑分离，我们通常建议为每个环境编写彼此独立的 webpack 配置。

新建`webpack.config.js`:

在`webpack.dev.config.js`的基础上先做以下几个修改~
- 先删除webpack-dev-server相关的东西~
- devtool的值改成cheap-module-source-map
- 刚才说的hash改成chunkhash

```js
const path = require('path');
var HtmlWebpackPlugin = require('html-webpack-plugin');
var webpack = require('webpack');

module.exports = {
    devtool: 'cheap-module-source-map',
    entry: {
        app: [
            path.join(__dirname, 'src/index.js')
        ],
        vendor: ['react', 'react-router-dom', 'redux', 'react-dom', 'react-redux']
    },
    output: {
        path: path.join(__dirname, './dist'),
        filename: '[name].[chunkhash].js',
        chunkFilename: '[name].[chunkhash].js'
    },
    module: {
        rules: [{
            test: /\.js$/,
            use: ['babel-loader'],
            include: path.join(__dirname, 'src')
        }, {
            test: /\.css$/,
            use: ['style-loader', 'css-loader']
        }, {
            test: /\.(png|jpg|gif)$/,
            use: [{
                loader: 'url-loader',
                options: {
                    limit: 8192
                }
            }]
        }]
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: path.join(__dirname, 'src/index.html')
        }),
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor'
        })
    ],

    resolve: {
        alias: {
            pages: path.join(__dirname, 'src/pages'),
            component: path.join(__dirname, 'src/component'),
            router: path.join(__dirname, 'src/router'),
            actions: path.join(__dirname, 'src/redux/actions'),
            reducers: path.join(__dirname, 'src/redux/reducers')
        }
    }
};
```
设置脚本：`"build":"webpack --config webpack.config.js"`即可打包为生产环境。

#### 17. 文件压缩
webpack使用UglifyJSPlugin来压缩生成的文件。`npm i --save-dev uglifyjs-webpack-plugin`

`webpack.config.js`:

```js
const UglifyJSPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  plugins: [
    new UglifyJSPlugin()
  ]
}
```
`npm run build`发现打包文件大小减小了好多。

#### 18. 指定环境
> 许多 library 将通过与 process.env.NODE_ENV 环境变量关联，以决定 library 中应该引用哪些内容。例如，当不处于生产环境中时，某些 library 为了使调试变得容易，可能会添加额外的日志记录(log)和测试(test)。其实，当使用 process.env.NODE_ENV === 'production' 时，一些 library 可能针对具体用户的环境进行代码优化，从而删除或添加一些重要代码。我们可以使用 webpack 内置的 DefinePlugin 为所有的依赖定义这个变量：

`webpack.config.js`:

```js
module.exports = {
  plugins: [
       new webpack.DefinePlugin({
          'process.env': {
              'NODE_ENV': JSON.stringify('production')
           }
       })
  ]
}
```
`npm run build`后发现vendor.[hash].js又变小了。

#### 19. 优化缓存
但是现在有一个问题。你随便修改代码一处，例如Home.js，随便改变个字，你发现home.xxx.js名字变化的同时，
vendor.xxx.js名字也变了。这不行啊。这和没拆分不是一样一样了吗？我们本意是vendor.xxx.js
名字永久不变，一直缓存在用户本地的。官方文档推荐了一个插件HashedModuleIdsPlugin:

```js
    plugins: [
        new webpack.HashedModuleIdsPlugin()
    ]
```
还要加一个runtime代码抽取:

```js
new webpack.optimize.CommonsChunkPlugin({
    name: 'runtime'
})
```
**注意**，引入顺序在这里很重要。CommonsChunkPlugin 的 'vendor' 实例，必须在 'runtime' 实例之前引入。

#### 20. public path
想象一个场景，我们的静态文件放在了单独的静态服务器上去了，那我们打包的时候，如何让静态文件的链接定位到静态服务器呢？

```js
    output: {
        publicPath : '/'
    }
```

#### 21. 打包优化
现在打开dist，是不是发现好多好多文件，每次打包后的文件在这里混合了？我们希望每次打包前自动清理下dist文件。`npm install clean-webpack-plugin --save-dev`
`webpack.config.js`:

```js
const CleanWebpackPlugin =require('clean-webpack-plugin');
plugins: [
    new CleanWebpackPlugin(['dist'])
]
```

#### 22. 抽取css
目前我们的css是直接打包进js里面的，我们希望能单独生成css文件。`npm install --save-dev extract-text-webpack-plugin`
```js
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
     new ExtractTextPlugin({
         filename: '[name].[contenthash:5].css',
         allChunks: true
     })
  ]
}
```

#### 23. axios和middleware
`npm install --save axios`

#### 24. 合并提取公共配置
想象一个场景，现在我想给webpack增加一个css modules依赖，你会发现，既要修改webpack.dev.config.js，又要修改webpack.config.js。

所以我们要把公共的配置文件提取出来。提取到webpack.common.config.js里面。webpack.dev.config.js和webpack.config.js写自己的特殊的配置。

这里我们需要用到webpack-merge来合并公共配置和单独的配置。`npm install --save-dev webpack-merge`

webpack.common.config.js:

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');

commonConfig = {
    entry: {
        app: [
            path.join(__dirname, 'src/index.js')
        ],
        vendor: ['react', 'react-router-dom', 'redux', 'react-dom', 'react-redux']
    },
    output: {
        path: path.join(__dirname, './dist'),
        filename: '[name].[chunkhash].js',
        chunkFilename: '[name].[chunkhash].js',
        publicPath: "/"
    },
    module: {
        rules: [{
            test: /\.js$/,
            use: ['babel-loader?cacheDirectory=true'],
            include: path.join(__dirname, 'src')
        }, {
            test: /\.(png|jpg|gif)$/,
            use: [{
                loader: 'url-loader',
                options: {
                    limit: 8192
                }
            }]
        }]
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: path.join(__dirname, 'src/index.html')
        }),
        new webpack.HashedModuleIdsPlugin(),
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor'
        }),
        new webpack.optimize.CommonsChunkPlugin({
            name: 'runtime'
        })
    ],

    resolve: {
        alias: {
            pages: path.join(__dirname, 'src/pages'),
            components: path.join(__dirname, 'src/components'),
            router: path.join(__dirname, 'src/router'),
            actions: path.join(__dirname, 'src/redux/actions'),
            reducers: path.join(__dirname, 'src/redux/reducers')
        }
    }
};

module.exports = commonConfig;
```
webpack.dev.config.js:

```js
const merge = require('webpack-merge');
const path = require('path');

const commonConfig = require('./webpack.common.config.js');

const devConfig = {
    devtool: 'inline-source-map',
    entry: {
        app: [
            'react-hot-loader/patch',
            path.join(__dirname, 'src/index.js')
        ]
    },
    output: {
        /*这里本来应该是[chunkhash]的，但是由于[chunkhash]和react-hot-loader不兼容。只能妥协*/
        filename: '[name].[hash].js'
    },
    module: {
        rules: [{
            test: /\.css$/,
            use: ["style-loader", "css-loader"]
        }]
    },
    devServer: {
        contentBase: path.join(__dirname, './dist'),
        historyApiFallback: true,
        host: '0.0.0.0',
    }
};

module.exports = merge({
    customizeArray(a, b, key) {
        /*entry.app不合并，全替换*/
        if (key === 'entry.app') {
            return b;
        }
        return undefined;
    }
})(commonConfig, devConfig);
```
webpack.config.js（也就是webpack.config.prod.js）：

```js
const merge = require('webpack-merge');

const webpack = require('webpack');
const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const ExtractTextPlugin = require("extract-text-webpack-plugin");

const commonConfig = require('./webpack.common.config.js');

const publicConfig = {
    devtool: 'cheap-module-source-map',
    module: {
        rules: [{
            test: /\.css$/,
            use: ExtractTextPlugin.extract({
                fallback: "style-loader",
                use: "css-loader"
            })
        }]
    },
    plugins: [
        new CleanWebpackPlugin(['dist/*.*']),
        new UglifyJSPlugin(),
        new webpack.DefinePlugin({
            'process.env': {
                'NODE_ENV': JSON.stringify('production')
            }
        }),
        new ExtractTextPlugin({
            filename: '[name].[contenthash:5].css',
            allChunks: true
        })
    ]

};

module.exports = merge(commonConfig, publicConfig);
```
#### 25. 增加404页面
- 新建pages/NotFound/NotFound组件。
- 修改router/router.js，增加404

```js
import NotFound from 'bundle-loader?lazy&name=notFound!pages/NotFound/NotFound';
<Route component={createComponent(NotFound)}/>
```
#### 26. polyfill

**babel-plugin-transform-runtime**
> 在转换 ES2015 语法为 ECMAScript 5 的语法时，babel 会需要一些辅助函数，例如 _extend。babel 默认会将这些辅助函数内联到每一个 js 文件里，这样文件多的时候，项目就会很大。
所以 babel 提供了 transform-runtime 来将这些辅助函数“搬”到一个单独的模块 babel-runtime 中，这样做能减小项目文件的大小。

`npm install --save-dev babel-plugin-transform-runtime`

修改`.babelrc`配置文件,增加配置:

.babelrc
```
     "plugins": [
       "transform-runtime"
     ]
```

**babel-polyfill**
> Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。
举例来说，ES6在Array对象上新增了Array.from方法。Babel就不会转码这个方法。如果想让这个方法运行，必须使用babel-polyfill，为当前环境提供一个垫片。

`npm install --save-dev babel-polyfill`

修改webpack两个配置文件:

webpack.common.config.js:

```js
     app: [
         "babel-polyfill",
         path.join(__dirname, 'src/index.js')
     ]
```
webpack.dev.config.js:

```js
     app: [
         'babel-polyfill',
         'react-hot-loader/patch',
         path.join(__dirname, 'src/index.js')
     ]
```
#### 27. 集成PostCSS
Autoprefixer这个插件,可以自动给css属性加浏览器前缀。postcss-cssnext 允许你使用未来的 CSS 特性（包括 autoprefixer）

当然，它有很多很多的插件可以用，你可以去官网详细了解。我们今天只用postcss-cssnext。（它包含了autoprefixer）

```
npm install -D  postcss-loader
npm install -D  postcss-cssnext
```
修改webpack配置文件,增加postcss-loader:
webpack.dev.config.js
```js
        rules: [{
            test: /\.(css|scss)$/,
            use: ["style-loader", "css-loader", "postcss-loader"]
        }]
```
webpack.config.js
```js
        rules: [{
            test: /\.css$/,
            use: ExtractTextPlugin.extract({
                fallback: "style-loader",
                use: ["css-loader", "postcss-loader"]
            })
        }]
```
新建postcss.config.js：

```js
module.exports = {
    plugins: {
        'postcss-cssnext': {}
    }
};
```
目前已完成了css自动添加前缀。

#### 28. redux 模块热替换配置
当修改reducer代码的时候，页面会整个刷新，而不是局部刷新。代码修改起来也简单,增加一段监听reducers变化，并替换的代码。
src/redux/store.js：

```js
if (module.hot) {
    module.hot.accept("./reducers", () => {
        const nextCombineReducers = require("./reducers").default;
        store.replaceReducer(nextCombineReducers);
    });
}
```
#### 29. 模拟AJAX数据Mock.js
Mock.js：拦截AJAX请求，返回需要的数据！`npm install mockjs --save-dev`

我们可以新建mock文件夹，在内部写模拟的接口数据user.js:

```js
 import Mock from 'mockjs';
 let Random = Mock.Random;
 Mock.mock('/api/user', {
     'name': '@cname',
     'intro': '@word(20)'
 });
```
可以在其他页面引入使用：
```js
if (MOCK) {
    require('mock/mock');
}
```

webpack.common.config.js配置mock目录别名：

```js
    resolve: {
        alias: {
            ...
            mock: path.join(__dirname, 'mock')
        }
    }

```
webpack.dev.config.js增加：

```js
   const webpack = require('webpack');
   plugins:[
        new webpack.DefinePlugin({
               MOCK: true
        })
    ]

```
这样，就只会在npm start 开发模式下，才会应用mock，如果你不想用，就把MOCK改成false就好了。
#### 30. 使用 CSS Modules
webpack.dev.config.js:

```js
module: {
        rules: [{
            test: /\.css$/,
            use: ["style-loader", "css-loader?modules&localIdentName=[local]-[hash:base64:5]", "postcss-loader"]
        }]
    }
```
webpack.config.js:

```js
module: {
    rules: [{
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
            fallback: "style-loader",
            use: ["css-loader?modules&localIdentName=[local]-[hash:base64:5]", "postcss-loader"]
        })
    }]
}
```
src/pages/Page1/Page1.js:

```js
import React, {Component} from 'react';
import image from './images/brickpsert.jpg';

//看这里
import style from './Page1.css';

export default class Page1 extends Component {
    render() {
        return (
            <div className={style.box}>
                this is page1~
                <img src={image}/>
            </div>
        )
    }
}
```
#### 31. json-server
json-server和Mock.js一样，都是用来模拟接口数据的。
json-server功能更强大，支持分页，排序，筛选等等。

具体使用方法参考官方文档~