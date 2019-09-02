---
title: iframe和定时器的思考
date: 2019-07-25
categories: JavaScript
author: yangpei
tags:
  - JavaScript
comments: true
cover_picture: /images/banner.jpg
---

#### 关于 iframe 的思考

**好处**

1. 方便插入第三方内容，但是由于发送了额外的 http 请求，会一定程度上影响整体页面加载，例如各种广告

- iframe 能够原封不动的把嵌入的网页展现出来，如果有多个网页引用 iframe，那么你只需要修改 iframe 的内容，就可以实现调用的每一个页面内容的更改，方便快捷。

<!-- more -->

- 网页如果为了统一风格，头部和版本都是一样的，就可以写成一个页面，用 iframe 来嵌套，可以增加代码的可重用。但是随着现在前端使用的 MVVM 框架越来越流行，组件化开发方式也成为了大势所趋，越来越多的项目采用组件化编写的方式进行开发和维护，也逐渐抛弃了原有的 iframe。如果遇到加载缓慢的第三方内容如图标和广告，这些问题还是可以由 iframe 来解决。

2. 缓存网页，主要是在网络不好的时候
3. 用于 postMessage 通信
4. 安全沙箱，避免污染环境
5. 流量作弊，嵌入一个不展现的 iframe 页面，反作弊很难发现

**缺点**

1. 页面样式调试麻烦，出现多个滚动条；
2. 浏览器的后退按钮失效；
3. 过多会增加服务器的 HTTP 请求；
4. 小型的移动设备无法完全显示框架；
5. 产生多个页面，不易管理；
6. 不容易打印；
7. 代码复杂，无法被一些搜索引擎解读。

**总结**
1：iframe 会阻塞主页面的 Onload 事件；2：iframe 和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。 iframe，目前是一个过时的技术，不建议在项目中使用。

> 运维性网站或继承性开发的网站，可以使用 iframe;
> 销售内，官网、展示性网站等建议不使用 iframe;
> 标准的网页设计是不使用 iframe 的。

**iframe 与父组件之间的传值**

- 主页面传值到 iframe：
  let iFrame = document.getElementById('myframe')
  iFrame.onload = function(){
  iFrame.contentWindow.postMessage('MessageFromIndex1','\*');
  }
- iframe 接收传值：
  function receiveMessageFromIndex ( event ) {
  console.log( 'receiveMessageFromIndex', event )
  }
  window.addEventListener("message", receiveMessageFromIndex, false);
- iframe 再向主页面传值：
  parent.postMessage( {msg: 'MessageFromIframePage'}, '\*');
- 主页面接收传值：
  function receiveMessageFromIframePage (event) {
  console.log('receiveMessageFromIframePage', event)
  }
  window.addEventListener("message", receiveMessageFromIframePage, false);

<!-- 目前在项目开发中，为了修改老旧的项目（html 页面和 jquery 操作 dom 的形式），融入新 cms 的表单（vue 组件式开发），暂且使用了 iframe 嵌入。同时也遇到了许多问题：

1. iframe 的嵌入存在跨域的问题，如果子域与主域不是同域，则会产生跨域的问题。因此会产生调试不方便的问题。可以采用 fiddler 代理的方案进行本地调试，将在线的 js 代理到本地的 js，本地 localhost 调试的 iframe 静态页面映射到线上域名，实现预期的效果之后，可替换线上的文件。
2. 开始在调整 iframe 时，会存在 iframe 的位置摆放问题。起初是采用 table 形式嵌入 iframe，动态调整参数可以解决
   虽然能解决 iframe 摆放位置的问题，但是也存在新的问题：
   表单数据时而可以点击，时而不能点击，后来去掉了 table 布局，删除了 iframe 中多余的部分，改为纯净的页面，直接通过 iframe 标签嵌入可以解决此问题。
3. iframe 的高度动态调整方案：
- 创建了 setInterval 定时器，在页面内容没有变化时，还是在不断地执行定时器，此处会消耗页面的性能；
- 高度变化不连贯，存在延迟的现象；
- 高度逐渐减小时，会出现卡顿变化的现象，一点点地在减小，不知道具体的原因； -->

#### 定时器性能

探讨 setInterval 和 setTimeout 的解决方案：
无论是 setTimeout 还是 setInterval 都逃不过执行延迟，跳帧的问题。为什么呢？原因是事件环中 JS Stack 过于繁忙的原因，当排队轮到定时器的 callback 执行的时候，早已超时。还有一个原因是定时器本身的 callback 操作过于繁重，甚至有 async 的操作，以至于无法预估运行时间，从而设定时间。因此，在设置事件为 500ms 时，不一定是 500ms 时恰好执行。

**需要使用到定时器的场景：**
1、轮询：当要时刻监视页面内容高度变化时，可以采用轮询方式，实时查询页面高度，可实现动态调整
2、轮播：鼠标不在轮播图上时，创建定时器进行轮播，鼠标移开时，销毁定时器
3、时钟：制作动态时钟时，需要使用定时器
4、函数防抖：延迟函数的执行，减少执行的次数，从而提高性能

**创建和销毁定时器问题：**
在 vue 或 react 项目开发中，有时会在页面初始化时创建定时器，记得在页面销毁时删除定时器，防止内存泄漏；
