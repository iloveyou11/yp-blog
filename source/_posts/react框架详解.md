---
title: 深入理解react
date: 2019-09-25
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

#### react基础
##### 介绍
安装官方脚手架项目
```
npm install -g create-react-app
create-react-app (name)
```
**react组件核心概念**
- 引入Component组件
- JSX语法
- 渲染虚拟DOM
- 组件props
- 组件state
- 组件嵌套
- 组件生命周期

**React特点**
- 声明式设计 −React采用声明范式，可以轻松描述应用。
- 高效 −React通过对DOM的模拟，最大限度地减少与DOM的交互。
- 灵活 −React可以与已知的库或框架很好地配合。
- JSX − JSX 是 JavaScript 语法的扩展。React 开发不一定使用 JSX ，但我们建议使用它。
- 组件 − 通过 React 构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
- 单向响应的数据流 − React 实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单

**总结：**
- 声明式开发：采用数据驱动的开发方式，只用关注数据，不用再关注复杂的DOM操作
- 框架并存：react可以只负责渲染指定id的dom，其他的dom渲染可以采用其他框架
- 组件化：采用组件化形式编写，父子组件通信，父组件可以向子组件传递属性、函数，子组件可以通过this.props接收
- 单向数据流：只允许子组件接收和使用父组件的属性，不允许修改父组件的属性，只能间接地调用父组件地函数。React地这种机制保证了错误的快速定位
- 视图层框架：react将自己定位为视图层框架，是因为它很好地解决了视图渲染与数据绑定的问题，但是没有解决复杂层级组件的通信问题，要借用flux、redux、mobx等解决
- 函数式编程：存在大量的函数，这样为自动化测试提供了很大的便利

##### jsx
JSX 是一种用于描述UI的JavaScript扩展语法，JSX可以使用JavaScript表达式， 因为JSX本质上仍然是JavaScript。JSX语法只是 React.createElement ( )的语法糖， 所有的JSX语法最终都会被转换成对这个方法的调用


##### this指向问题
[React事件this指向问题](https://segmentfault.com/a/1190000013609997)
```js
1、<span onClick={this.del.bind(this)}></span>
2、constructor(props) {
        super(props);
        this.state = {};
        this.del=this.del.bind(this)
    }
3、<span onClick={::this.del}></span>
4、del=()=>{
        console.log('del')
    }
```

##### 生命周期
React 16之后有三个生命周期被废弃(但并未删除)
- componentWillMount
- componentWillReceiveProps
- componentWillUpdate

官方计划在17版本完全删除这三个函数，只保留UNSAVE_前缀的三个函数，目的是为了向下兼容，但是对于开发者而言应该尽量避免使用他们，而是使用新增的生命周期函数替代它们
目前React 16.8 +的生命周期分为三个阶段,分别是挂载阶段、更新阶段、卸载阶段

**挂载阶段:**
- **constructor**: 构造函数，最先被执行,我们通常在构造函数里初始化state对象或者给自定义方法绑定this
- **getDerivedStateFromProps**: static getDerivedStateFromProps(nextProps, prevState),这是个静态方法,当我们接收到新的属性想去修改我们state，可以使用getDerivedStateFromProps
- **render**: render函数是纯函数，只返回需要渲染的东西，不应该包含其它的业务逻辑,可以返回原生的DOM、React组件、Fragment、Portals、字符串和数字、Boolean和null等内容
- **componentDidMount**: 组件装载之后调用，此时我们可以获取到DOM节点并操作，比如对canvas，svg的操作，服务器请求，订阅都可以写在这个里面，但是记得在componentWillUnmount中取消订阅

**更新阶段:**
- **getDerivedStateFromProps**: 此方法在更新个挂载阶段都可能会调用
- **shouldComponentUpdate**: shouldComponentUpdate(nextProps, nextState),有两个参数nextProps和nextState，表示新的属性和变化之后的state，返回一个布尔值，true表示会触发重新渲染，false表示不会触发重新渲染，默认返回true,我们通常利用此生命周期来优化React程序性能
- **render**: 更新阶段也会触发此生命周期
- **getSnapshotBeforeUpdate**: getSnapshotBeforeUpdate(prevProps, prevState),这个方法在render之后，componentDidUpdate之前调用，有两个参数prevProps和prevState，表示之前的属性和之前的state，这个函数有一个返回值，会作为第三个参数传给componentDidUpdate，如果你不想要返回值，可以返回null，此生命周期必须与componentDidUpdate搭配使用
- **componentDidUpdate**: componentDidUpdate(prevProps, prevState, snapshot),该方法在getSnapshotBeforeUpdate方法之后被调用，有三个参数prevProps，prevState，snapshot，表示之前的props，之前的state，和snapshot。第三个参数是getSnapshotBeforeUpdate返回的,如果触发某些回调函数时需要用到 DOM 元素的状态，则将对比或计算的过程迁移至 getSnapshotBeforeUpdate，然后在 componentDidUpdate 中统一触发回调或更新状态。

**卸载阶段:**
- **componentWillUnmount**: 当我们的组件被卸载或者销毁了就会调用，我们可以在这个函数里去清除一些定时器，取消网络请求，清理无效的DOM元素等垃圾清理工作

##### 组件类型
**展示组件(Presentational component)和容器组件(Container component)**

- 展示组件关心组件看起来是什么。展示专门通过 props 接受数据和回调，并且几乎不会有自身的状态，但当展示组件拥有自身的状态时，通常也只关心 UI 状态而不是数据的状态。
- 容器组件则更关心组件是如何运作的。容器组件会为展示组件或者其它容器组件提供数据和行为(behavior)，它们会调用 Flux actions，并将其作为回调提供给展示组件。容器组件经常是有状态的，因为它们是(其它组件的)数据源。

**类组件(Class component)和函数式组件(Functional component)**
- 类组件不仅允许你使用更多额外的功能，如组件自身的状态和生命周期钩子，也能使组件直接访问 store 并维持状态
- 当组件仅是接收 props，并将组件自身渲染到页面时，该组件就是一个 '无状态组件(stateless component)'，可以使用一个纯函数来创建这样的组件。这种组件也被称为哑组件(dumb components)或展示组件

**(组件的)状态(state)和属性(props)**

- State 是一种数据结构，用于组件挂载时所需数据的默认值。State 可能会随着时间的推移而发生突变，但多数时候是作为用户事件行为的结果。
- Props(properties 的简写)则是组件的配置。props 由父组件传递给子组件，并且就子组件而言，props 是不可变的(immutable)。组件不能改变自身的 props，但是可以把其子组件的 props 放在一起(统一管理)。Props 也不仅仅是数据--回调函数也可以通过 props 传递。

**何为受控组件(controlled component)**

在 HTML 中，类似 `<input>, <textarea> 和 <select>` 这样的表单元素会维护自身的状态，并基于用户的输入来更新。当用户提交表单时，前面提到的元素的值将随表单一起被发送。但在 React 中会有些不同，包含表单元素的组件将会在 state 中追踪输入的值，并且每次调用回调函数时，如 onChange 会更新 state，重新渲染组件。一个输入表单元素，它的值通过 React 的这种方式来控制，这样的元素就被称为"受控元素"。

**何为高阶组件(higher order component)**

高阶组件是一个以组件为参数并返回一个新组件的函数。HOC 运行你重用代码、逻辑和引导抽象。最常见的可能是 Redux 的 connect 函数。除了简单分享工具库和简单的组合，HOC 最好的方式是共享 React 组件之间的行为。如果你发现你在不同的地方写了大量代码来做同一件事时，就应该考虑将代码重构为可重用的 HOC。

##### 状态管理——React-redux

<img src="https://i.loli.net/2019/09/26/mlMWQBGIaOqCAot.png" alt="redux-flow" width="60%"/>

**Redux的设计原则：**
- store是唯一的
- 只有store能够改变state的内容（reducer不要改变state的内容）
- Reducer必须是纯函数

**Store相关函数**：
- createStore：创建store
- store.dispatch：派发action
- store.getState：获取state
- store.subscribe：监听store的变化，store变化时自动运行函数

**功能：**
- React-Redux 是为了方便在 React 项目中使用 Redux 所封装的库
- React-Redux 把组件分为两类：UI 组件（Component）和容器组件（Container）
- React-Redux 提供 Provider 组件，使它的子组件（容器组件）都能拿到 state
- 连接通信（connect） - 连接 UI 组件和容器组件，为输入输出 state 做准备
- 逻辑输入（mapStateToProps）- 将容器组件的 state 映射为 UI 组件的 props
- 逻辑输出（mapDispatchToProps） - 用户动作触发 UI 组件绑定事件，dispatch 更新容器组件 state

**相关操作：**
1. connect() 示例：
```jsx
import {connect} from 'react-redux';
const Container = connect(
    mapStateToProps,
    mapDispatchToProps
)(Component);
```
2. mapStateToProps() 示例：
```jsx
const mapStateToProps = (state, ownProps) => {
    return props;
};
```
mapStateToProps 会订阅 Store，每当 state 更新，就会自动执行，重新计算 UI 组件的 props，从而触发 UI 组件的重新渲染

3. mapDispatchToProps 示例：
```jsx
mapDispatchToProps 函数：
const mapDispatchToProps = (dispatch, ownProps) => {
    return {
        handleClick: () => {
            dispatch(action);
        }
    };
};
mapDispatchToProps 对象：
const mapDispatchToProps = {
    handleClick: actionCreator;
};
```
mapDispatchToProps 用来建立 UI 组件的 props 到 Store.dispatch 方法的映射
至此，容器组件与内部的 UI 组件的通信已经解决。容器组件还需要与外部通信，拿到 state 对象。
React-Redux 提供 Provider 组件，可以让容器组件拿到 state。

4、provider
```jsx
import {Provider} from 'react-redux';
import {createStore} from 'redux';
import todoApp from './reducers';
import App from './components/App';
const store = createStore(todoApp);
render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);
```
##### 状态管理——mobx
1. 定义需要被全局观察的state，用@observable修饰
2. 定义改变state的行为函数，用@action修饰
3. 定义基于某state，通过计算产生新值的get函数，用@computed修饰
4. 定义基于所传参数，通过计算得到state值的set函数，用@computed修饰
5. 定义基于state变化，自动触发的行为函数，用@autorun修饰
6. 在文件末尾，新建一个该组件的实例，并export


#### react原理
##### 调用 setState 后？
在代码中调用 setState 函数之后，React 会将传入的参数对象与组件当前的状态合并，然后触发所谓的调和过程（Reconciliation）。

经过调和过程，React 会以相对高效的方式根据新的状态构建 React 元素树并且着手重新渲染整个 UI 界面。在 React 得到元素树之后，React 会自动计算出新的树与老树的节点差异，然后根据差异对界面进行最小化重渲染。

在差异计算算法中，React 能够相对精确地知道哪些位置发生了改变以及应该如何改变，这就保证了按需更新，而不是全部重新渲染。

##### react diff 原理
把树形结构按照层级分解，只比较同级元素。
给列表结构的每个单元添加唯一的 key 属性，方便比较。
React 只会匹配相同 class 的 component（这里面的 class 指的是组件的名字）

合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.
选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能。

##### flux 思想
Flux 的最大特点，就是数据的"单向流动"。
1.	用户访问 View
2.	View 发出用户的 Action
3.	Dispatcher 收到 Action，要求 Store 进行相应的更新
4.	Store 更新后，发出一个"change"事件
5.	View 收到"change"事件后，更新页面

##### refs
Refs 是 React 中引用的简写。它是一个有助于存储对特定的 React 元素或组件的引用的属性，它将由组件渲染配置函数返回。用于对 render() 返回的特定元素或组件的引用。当需要进行 DOM 测量或向组件添加方法时，它们会派上用场。
以下是应该使用 refs 的情况： 
1.	需要管理焦点、
2.	选择文本或媒体播放时 
3.	触发式动画
4.	第三方 DOM 库集成


#### react应用
##### 性能优化方案

组件更新：

**1、PureComponent**

如果组件挂载了connect连接react-redux，则当store内部的数据一旦发生改变时，相关联的组件都会重新渲染，而组件是否需要重新渲染取决于它所需要的部分属性，为了提高性能，新版的react提供了 PureComponent模块，**Component改为PureComponent**，能够内置shouldComponentUpdate函数，根据需要进行刷新，能提高性能。

但是我们要注意的是，这里的PureRender是浅比较的，因为深比较的场景是相当昂贵的。所以我们要注意：不要直接为props设置对象或者数组、不要将方法直接绑定在元素上，因为其实函数也是对象.
提醒：如果使用PureComponent，**建议使用immutable.js**管理项目数据，若未使用immutable.js，则尽量使用Component，否则会遇到一些意想不到的错误。

**2、PureRenderMixin 优化**

React 最基本的优化方式是使用**PureRenderMixin**，安装工具 npm i react-addons-pure-render-mixin --save，然后在组件中引用并使用
```
import React from 'react'
import PureRenderMixin from 'react-addons-pure-render-mixin'
class List extends React.Component {
    constructor(props, context) {
        super(props, context);
        this.shouldComponentUpdate = PureRenderMixin.shouldComponentUpdate.bind(this);
    }
    //...省略其他内容...
}
```
React 有一个生命周期 hook 叫做shouldComponentUpdate，组件每次更新之前，都要过一遍这个函数，如果这个函数返回true则更新，如果返回false则不更新。而默认情况下，这个函数会一直返回true，就是说，如果有一些无效的改动触发了这个函数，也会导致无效的更新。**因此，我们在开发过程中，在每个 React 组件中都尽量使用PureRenderMixin**

**组件管理：**

**3、Immutable.js**

React 的终极优化是使用 Immutable.js 来处理数据，Immutable 实现了 js 中不可变数据的概念。
但是也不是所有的场景都适合用它，当我们组件的props和state中的数据结构层次不深（例如普通的数组、对象等）的时候，就没必要用它。但是当数据结构层次很深（例如obj.x.y.a.b = 10这种），你就得考虑使用了。

之所以不轻易使用是，Immutable 定义了一种新的操作数据的语法，如下。和我们平时操作 js 数据完全不一样，而且每个地方都得这么用，学习成本高、易遗漏，风险很高。

**4、单个react组件性能优化**

render里面尽量减少新建变量和bind函数，传递参数是尽量减少传递参数的数量。
<img src="https://i.loli.net/2019/09/26/HqVrtGhgTfDIAxB.png" width="80%" alt="单个react组件性能优化" />

**5、多个react组件性能优化，key的优化**

给列表中的组件添加key属性——（针对列表遍历类型，遍历时候增加唯一 key 属性值，对子组件进行唯一性识别，准确知道要操作的子组件，提高 DOM Diff 的效率)

关于key的使用我们要注意的是，这个key值要稳定不变的，就如同身份证号之于我们是稳定不变的一样。

一个常见的错误就是，拿数组的的下标值去当做key，这个是很危险的，代码如下，我们一定要避免。


##### 异步处理中间件
- **Redux-thunk：redux的中间件**

使用redux-thunk，可以将异步操作代码放入actionCreator中，在action中直接返回函数，而不是传统的action对象
优点：将组件生命周期中的大量异步逻辑代码提取出来，可以简化代码，方便自动化测试

原理：是store与action之间的中间件，修改了dispatch方法的逻辑，如果action是对象，直接将action连同state传给reducer；如果action是函数，则先执行函数后，再处理action的派发事件。
- Redux-saga：redux中间件

将异步逻辑拆分到sagas.js里面统一管理

##### 服务器渲染SSR
**客户端渲染路线：**
1.	请求html
2.	服务端返回html
3.	浏览器下载html里面的js/css文件
4.	等待js文件下载完成
5.	等待js加载并初始化完成
6.	js代码可以运行后由js代码向后端请求数据( ajax/fetch )
7.	等待后端数据返回
8.	react-dom( 客户端 )从无到完整地，把数据渲染为响应页面

**服务端渲染路线：**
1.	请求html
2.	服务端请求数据( 内网请求快 )
3.	服务器初始渲染（服务端性能好，较快）
4.	服务端返回已经有正确内容的页面
5.	客户端请求js/css文件
6.	等待js文件下载完成
7.	等待js加载并初始化完成
8.	react-dom( 客户端 )把剩下一部分渲染完成( 内容小，渲染快)

##### react hooks

使用react hooks可以极大地简化代码写法，使用function代替原始的class写法，以下是class和function的对比：
```jsx
import React, { useState, useEffect } from 'react'

// 原始class组件
class MyCount extends React.Component {
  state = {
    count: 0
  }

  componentDidMount() {
    this.interval = setInterval(() => {
      this.setState({ count: this.state.count + 1 })
    }, 1000);
  }

  componentWillUnmount() {
    if (this.interval) {
      clearInterval(this.interval)
    }
  }

  render() {
    return <span>{this.state.count}</span>
  }
}


// 使用react hooks
function MyCountFunction() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    const interval = setInterval(() => {
      setCount(c => c + 1)
    }, 1000);
    return () => clearInterval(interval)
  }, [])

  return <span>{count}</span>
}

export default MyCountFunction
```


**状态管理的hook：useState & useReducer**
1. 使用useState
```jsx
import React, { useState, useEffect } from 'react'

function MyCountFunction() {
  const [count, setCount] = useState(0) //0是默认值
  
  useEffect(() => {
    const interval = setInterval(() => {
      setCount(c => c + 1)
    }, 1000);
    return () => clearInterval(interval)
  }, [])

  return <span>{count}</span>
}

export default MyCountFunction
```
2. 使用useReducer

```jsx
import React, { useReducer, useEffect } from 'react'

function countReducer(state, action) {
  switch (action.type) {
    case 'add':
      return state + 1
    case 'minus':
      return state - 1
    default:
      return state
  }
}
function MyCountFunction() {
  const [count, dispatchCount] = useReducer(countReducer, 0)
  
  useEffect(() => {
    const interval = setInterval(() => {
      dispatchCount({ type: 'add' })
    }, 1000);
    return () => clearInterval(interval)
  }, [])

  return <span>{count}</span>
}

export default MyCountFunction
```
**useEffect和useLayoutEffect**
**useEffect**
如果第二个参数不传，则组件每次状态改变重新渲染时，都会执行useEffect中定义的函数
如果第二个参数传[]，则组件只在第一次渲染时会执行响应函数
如果第二个参数传[name]，则组件只有修改name时才会执行响应函数
如果第二个参数传[count]，则组件只有修改count时才会执行响应函数

**[react官方建议]**
在useEffect中用到的任何在外部定义的变量，就要把这个值作为第二项将其传进去，
我们称之为dependency，根据dependency判断是否要重新执行useEffect

useLayoutEffect永远要比useEffect先执行
**[原理]**
useLayoutEffect是在没有更新到真正的dom内容生成html之前执行
useEffect是在真正的dom内容生成html之后执行
如果useLayoutEffect时间执行过长，会影响页面渲染，如果没有特殊需要，不要使用useLayoutEffect

```jsx
import React, { useReducer, useEffect, useLayoutEffect } from 'react'

function countReducer(state, action) {
  switch (action.type) {
    case 'add':
      return state + 1
    case 'minus':
      return state - 1
    default:
      return state
  }
}

function MyCountFunction() {
  const [count, dispatchCount] = useReducer(countReducer, 0)
  const [name, setName] = useState('yp')

  useEffect(() => {
    console.log('useEffect start');
    return () => console.log('useEffect end');
  })

  useLayoutEffect(() => {
    console.log('useLayoutEffect start');
    return () => console.log('useLayoutEffectend');
  })

  return (
    <div>
      <input type="text" value={name} onChange={e => setName(e.target.value)} />
      <button onClick={() => dispatchCount({ type: 'add' })}>+1</button>
    </div>
  )
}

export default MyCountFunction
```

**useContext**

```jsx
import React, { useContext } from 'react'
import { UserContext } from './UserContext'
import { MessageContext } from './MessageContext'

const MessageList = () => {
  const { user } = useContext(UserContext)
  const { loading, messages, onSelectMessage } = useContext(MessageContext)
  return (
    <div>
      {
        loading ? <div>加载中......</div> :
          messages.length === 0 ? <div>没有信息, {user.name}</div> :
            <ul>
              {messages.map(message => <Message key={message.id} message={message} onClick={() => onSelectMessage(message)} />)}
            </ul>
      }
    </div>
  )
}

const Message = ({ message, onClick }) => (
  <li onClick={onClick}>
    <div>{message.subject}</div>
  </li>
)

export default MessageList
```
**Ref hook**

获取dom元素，应用很简单，`const inputRef = useRef(), <input ref={inputRef}/>`即可
```jsx
import React, { useState, useEffect, useRef } from 'react'

function MyCountFunction() {
  const [count, dispatchCount] = useReducer(countReducer, 0)
  const [name, setName] = useState('yp')
  const inputRef = useRef()

  useEffect(() => {
    console.log(inputRef);
    return () => console.log('end');
  })

  return (
    <div>
      <input ref={inputRef} type="text" value={name} onChange={e => setName(e.target.value)} />
      <button onClick={() => dispatchCount({ type: 'add' })}>+1</button>
    </div>
  )
}

export default MyCountFunction
```

**useMemo, useCallback**

useMemo, useCallback用于性能优化

**问题的产生**

每次name、count改变，MyCountFunction会重新渲染，从而导致MyChild的prop（onButtonClick, config）会重新生成【注意：与之前的值完全是两个不同的对象，即便值完全相等】

因此，属性需要使用到useMemo来优化，函数需要使用到useCallback来优化，使得prop相同时，子组件MyChild不必重新渲染

**解决组件渲染，提高组件性能**

useMemo第二个参数：

和useEffect第二个参数的效果是一样的
[count]表示只要count不变，返回的对象就不必重新生成，只要count不变，useMemo就会返回相同的对象

useCallback使得函数内容相同时，不必重新生成新的函数

针对方法，使用useMemo同样有效，只是useCallback是个更加简化的版本。

```jsx
import React, { useState, useEffect, memo, useMemo, useCallback } from 'react'

function MyCountFunction() {
  const [count, setCount] = useState(0) //0是默认值
  const [name, setName] = useState('yp')

  useEffect(() => setCount(c => c + 1), [])
  
  const config = useMemo(() => ({
    text: `count is ${count}`,
    color: count > 3 ? 'red' : 'blue'
  }), [count])

  const handleButtonClick = useCallback(() => setCount(c => c + 1), [])

  return (
    <div>
      <input type="text" value={name} onChange={e => setName(e.target.value)} />
      <MyChild
        config={config}
        onButtonClick={handleButtonClick}
      >
      </MyChild>
    </div>
  )
}

const MyChild = memo(function ({ onButtonClick, config }) {
  console.log('child render');
  return (
    <button onClick={onButtonClick} style={{ color: config.color }}>
      {config.text}
    </button>
  )
})

export default MyCountFunction
```

存在闭包陷阱

```jsx
  const handleClickAlert = () => {
    setTimeout(() => {
      alert(count)
    }, 2000);
  }

  return (
    <div>
      <input type="text" value={name} onChange={e => setName(e.target.value)} />
      <MyChild
        config={config}
        onButtonClick={handleButtonClick}
      >
      </MyChild>
      <button onClick={handleClickAlert}>alert</button>
    </div>
  )
```
如果先点击了alert按钮，再点击+1改变count值，会发现alert出现的内容仍然是点击时候的count值（非最新count值），每次执行的更新都是闭包中的状态，在这次状态下触发的任何东西，都不会去获得以后更新的状态，只会在这个闭包的状态下去执行它。

**解决办法：**
使用useRef来规避闭包陷阱

```jsx
  const countRef = useRef()
  countRef.current = count
  const handleClickAlert = () => {
    setTimeout(() => {
      alert(countRef.current)
    }, 2000);
  }
```