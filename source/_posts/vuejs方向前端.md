---
title: 前端面试题整理
date: 2019-07-24
categories: 面试题
tags:
  - 面试题
  - vue
comments: true
cover_picture: /images/banner.jpg
---

### vuejs
特点：声明式渲染、组件化构建

### 开发技能树
**1. 组件通讯（prop、Event、Global Event Bus、Vuex）**
**2. 插槽（普通插槽、作用域插槽）**
**3. 过渡动画（过渡类名、JavaScript钩子）**
**4. DOM操作**
    1）操作css
    2）配合原生js库使用（如cube-ui对better-scroll的封装、element-ui对popper.js的封装）
**5. 组件封装（就近管理、高复用性、分层设计、灵活扩展）**
**6. keep-alive（来回切换避免重复渲染）**
    activated、deactivate
**7. 内存泄漏**
    产生的原因（未清理的定时器、未清理的全局注册自定义事件、未清理的全局注册DOM事件）
**8. 错误调试**
    常见错误：
    1）深层对象数据访问问题（使用v-if判断）
    2）对计算属性赋值
    3）对prop直接修改（使用emit派发事件）
    4）使用未注册的组件（局部注册、全局注册）
    调试工具：
    1）chrome工具、vue-devtools、vConsole
**9. 性能优化**
    1）数据定义（不一定全部定义在data中，可以将一些数据挂载到实例上）
    2）按需加载（只下载首屏渲染的资源，可以使用异步组件、异步路由来实现）
    3）预渲染（减少白屏时间）
    4）后编译
**10. vuejs渲染原理**
    new Vue——init——$mount——compile——render——vnode——patch——DOM
    虚拟DOM（可以实现跨端）
    编译过程涉及AST树（抽象语法树）(https://segmentfault.com/a/1190000016231512?utm_source=tag-newest)
**11. 组件化实现原理**
**12. 响应式实现原理**

### 源码学习推荐
源码推荐学习：推荐学习jquery、vue、react的设计理念和源码架构

### 前端工程化
**1. 脚手架（vue-cli3.0、插件化机制、webpack配置）**
    调整webpack配置最简单的方式是在vue.config.js中的configureWebpack选项提供一个对象
**2. webpack（module、entry、output、loader、plugin）**
    [output]：线上运行时需要通过设置的public path指向CDN地址
    [loader]：用于对模块的源代码进行转化
        可以手写loader
        常见loader：babel-loader、style-loader、css-loader、less-loader、file-loader、url-loader、vue-loader
    [plugin]：插件比loader更加强大，可以帮助用户直接触及到编译过程
        打包优化、
**3. 开发&部署**
    需求阶段——开发阶段——联调阶段——上线阶段（推荐增量发布，hash化资源 / 或灰度发布）

### 广度知识必知必会
**1. HTTP相关[图解HTTP]**
    常见状态码、浏览器缓存、抓包工具（fiddler、charles）
**2. 跨域**
    常见跨域解决方案 (CORS JSONP……)
**3. 性能优化[高性能网站建设进阶指南]**
    性能监测lighthouse、数据埋点、雅虎军规
**4. web安全**
    xss、csrf、https
**5. 数据结构&算法**
    栈/队列/树/图
    排序/递归
    算法设计技巧
**6. 浏览器渲染原理**
    DOM tree + CSSOM tree ——> render tree (layout + painting)
**7. 正则表达式[精通正则表达式]**
    基本语法、匹配原理、使用技巧
**8. 设计模式**
    工厂模式、订阅发布模式、适配器模式……
**9. 后端语言**
    java、php、nodejs……

### 前端架构
**1. 技术选型**
    1）业务应该选择什么技术栈？
        PC toB toC 移动 重交互 游戏 偏显示 电商……
    2）选用的技术栈是否靠谱？
        持续维护、star、download、社区生态、测试完成度、大公司使用情况、issue解决情况……
**2. 老项目重构**
**3. 个人成长经验**