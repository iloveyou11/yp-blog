---
title: 深入理解vue
date: 2019-09-24
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

#### vue基础
##### vue-cli脚手架

```javascript
npm install -g @vue/cli
或
yarn global add @vue/cli

创建项目
vue create [name]

使用图形化界面
vue ui
```
调整 webpack 配置最简单的方式就是在 vue.config.js 中的 configureWebpack 选项提供一个对象.
##### 基础指令
```javascript
文本插值是最基本的形式，用双大括号
v-text、v-html
监听事件指令 v-on，简写为@click
属性绑定指令 v-bind，简写为
遍历指令 v-for
表单输入绑定指令 v-model（3个修饰符.lazy .trim .number）
条件渲染v-if 和 v-show的区别、v-else-if 和 v-else 不是必须的
```
**v-if和v-show的区别**
- v-if是真实的条件渲染，当进行条件切换时，它会销毁和重建条件块的内容，并且它支持<template>语法；
- v-show的条件切换时基于css的display属性，所以不会销毁和重建条件块的内容；
- 当你频繁需要切换条件时，推荐使用v-show；否则使用v-if；

##### class绑定

```javascript
// 1.对象语法
v-bind:class="{ active: isActive, 'text-danger': hasError }
data: {  
isActive: true,
  hasError: false
}
// 2.数组语法
<div v-bind:class="[activeClass, errorClass]"></div>
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
// 3.Style绑定
v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"

```

##### 事件修饰符

```javascript
vue还为v-on提供了事件修饰符
  .stop 阻止事件继续传播
  .prevent 提交的事件不再阻止页面
  .capture 添加事件监听器时使用事件捕获模式
  .self 只当在event.target是当前元素自身时触发处理函数
  .once 点击事件将只触发一次
  .passive 滚动事件的默认行为将会立即触发

1   <div v-on:click.prevent="greet">1</div>//等价于event.preventDefault()
2   <div v-on:click.stop="greet">2</div>//等价于event.stopPropagation()
3   <div v-on:click.capture="greet">3</div>//等价于事件回调函数采用捕获阶段监听事件
4   <div v-on:click.self="greet">4</div>//等价于event.target

```
##### nextTick
在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。
需要注意的是，在 created 和 mounted 阶段，如果需要操作渲染后的试图，也要使用 nextTick 方法。
![nextTick.png](https://i.loli.net/2019/09/26/oZsLnaCRl3qQJkM.png)
```javascript
// 这样可以，nextTick里面的代码会在DOM更新后执行
Vue.nextTick(function(){
    console.log(vm.$el.textContent) //可以得到'changed'
})

// 注意 mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted
mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}

```
##### vue-router
[Vue2.0之vue-router](http://www.imooc.com/article/70052)

[vue-router 60分钟快速入门](https://www.cnblogs.com/keepfool/p/5690366.html)

安装vue-router

几种实现方式动态路由匹配、嵌套路由、编程式路由、命名路由
命名视图、重定向与别名、路由组件传参

**导航护卫**

**全局前置守卫**当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中。

**全局解析守卫**这和 router.beforeEach 类似，区别是在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用。

**全局后置钩子**
transition 可以定义路由过渡动画

##### vuex
`npm install vuex –save`

1. **state**：定义全局状态属性
this.$store.state.showFooter
2. **getters**：和vue计算属性computed一样，来实时监听state值的变化(最新状态)，并把它也仍进Vuex.Store里面
3. **mutations**：具体的用法就是给里面的方法传入参数state或额外的参数,然后利用vue的双向数据驱动进行值的改变，同样的定义好之后也把这个mutations扔进Vuex.Store里面
this.$store.commit('show')
4. **actions**：通常用于异步操作或是mutations的封装，可以包含任意异步操作，这里面的方法是用来异步触发mutations里面的方法，actions里面自定义的函数接收一个context参数和要变化的形参，context与store实例具有相同的方法和属性，所以它可以执行context.commit(' '),然后也不要忘了把它也扔进Vuex.Store里面
this.$store.dispatch('showFooter')

举个栗子：
```javascript
import Vue from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex);
 const state={   //要设置的全局访问的state对象
     showFooter: true,
     changableNum:0
     //要设置的初始属性值
   };
const getters = {   //实时监听state值的变化(最新状态)
    isShow(state) {  //承载变化的showFooter的值
       return state.showFooter
    },
    getChangedNum(){  //承载变化的changebleNum的值
       return state.changableNum
    }
};
const mutations = {
    show(state) {   //自定义改变state初始值的方法，这里面的参数除了state之外还可以再传额外的参数(变量或对象);
        state.showFooter = true;
    },
    hide(state) {  //同上
        state.showFooter = false;
    },
    newNum(state,sum){ //同上，这里面的参数除了state之外还传了需要增加的值sum
       state.changableNum+=sum;
    }
};
 const actions = {
    hideFooter(context) {  //自定义触发mutations里函数的方法，context与store 实例具有相同方法和属性
        context.commit('hide');
    },
    showFooter(context) {  //同上注释
        context.commit('show');
    },
    getNewNum(context,num){   //同上注释，num为要变化的形参
        context.commit('newNum',num)
     }
};
  const store = new Vuex.Store({
       state,
       getters,
       mutations,
       actions
});
export default store;

```
modules 模块化 以及 组件中引入 mapGetters、mapActions 和 mapStates的使用
```javascript
import {mapState,mapGetters,mapActions} from 'vuex';
computed:{
    	...mapState({  //这里的...是超引用，ES6的语法，意思是state里有多少属性值我可以在这里放多少属性值
         isShow:state=>state.footerStatus.showFooter //注意这些与上面的区别就是state.footerStatus,
      }),
...mapActions('collection',[ //collection是指modules文件夹下的collection.js
          'invokePushItems'  //collection.js文件中的actions里的方法，在上面的@click中执行并传入实参
      ])，
...mapGetters('collection',{ //用mapGetters来获取collection.js里面的getters
            arrList:'renderCollects'
      })
}
```

##### vue生命周期
vue生命周期:Vue 实例从创建到销毁的过程，就是生命周期。从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、销毁等一系列过程，称之为 Vue 的生命周期。它的生命周期中有多个事件钩子，让我们在控制整个Vue实例的过程时更容易形成好的逻辑。

总共分为 8 个阶段beforeCreate（创建前） created（创建后） beforeMount（载入前） mounted（载入后） beforeUpdate（更新前）, updated（更新后） beforeDestroy（销毁前） destroyed（销毁后）。
- 创建前/后：在 beforeCreate 阶段，vue 实例的挂载元素 el 还没有。
- 载入前/后：在 beforeMount 阶段，vue 实例的$el 和 data 都初始化了，但还是挂载之前为虚拟的 dom 节点，data.message 还未替换。在 mounted 阶段，vue 实例挂载完成，data.message 成功渲染。
- 更新前/后：当 data 变化时，会触发 beforeUpdate 和 updated 方法。
- 销毁前/后：在执行 destroy 方法后，对 data 的改变不会再触发周期函数，说明此时 vue 实例已经解除了事件监听以及和 dom 的绑定，但是 dom 结构依然存在。
- 另外还有 keep-alive 独有的生命周期，分别为 activated 和 deactivated 。用 keep-alive 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 deactivated 钩子函数，命中缓存渲染后会执行 activated钩子函数。

**应用场景？**

- beforeCreate 可以在此时加一些loading效果，在created时进行移除
- created 需要异步请求数据的方法可以在此时执行，完成数据的初始化
- mounted 当需要操作dom的时候执行，可以配合$.nextTick 使用进行单一事件对数据的更新后更新dom
- updated 当数据更新需要做统一业务处理的时候使用

##### vue调试方法
1. [在 VS Code 中调试](https://cn.vuejs.org/v2/cookbook/debugging-in-vscode.html)

vscode安装Debugger for Chrome。在vue.config.js中设置source-map：

```javascript
module.exports = {
  configureWebpack: {
    devtool: 'source-map'
  }
}
```
点击“调试”>“添加配置”，生成launch.json，注意url的端口要与项目运行的端口一致，点击“开始调试”即可。

```javascript
{
  "version": "0.2.0",
  "configurations": [{
    "type": "chrome",
    "request": "launch",
    "name": "vuejs: chrome",
    "url": "http://localhost:8081",
    "webRoot": "${workspaceFolder}/src",
    "breakOnLoad": true,
    "sourceMapPathOverrides": {
      "webpack:///./src/*": "${webRoot}/*"
    }
  }]
}
```

2. [Vue DevTools](https://cn.vuejs.org/v2/cookbook/debugging-in-vscode.html#Vue-Devtools)

直接在chrome中下载此插件即可。


**对于Vue-cli创建的工程化项目，哪些方式可以调试应用？**
- 使用vue官方推荐的devTools进行调试（官方推荐的dev-Tools是最方便去查看vue的状态管理、vue变量的工具）
- 在webpack配置代码中打开source-map，插入debugger，使用chrome的调试窗口（但是要注意这种方式，不方便查看vuex的状态变化，vuex的commit事件无法监听）
- 使用alert, console.log，JSON.stringfy打印相关的日志（这个是最大众，最简单，也是最普通的一种方式了）

#### vue原理
##### 组件化思想
**组件化**是将页面的功能模块进行拆分、封装，组件代码包含了组件所有的功能代码与样式。
**组件化的作用**是复用、高可维护性。
组件化不局限于前端代码，而是一种设计思想。

##### vue响应式原理

[官方解释](https://cn.vuejs.org/v2/guide/reactivity.html)

如何追踪数据变化？

当你把一个普通的 JavaScript 对象传入 Vue 实例作为 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。

这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在属性被访问和修改时通知变更。

以下是官方的流程图:
<img alt="vue响应式原理" src="https://i.loli.net/2019/09/26/Bi9arClmjRevOoY.jpg" width="60%"/>
由上图可知，每个组件实例都对应一个 watcher 实例，它会在组件渲染的过程中把“接触”过的数据属性记录为依赖（借用getter实现）。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

##### vue双向绑定原理
vue实现数据双向绑定主要是：采用**数据劫持结合发布者-订阅者模式**的方式，通过 **Object.defineProperty（）** 来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调。

当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

vue的数据双向绑定 将MVVM作为数据绑定的入口，整合Observer，Compile和Watcher三者，通过Observer来监听自己的model的数据变化，通过Compile来解析编译模板指令（vue中是用来解析），最终利用watcher搭起observer和Compile之间的通信桥梁，达到数据变化 —>视图更新；视图交互变化（input）—>数据model变更双向绑定效果。
<img alt="vue双向绑定" src="https://i.loli.net/2019/09/26/jaxvf63mpghXLR5.png" width="60%"/>
<img alt="vue双向绑定" src="https://i.loli.net/2019/09/26/WSqI6amD3BVx5G8.png" width="60%"/>
veu2.0使用Object.defineProperty存在一些缺陷，vue3.0改为使用proxy实现双向数据绑定。

**如何正确地更新页面列表list中第2个元素？**

由于 JavaScript 的限制，Vue 不能检测以下数组的变动：
- 当你利用索引直接设置一个数组项时，例如：vm.items[indexOfItem] = newValue
- 当你修改数组的长度时，例如：vm.items.length = newLength

所以，不能采用在Vue的实例中，this.lists[1] = data，或是在数据请求的回调中，使用vm.lists[1] = data。

解决方案：
- 在数据请求的回调中，使用$set方法，Vue.$set(vm.lists, 1, data) [对应API](https://cn.vuejs.org/v2/guide/list.html#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
- new一个新的数组listsNew，然后把第二个元素改成data，然后把this.lists = listsNew，赋值给数组。

#### 其他知识点
##### hash模式 和 history模式
**hash模式**：
在浏览器中符号“#”，#以及#后面的字符称之为hash，用window.location.hash读取

**特点：** hash虽然在URL中，但不被包括在HTTP请求中；用来指导浏览器动作，对服务端安全无用，hash不会重加载页面。
hash 模式下，仅 hash 符号之前的内容会被包含在请求中，如 http://www.xxx.com，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误。

**history模式**：history采用HTML5的新特性；且提供了两个新方法：pushState（），replaceState（）可以对浏览器历史记录栈进行修改，以及popState事件的监听到状态变更。
history 模式下，前端的 URL 必须和实际向后端发起请求的 URL 一致，如 `http://www.xxx.com/items/id`。 后端如果缺少对 /items/id 的路由处理，将返回 404 错误。

**特点**：Vue-Router 官网里如此描述“不过这种模式要玩好，还需要后台配置支持……所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。”

##### keep-alive
keep-alive是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
在vue 2.1.0 版本之后，keep-alive新加入了两个属性: include(包含的组件缓存) 与 exclude(排除的组件不缓存，优先级大于include) 

##### computed 和 watch
**computed**： 是计算属性，依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值；

**watch**： 更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；

运用场景：
- 当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
- 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

##### vue项目性能优化
**（1）代码层面的优化**

- v-if 和 v-show 区分使用场景
- computed 和 watch 区分使用场景
- v-for 遍历必须为 item 添加 key，且避免同时使用 v-if
- 长列表性能优化
- 事件的销毁
- 图片资源懒加载
- 路由懒加载
- 第三方插件的按需引入
- 优化无限列表性能
- 服务端渲染 SSR or 预渲染

**（2）Webpack 层面的优化**
- Webpack 对图片进行压缩
- 减少 ES6 转为 ES5 的冗余代码
- 提取公共代码
- 模板预编译
- 提取组件的 CSS
- 优化 SourceMap
- 构建结果输出分析
- Vue 项目的编译优化

**（3）基础的 Web 技术的优化**
- 开启 gzip 压缩
- 浏览器缓存
- CDN 的使用
- 使用 Chrome Performance 查找性能瓶颈

##### vue3.0
Vue 3.0 正走在发布的路上，Vue 3.0 的目标是让 Vue 核心变得更小、更快、更强大，因此 Vue 3.0 增加以下这些新特性：

**（1）监测机制的改变**

3.0 将带来基于代理 Proxy 的 observer 实现，提供全语言覆盖的反应性跟踪。这消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制：
- 只能监测属性，不能监测对象
- 检测属性的添加和删除；
- 检测数组索引和长度的变更；
- 支持 Map、Set、WeakMap 和 WeakSet。
新的 observer 还提供了以下特性：
- 用于创建 observable 的公开 API。这为中小规模场景提供了简单轻量级的跨组件状态管理解决方案。
- 默认采用惰性观察。在 2.x 中，不管反应式数据有多大，都会在启动时被观察到。如果你的数据集很大，这可能会在应用启动时带来明显的开销。在 3.x 中，只观察用于渲染应用程序最初可见部分的数据。
- 更精确的变更通知。在 2.x 中，通过 Vue.set 强制添加新属性将导致依赖于该对象的 watcher 收到变更通知。在 3.x 中，只有依赖于特定属性的 watcher 才会收到通知。
- 不可变的 observable：我们可以创建值的“不可变”版本（即使是嵌套属性），除非系统在内部暂时将其“解禁”。这个机制可用于冻结 prop 传递或 Vuex 状态树以外的变化。
- 更好的调试功能：我们可以使用新的 renderTracked 和 renderTriggered 钩子精确地跟踪组件在什么时候以及为什么重新渲染。

**（2）模板**

模板方面没有大的变更，只改了作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
同时，对于 render 函数的方面，vue3.0 也会进行一系列更改来方便习惯直接使用 api 来生成 vdom 。

**（3）对象式的组件声明方式**

vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易。

此外，vue 的源码也改用了 TypeScript 来写。其实当代码的功能复杂之后，必须有一个静态类型系统来做一些辅助管理。现在 vue3.0 也全面改用 TypeScript 来重写了，更是使得对外暴露的 api 更容易结合 TypeScript。静态类型系统对于复杂代码的维护确实很有必要。

**（4）其它方面的更改**

vue3.0 的改变是全面的，上面只涉及到主要的 3 个方面，还有一些其他的更改：
- 支持自定义渲染器，从而使得 weex 可以通过自定义渲染器的方式来扩展，而不是直接 fork 源码来改的方式。
- 支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。
- 基于 treeshaking 优化，提供了更多的内置功能。


**vue应用**

**父子组件通信中常用方法**
1. 在父组件中，使用component引用子组件，然后使用props属性：
`<child-component :property="data"></child-component>`
2. 使用Vuex状态管理进行父子组件通信，定义store.js，并定义state，在state中定义传递的属性比如叫childProperty。然后，在子组件中，使用`store.state.childProperty`进行使用。
3. 使用router中的Params进行传参（即路径传参）,
设置路由`/child/:id`，当访问到/child/1元素的时候，在子组件中，使用`this.$route.params.id`的方式进行使用

++不推荐使用LocalStorage缓存传参++，虽然使用缓存也可以获取到数据。但是，这不是推荐的做法，也不方便管理，容易丢失数据或者是数据紊乱（因为没有及时清理与回收）


**vue-cli与elementui集成**

```javascript
// 安装element
vue add element


// main.js引入element
import Vue from 'vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import App from './App.vue';

Vue.use(ElementUI);

new Vue({
  el: '#app',
  render: h => h(App)
});


// 实现按需引入
npm install babel-plugin-component -D
// .babelrc
{
  "presets": [["es2015", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
// main.js
import Vue from 'vue';
import { Button, Select } from 'element-ui';
import App from './App.vue';

Vue.component(Button.name, Button);
Vue.component(Select.name, Select);
/* 或写为
 * Vue.use(Button)
 * Vue.use(Select)
 */

new Vue({
  el: '#app',
  render: h => h(App)
});
```

##### 服务器渲染SSR 
[Vue SSR指南](https://ssr.vuejs.org/zh/)

[从零开始搭建vue-ssr系列](https://segmentfault.com/a/1190000009352740)

**nuxt.js**
[官网](https://zh.nuxtjs.org/guide)
[学习笔记](https://iiong.com/nuxtjs-notes/)
[视频教程](https://www.bilibili.com/video/av37607677/?p=7)

客户端渲染和服务器端渲染的最重要的区别就是究竟是谁来完成html文件的完整拼接，如果是在服务器端完成的，然后返回给客户端，就是服务器端渲染，而如果是前端做了更多的工作完成了html的拼接，则就是客户端渲染
。
创建nuxt项目：
```javascript
yarn create nuxt-app <项目名>
yarn install
cnpm run dev
// 如果要使用 sass 就必须要安装 node-sass和sass-loader
npm install --save-dev node-sass sass-loader
```

<img src="https://i.loli.net/2019/09/26/jBaK4dVUvDIHWPy.png" width="60%" alt="nuxt"/>

[相关文章](http://www.imooc.com/article/72021)

前端vue等框架打包的项目一般为SPA应用，而单页面是不利于SEO的，现在的解决方案有两种：
- SSR服务器渲染
- 预渲染模式(这比服务端渲染要简单很多，而且可以配合 vue-meta-info 来生成title和meta标签，基本可以满足SEO的需求 )
　  
**TIPS** : 使用预渲染vue-router必须使用history模式。当然，有时候我们也可能会遇到让人头疼的SEO问题，那么使用此插件配合 prerender-spa-plugin 也是再合适不过了
<img src="https://i.loli.net/2019/09/26/C9B2uKNRY8fecvg.png" width="50%" alt="nuxt流程图"/>
[Vue SEO处理1——Vue-meta-info&prerender-spa-plugin](https://blog.csdn.net/aeoliancrazy/article/details/79539143)

##### 警惕内存泄漏
- beforeDestroy()、destroyed钩子清除出定时器、相关变量置为null
- 使用内建的 keep-alive组件，状态就会保留，因此就留在了内存里

要确保测试应用的内存泄漏问题并在适当的时机做必要的组件清理。

##### 页面过渡动画
Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。
包括以下工具：

- 在 CSS 过渡和动画中自动应用 class
- 可以配合使用第三方 CSS 动画库，如 Animate.css
- 在过渡钩子函数中使用 JavaScript 直接操作 DOM
- 可以配合使用第三方 JavaScript 动画库，如 Velocity.js
    
Vue 提供了 transition的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 v-if)
- 条件展示 (使用 v-show)
- 动态组件
- 组件根节点

在进入/离开的过渡中，会有 6 个 class 切换。
<img src="https://i.loli.net/2019/09/26/DHv8Tj6kUKgMXut.jpg" alt="vue页面过渡" width="60%"/>
对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用。

##### 可复用的过渡
过渡可以通过 Vue的组件系统实现复用。要创建一个可复用过渡组件，你需要做的就是将 <transition> 或者 <transition-group> 作为根组件，然后将任何子组件放置在其中就可以了。

```javascript
Vue.component('my-special-transition', {
  template: '\
    <transition\
      name="very-special-transition"\
      mode="out-in"\
      v-on:before-enter="beforeEnter"\
      v-on:after-enter="afterEnter"\
    >\
      <slot></slot>\
    </transition>\
  ',
  methods: {
    beforeEnter: function (el) {
      // ...
    },
    afterEnter: function (el) {
      // ...
    }
  }
})
```
##### vue-router
[vue-router](https://router.vuejs.org/zh/)
##### vuex
[vuex](https://vuex.vuejs.org/zh/)