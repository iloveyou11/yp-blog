---
title: react源码分析笔记
date: 2019-11-26
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

react作为一个优秀的前端前端框架，必定有着它的过人之处。Facebook团队花费三年多打造的这个框架，如今已经成为了企业追捧的框架选择，让我们来学习react优秀的设计思想吧！

<!-- more -->



##### jsx到js代码的转换
建议使用`babel-playground`查看转换过程
```html
<div>
  <div>lala</div>
  <span>hello</span>
</div>
```
```js
React.createElement(
  "div",
  null, 
  React.createElement("div", null, "lala"), 
  React.createElement("span", null, "hello")
);
```

##### ReactElement
```js
//简化的代码……
export function createElement(type, config, children) {
    let propName;
    const props = {};

    let key = null;
    let ref = null;
    let self = null;
    let source = null;

    if (config != null) {
        if (hasValidRef(config)) {
            ref = config.ref;
        }
        if (hasValidKey(config)) {
            key = '' + config.key;
        }

        //将config上的属性存到特定对象中
        for (propName in config) {
            if (
                hasOwnProperty.call(config, propName) &&
                !RESERVED_PROPS.hasOwnProperty(propName)
            ) {
                props[propName] = config[propName];
            }
        }
    }

    // 剩下的参数均是children
    const childrenLength = arguments.length - 2;
    if (childrenLength === 1) {
        props.children = children;
    } else if (childrenLength > 1) {
        const childArray = Array(childrenLength);
        for (let i = 0; i < childrenLength; i++) {
            childArray[i] = arguments[i + 2];
        }
        props.children = childArray;
    }

    // 处理传入默认的props
    if (type && type.defaultProps) {
        const defaultProps = type.defaultProps;
        for (propName in defaultProps) {
            if (props[propName] === undefined) {
                props[propName] = defaultProps[propName];
            }
        }
    }
  
    return ReactElement(
        type,
        key,
        ref,
        self,
        source,
        ReactCurrentOwner.current,
        props,
    );
}
```

再看一下ReactElement函数:

```js
//简化的代码……
const ReactElement = function(type, key, ref, self, source, owner, props) {
    const element = {
        //用来标识element的类型,如果是通过React.createElement创建的,那它的$$typeof永远都是REACT_ELEMENT_TYPE,这个属性在后续组件的渲染中是经常被用来判断的
        $$typeof: REACT_ELEMENT_TYPE,

        type: type,//节点类型
        key: key,
        ref: ref,
        props: props,

        _owner: owner,
    };
    return element;
};

```

##### Component

我们以为React.Component会是非常复杂的,会帮我们实现各种各样的逻辑,如接受render如何将组件渲染到页面呀,but!为什么只有以下几行代码?在这里,Component竟然只保留了部分信息.
```js
//Component
function Component(props, context, updater) {
    this.props = props;
    this.context = context;
    this.refs = emptyObject;
    this.updater = updater || ReactNoopUpdateQueue;
}
Component.prototype.isReactComponent = {};
Component.prototype.setState = function(partialState, callback) {
    // 1. 错误检测与提醒
    // 2. 调用enqueueSetState方法,在react-dom中实现的
    this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
Component.prototype.forceUpdate = function(callback) {
    this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};


//PureComponent
function PureComponent(props, context, updater) {
    this.props = props;
    this.context = context;
    this.refs = emptyObject;
    this.updater = updater || ReactNoopUpdateQueue;
}
// 简单的继承
const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
Object.assign(pureComponentPrototype, Component.prototype);
//唯一的区别,来标记这个组件是PureComponent,以便在后续更新时会主动判断是否更新
pureComponentPrototype.isPureReactComponent = true;
```

##### createRef和ref
react中的3种使用ref的方式:
1. 定义`ref="stringRef"`,获取`this.refs.stringRef.textContent = 'string ref got'`(最不被推荐,即将废弃)
2. 定义`ref={ele => (this.methodRef = ele)}`,获取`this.methodRef.textContent = 'method ref got'`
3. 使用createRef API,`this.objRef = React.createRef()`,定义`ref={this.objRef}`,获取`this.objRef.current.textContent = 'obj ref got'`

demo示例:
```js
import React from 'react'

export default class RefDemo extends React.Component {
    constructor() {
        super()
        this.objRef = React.createRef()
    }

    componentDidMount() {
        setTimeout(() => {
            this.refs.stringRef.textContent = 'string ref got'
            this.methodRef.textContent = 'method ref got'
            this.objRef.current.textContent = 'obj ref got'
        }, 1000)
    }

    render() {
        return (
            <>
                <p ref="stringRef">span1</p>
                <p ref={ele => (this.methodRef = ele)}>span3</p>
                <p ref={this.objRef}>span3</p>
            </>
        )
    }
}
```
createRef源码如下,只是简单地返回一个RefObject对象,但是之后如何使用的呢?先保留这个问题.
```js
export function createRef(): RefObject {
    const refObject = {
        current: null,
    };
    return refObject;
}
```

##### forwardRef
```js
import React from 'react'

// 如果TargetComponent返回的是一个function component,不是class component,那render函数中的ref是拿不到任何东西的,就会报错,因为function component是没有实例的

// React.forwardRef相当于是HOC,帮我们实现了ref的传递
const TargetComponent = React.forwardRef((props, ref) => (
  <input type="text" ref={ref} />
))

export default class Comp extends React.Component {
  constructor() {
    super()
    this.ref = React.createRef()
  }

  componentDidMount() {
    this.ref.current.value = 'ref get input'
  }

  render() {
    return <TargetComponent ref={this.ref} />
  }
}

```
forwardRef的源码:
```js
export default function forwardRef < Props, ElementType: React$ElementType > (
    render: (props: Props, ref: React$Ref < ElementType > ) => React$Node,
) {
    return {
      // 注意,这里的$$typeof是REACT_FORWARD_REF_TYPE,并不代表render渲染出来的组件是REACT_FORWARD_REF_TYPE.
      // 逻辑关系是: react渲染出来的组件仍然是REACT_ELEMENT_TYPE,只是它的type是这个forward-ref组件,它其中有个属性$$typeof是REACT_FORWARD_REF_TYPE,这里不要搞混淆了
        $$typeof: REACT_FORWARD_REF_TYPE,
        render,
    };
}
```
##### Context
嵌套多层的父子组件之间传值通过props的方式是不够现实的,因为中间这些组件传递props是完全没有意义的事情,这时可以采用Context的方式.

上级组件中提供了Context之后,它下面渲染的组件都可以拿到Context传递的值,以此达到跨越多层组件传递信息的功能.

demo示例:
```js
import React from 'react'
import PropTypes from 'prop-types'
const { Provider, Consumer } = React.createContext('default')

class Parent extends React.Component {
  state = {
    childContext: '123',
    newContext: '456',
  }

  getChildContext() {
    return { value: this.state.childContext, a: 'aaaaa' }
  }

  render() {
    return (
      <>
        <div>
          <label>childContext:</label>
          <input
            type="text"
            value={this.state.childContext}
            onChange={e => this.setState({ childContext: e.target.value })}
          />
        </div>
        <div>
          <label>newContext:</label>
          <input
            type="text"
            value={this.state.newContext}
            onChange={e => this.setState({ newContext: e.target.value })}
          />
        </div>
        <Provider value={this.state.newContext}>{this.props.children}</Provider>
      </>
    )
  }
}

function Child(props, context) {
  return <Consumer>{value => <p>newContext: {value}</p>}</Consumer>
}

export default () => (
  <Parent>
    <Child/>
  </Parent>
)

```
createContext源码示例:
```js
export function createContext<T>(
  defaultValue: T,
  calculateChangedBits: ?(a: T, b: T) => number,
): ReactContext<T> {
  if (calculateChangedBits === undefined) {
    calculateChangedBits = null;
  } 

  const context: ReactContext<T> = {
    $$typeof: REACT_CONTEXT_TYPE,
    _calculateChangedBits: calculateChangedBits,
    _currentValue: defaultValue,
    _currentValue2: defaultValue,
    Provider: (null: any),
    Consumer: (null: any),
  };

  context.Provider = {
    $$typeof: REACT_PROVIDER_TYPE,
    _context: context,
  };

  context.Consumer = context;

  return context;
}
```
由以上代码可以看出.context.Consumer = context,在Consumer进行渲染的时候,它要去获取value的话,就直接在_currentValue拿到最新的context的值.

##### ConcurrentMode
react16之后提出的令人振奋的功能ConcurrentMode.

**目标:** 让react整体的渲染过程能够进行一个优先级的排比,并且让整体渲染过程是可以中断的,就可以进行任务的调度.从而先执行优先级高的任务,等浏览器空闲时再执行优先级低的任务.

ConcurrentMode的demo示例:
```js
import React, { ConcurrentMode } from 'react'
import { flushSync } from 'react-dom' //强制在更新时使用优先级最高的方式进行更新

class Parent extends React.Component {
  state = {
    async: true,
    num: 1,
    length: 2000,
  }

  componentDidMount() {
    this.interval = setInterval(() => {
      this.updateNum()
    }, 200)
  }

  componentWillUnmount() {
    // 别忘了清除interval
    if (this.interval) {
      clearInterval(this.interval)
    }
  }

  updateNum() {
    const newNum = this.state.num === 3 ? 0 : this.state.num + 1
    if (this.state.async) {
      this.setState({
        num: newNum,
      })
    } else {
      flushSync(() => {
        this.setState({
          num: newNum,
        })
      })
    }
  }

  render() {
    const children = []
    const { length, num, async } = this.state
    for (let i = 0; i < length; i++) {
      children.push(
        <div className="item" key={i}>
          {num}
        </div>,
      )
    }

    return (
      <div className="main">
        async:{' '}
        <input
          type="checkbox"
          checked={async}
          onChange={() => flushSync(() => this.setState({ async: !async }))}
        />
        <div className="wrapper">{children}</div>
      </div>
    )
  }
}
export default () => (
  <ConcurrentMode>
    <Parent />
  </ConcurrentMode>
)
```
ConcurrentMode源码:
```js
export const REACT_CONCURRENT_MODE_TYPE = hasSymbol
  ? Symbol.for('react.concurrent_mode')
  : 0xeacf;
```
what?!  只是一个简单的Symbol?没有复杂的逻辑?

##### Suspense
原理: 在Suspense组件下面渲染了一个或多个异步的组件,有任何一个抛出了promise,在这个promise resolve之前,都会显示fallback.只有等到所有组件promise resolve之后,才会去除fallback,然后显示其中的内容.

Suspense的demo示例:
```js
import React, { Suspense, lazy } from 'react'
const LazyComp = lazy(() => import('./lazy.js'))
let data = ''
let promise = ''

function requestData() {
  if (data) return data
  if (promise) throw promise
  promise = new Promise(resolve => {
    setTimeout(() => {
      data = 'Data resolved'
      resolve()
    }, 2000)
  })
  throw promise
}

function SuspenseComp() {
  const data = requestData()
  return <p>{data}</p>
}

export default () => (
  <Suspense fallback="loading data">
    <SuspenseComp />
    <LazyComp />
  </Suspense>
)
```
Suspense和lazy源码解析:
```js
//Suspense又是一个Symbol标识
export const REACT_SUSPENSE_TYPE = hasSymbol
  ? Symbol.for('react.suspense')
  : 0xead1;

//lazy函数返回一个对象
export function lazy<T, R>(ctor: () => Thenable<T, R>): LazyComponent<T> {
  return {
    $$typeof: REACT_LAZY_TYPE,
    _ctor: ctor,//传入的方法
    _status: -1,//promise的状态,pending为-1
    _result: null,//记录resolve之后返回的结果
  };
}
```

##### Hooks
可以使用hooks给函数式组件存储state状态,给function component提供了class component所具有的能力.

简单demo示例:
```js
import React, { useState, useEffect } from 'react'

export default () => {
  //useState返回变量 和 改变变量的方法
  const [name, setName] = useState('haha') 

  // 生命周期方法
  useEffect(() => {
    return () => {
      console.log('unbind')
    }
  }, [])

  return (
    <>
      <p>My Name is: {name}</p>
      <input type="text" value={name} onChange={e => setName(e.target.value)} />
    </>
  )
}
```
