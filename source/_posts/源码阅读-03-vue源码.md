---
title: 源码阅读-03-vue源码
date: 2020-08-18
categories: 源码
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

### 关于如何读源码
曾经，我去阅读源码时，总是喜欢一句不漏的从上到下阅读，怕漏掉什么核心代码导致不理解程序的详细流程，遇到一个函数或类就跳过去看，然后……花费了很多的时间而且效果相当不理想。不仅对整体的框架没有大体了解，也让自己像只无头苍蝇，不知道如何抓住重点去深入研究背后的原理。后来看了不少大佬阅读的方式，对我也是收获颇多：不要采用DFS（深度遍历）的方式阅读源码，而是应该先理清框架的架构，再从入口出发，依照流程读下去。这个过程首先应该对框架有一个整体的了解，建议画个流程图或思维脑图，掌握源码的整体处理流程。接下来可以针对源码功能进行个分类，进行深入研究：如阅读koa时，可以分为框架设计和基础结构、中间件实现机制（洋葱模型）、同一错误处理、委托模式、接口安全保障……阅读vue时可以分为框架设计、完整流程、响应式原理、MVVM模型实现、编译原理……哪个部分感兴趣就可以深入探索，整理成文方便自己回顾，会不知不觉地学习到一些优秀的代码架构设计思维和优秀的技巧。少年啊，Fighting！！！

### 架构设计

### MVVM实现
我们先实现一个小巧简单的mvvm框架吧~ Let's do it！

`index.html`（使用我们自己的mvvm框架）
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <div id="app">
        <!-- 双向数据绑定 -->
        <input type="text" v-model='msg'> {{msg}}
    </div>
</body>
<!-- <script src="node_modules/vue/dist/vue.min.js"></script> -->
<script src="./watcher.js"></script>
<script src="./observer.js"></script>
<script src="./compile.js"></script>
<script src="./mvvm.js"></script>
<script>
    // mvvm如何实现？
    // vue中实现双向绑定 1.模板编译 2.数据劫持 3.Watcher
    let vm = new MVVM({
        el: '#app', //el:document.getElementById('app')
        data: {
            msg: 'hello'
        }
    })
</script>

</html>
```

`mvvm.js`
```js
class MVVM {
    constructor(options) {
        // 先把可用的东西挂载到实例上
        this.$el = options.el
        this.$data = options.data

        // 如果有要编译的模板就开始编译
        if (this.$el) {
            // 数据劫持，把对象的所有属性改为get、set
            new Observer(this.$data)
            this.proxyData(this.$data)
                // 用数据和元素进行编译
            new Compile(this.$el, this)
        }
    }

    // 代理数据，因为用户可能要通过this.msg取值，而不是this.$data.msg取值
    proxyData(data) {
        Object.keys(data).forEach(key => {
            Object.defineProperty(this, key, {
                get() {
                    return data[key]
                },
                set(newVal) {
                    data[key] = newVal
                }
            })
        })
    }
}
```

`compile.js`
```js
class Compile {
    constructor(el, vm) {
        this.el = this.isElementNode(el) ? el : document.querySelector(el)
        this.vm = vm
        if (this.el) {
            // 1. 先把真实的dom移入到内存，放到fragment
            let fragment = this.node2Fragment(this.el)

            // 2. 编译——提取想要的元素节点和文本节点 v-model {{}}
            this.compile(fragment)

            // 3. 把编译好的element放回页面
            this.el.appendChild(fragment)
        }
    }

    // 辅助方法

    isElementNode(node) {
        return node.nodeType === 1
    }
    isDirective(name) {
        return name.includes('v-')
    }

    // 核心方法

    // 将el元素内容全部放入内存
    node2Fragment(el) {
        let fragment = document.createDocumentFragment() //文档碎片
        let firstChild
        while (firstChild = el.firstChild) {
            fragment.appendChild(firstChild)
        }
        return fragment
    }

    // 编译
    compile(fragment) {
        // childNodes拿不到嵌套子节点，需要使用递归
        let childNodes = fragment.childNodes
        Array.from(childNodes).forEach(node => {
            // 元素节点
            if (this.isElementNode(node)) {
                this.compileElement(node)
                this.compile(node) // 需要深入检查， 使用递归
            } else {
                // 文本节点
                this.compileText(node)
            }
        })
    }

    // 编译元素 v-model、v-text等
    compileElement(node) {
        let attrs = node.attributes
        Array.from(attrs).forEach(attr => {
            // 判断属性名字是否包含v-
            let attrName = attr.name
            if (this.isDirective(attrName)) {
                let expr = attr.value //expr是指令的值
                    // node this.vm.$data expr
                    //取到v-后面的名称，如v-model的model，v-text的text等等
                    // let type = attrName.slice(2)
                let [, type] = attrName.split('-')
                Compileutil[type](node, this.vm, expr)
            }
        })
    }

    // 编译文本，{{}}
    compileText(node) {
        let expr = node.textContent
        let reg = /\{\{([^}]+)\}\}/g //匹配{{}}
        if (reg.test(expr)) {
            // node this.vm.$data expr
            const type = 'text'
            Compileutil[type](node, this.vm, expr)
        }
    }
}

Compileutil = {
    // 获取实例上对应的数据，如msg.a.b=>'hello'
    // msg.a.b=>this.$data.msg=>this.$data.msg.a=>this.$data.msg.a.b
    getVal(vm, expr) {
        expr = expr.split('.')
        return expr.reduce((prev, next) => {
            return prev[next]
        }, vm.$data)
    },
    // 获取编译文本后的结果，如{{msg}}=>'hello'
    getTextVal(vm, expr) {
        return expr.replace(/\{\{([^}]+)\}\}/g, (...arguments) => {
            // arguments[1]是正则匹配括号内容，如{{msg}}的msg
            return this.getVal(vm, arguments[1])
        })
    },
    // 赋值
    // 例如给msg.a.b赋新值，则取到最后再赋value值
    setVal(vm, expr, value) {
        expr = expr.split('.')

        return expr.reduce((prev, next, curIndex) => {
            if (curIndex === expr.length - 1) {
                return prev[next] = value
            }
        }, vm.$data)
    },
    // 文本处理
    text(node, vm, expr) {
        let updateFn = this.update['textUpdater']

        // 拿到{{a}}{{b}}的a、b
        expr.replace(/\{\{([^}]+)\}\}/g, (...arguments) => {
            new Watcher(vm, arguments[1], newVal => {
                // 如果数据变化了， 文本节点需要重新获取依赖的数据来更新文本节点
                updateFn && updateFn(node, this.getTextVal(vm, expr))
            })
        })

        let value = this.getTextVal(vm, expr)
        updateFn && updateFn(node, value)
    },
    // 输入框处理 
    model(node, vm, expr) {
        let updateFn = this.update['modelUpdater']
            // 这里应该加一个监控，数据变化时，应该调用watcher的callback，将新值传递过来
        new Watcher(vm, expr, newVal => {
            updateFn && updateFn(node, this.getVal(vm, expr))
        })
        updateFn && updateFn(node, this.getVal(vm, expr))
        node.addEventListener('input', e => {
            let newVal = e.target.value
            this.setVal(vm, expr, newVal)
        })
    },
    update: {
        // 文本更新
        textUpdater(node, value) {
            node.textContent = value
        },
        // 输入框更新
        modelUpdater(node, value) {
            node.value = value
        },
    }
}
```

`observer.js`
```js
class Observer {
    constructor(data) {
        this.observe(data)
    }

    // 将data数据原有的属性改为get和set的形式
    observe(data) {
        if (!data || typeof data !== 'object') return
        Object.keys(data).forEach(key => {
            // 开始劫持
            this.defineReactive(data, key, data[key])
                // 如果劫持的是对象，还要对对象内的属性继续劫持
            this.observe(data[key])
        })
    }

    // 定义响应式
    defineReactive(data, key, value) {
        let _this = this
        let dep = new Dep() //每个变化的数据都会对应一个数组，这个数组是存放所有更新的操作
        Object.defineProperty(data, key, {
            enumerable: true,
            configurable: true,
            get() {
                Dep.target && dep.addSub(Dep.target)
                return value
            },
            set(newValue) {
                if (newValue !== value) {
                    // 设置新值时，如果是对象仍然需要劫持
                    _this.observe(newValue)
                    value = newValue
                    dep.notify() //通知所有订阅者数据变化了
                }
            }
        })
    }
}
```

`watcher.js`
```js
// 观察者，给需要变化的dom元素增加观察者
// 用新值和旧值进行比对，如果发生变化，执行对应的方法

class Watcher {
    constructor(vm, expr, cb) {
        this.vm = vm
        this.expr = expr
        this.cb = cb

        this.value = this.get()
    }

    // 获取实例上对应的数据，如msg.a.b=>'hello'
    getVal(vm, expr) {
        expr = expr.split('.')
        return expr.reduce((prev, next) => {
            return prev[next]
        }, vm.$data)
    }

    get() {
        Dep.target = this
        let value = this.getVal(this.vm, this.expr)
        Dep.target = null
        return value
    }

    // 对外暴露的方法
    update() {
        let newVal = this.getVal(this.vm, this.expr)
        let oldVal = this.value
        if (newVal !== oldVal) {
            this.cb(newVal)
        }
    }
}

// 发布订阅
class Dep {
    constructor() {
        // 订阅数组
        this.subs = []
    }

    // 添加订阅
    addSub(watcher) {
        this.subs.push(watcher)
    }

    notify() {
        this.subs.forEach(watcher => {
            watcher.update()
        })
    }
}
```

测试html页面，可以成功实现mvvm，view和model已经完成了双向绑定。下面让我们具体分析其中的细节：
1. vue双向数据绑定原理
2. vue响应式原理
3. vue生命钩子函数执行原理
4. vue模板编译渲染函数原理

### 双向数据绑定
一句话概括：Vue内部通过Object.defineProperty方法属性拦截的方式，把data对象里每个数据的读写转化成getter/setter，当数据变化时通知视图更新，如下图所示：

<img src="https://i.loli.net/2020/08/20/ZYeczjpvtfM18yP.png" alt="vue1" width="80%" />

首先，如何让数据对象变得可观测？我们可以使用Object.defineProperty方法，使数据变得可观测。举个栗子：

```js
/**
 * 把一个对象的每一项都转化成可观测对象
 * @param { Object } obj 对象
 */
function observable(obj) {
    if (!obj || typeof obj !== 'object') {
        return;
    }
    let keys = Object.keys(obj);
    keys.forEach((key) => {
        defineReactive(obj, key, obj[key])
    })
    return obj;
}
/**
 * 使一个对象转化成可观测对象
 * @param { Object } obj 对象
 * @param { String } key 对象的key
 * @param { Any } val 对象的某个key的值
 */
function defineReactive(obj, key, val) {
    Object.defineProperty(obj, key, {
        get() {
            console.log(`${key}属性被读取了`);
            return val;
        },
        set(newVal) {
            console.log(`${key}属性被修改了`);
            val = newVal;
        }
    })
}
```

通过调用observable方法，可以使得传入的obj对象任何属性变得可观测。

完成了数据的'可观测'，即我们知道了数据在什么时候被读或写了，那么，我们就可以在数据被读或写的时候通知那些依赖该数据的视图更新了，为了方便，我们需要先将所有依赖收集起来，一旦数据发生变化，就统一通知更新。其实，这就是典型的“发布订阅者”模式，数据变化为“发布者”，依赖对象为“订阅者”。

现在，我们需要创建一个依赖收集容器，也就是消息订阅器Dep，用来容纳所有的“订阅者”。订阅器Dep主要负责收集订阅者，然后当数据变化的时候后执行对应订阅者的更新函数。

**创建消息订阅器Dep:**
```js
class Dep {
    constructor() {
        this.subs = []
    }

    //增加订阅者
    addSub(sub) {
        this.subs.push(sub);
    }

    //判断是否增加订阅者
    depend() {
        if (Dep.target) {
            this.addSub(Dep.target)
        }
    }

    //通知订阅者更新
    notify() {
        this.subs.forEach((sub) => {
            sub.update()
        })
    }
}
Dep.target = null;
```

有了订阅器，再将defineReactive函数进行改造一下，向其植入订阅器：

```js
function defineReactive(obj, key, val) {
    let dep = new Dep();
    Object.defineProperty(obj, key, {
        get() {
            dep.depend();
            console.log(`${key}属性被读取了`);
            return val;
        },
        set(newVal) {
            val = newVal;
            console.log(`${key}属性被修改了`);
            dep.notify() //数据变化通知所有订阅者
        }
    })
}
```

我们设计了一个订阅器Dep类，该类里面定义了一些属性和方法，这里需要特别注意的是它有一个静态属性 target，这是一个**全局唯一的Watcher**。

我们将订阅器Dep添加订阅者的操作设计在getter里面，这是为了让Watcher初始化时进行触发，因此需要判断是否要添加订阅者。在setter函数里面，如果数据变化，就会去通知所有订阅者，订阅者们就会去执行对应的更新的函数。

接下来，我们设计订阅者Watcher：

```js
class Watcher {
    constructor(vm, exp, cb) {
        this.vm = vm; // 一个Vue的实例对象；
        this.exp = exp; //是node节点的v-model或v-on：click等指令的属性值。如v-model="name"，exp就是name;
        this.cb = cb; // 是Watcher绑定的更新函数;
        this.value = this.get(); // 将自己添加到订阅器的操作
    }
    update() {
        let value = this.vm.data[this.exp];
        let oldVal = this.value;
        if (value !== oldVal) {
            this.value = value;
            this.cb.call(this.vm, value, oldVal);
        }
    }
    get() {
        Dep.target = this; // 缓存自己
        let value = this.vm.data[this.exp] // 强制执行监听器里的get函数
        Dep.target = null; // 释放自己
        return value;
    }
}
```

开始测试：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
</head>

<body>
    <h1 id="name"></h1>
    <input type="text">
    <input type="button" value="改变data内容" onclick="changeInput()">

    <script src="observer.js"></script>
    <script src="watcher.js"></script>
    <script>
        function myVue(data, el, exp) {
            this.data = data;
            observable(data); //将数据变的可观测
            el.innerHTML = this.data[exp]; // 初始化模板数据的值
            new Watcher(this, exp, function(value) {
                el.innerHTML = value;
            });
            return this;
        }

        var ele = document.querySelector('#name');
        var input = document.querySelector('input');

        var myVue = new myVue({
            name: 'hello world'
        }, ele, 'name');

        //改变输入框内容
        input.oninput = function(e) {
            myVue.data.name = e.target.value
        }

        //改变data内容
        function changeInput() {
            myVue.data.name = "难凉热血"
        }
    </script>
</body>

</html>
```

### 响应式原理

数据发生变化后，会重新对页面渲染，这就是Vue响应式，那么这一切是怎么做到的呢？想完成这个过程，我们需要：
1. 侦测数据的变化
2. 收集视图依赖了哪些数据
3. 数据变化时，自动“通知”需要更新的视图部分，并进行更新

Vue 是如何将一个 plain object 给处理成 reactive object 的，也就是，Vue 是如何拦截拦截对象的 get/set 的？

**追踪变化：**

把一个普通JS对象传给Vue实例的data选项，Vue将遍历此对象所有的属性，并使用Object.defineProperty把这些属性全部转为getter/setter。每个组件实例都有相应的watcher实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。

<img src="https://i.loli.net/2020/08/20/261vpTAkhSMIDFY.png" alt="vue2" width="70%" />

<img src="https://i.loli.net/2020/08/20/tdAGzSqnph683e7.png" alt="vue3" width="70%" />

1. 在 new Vue() 后， Vue 会调用_init 函数进行初始化，也就是init 过程，在 这个过程Data通过Observer转换成了getter/setter的形式，来对数据追踪变化，当被设置的对象被读取的时候会执行getter 函数，而在当被赋值的时候会执行 setter函数。
2. 当外界通过Watcher读取数据时，会触发getter从而将Watcher添加到依赖中。
3. 在修改对象的值的时候，会触发对应的setter， setter通知之前依赖收集得到的 Dep 中的每一个 Watcher，告诉它们自己的值改变了，需要重新渲染视图。这时候这些 Watcher就会开始调用 update 来更新视图。

下面，我们来深入研究下以下几个问题：

**1. 如何侦测数据的变化？**
其实有两种办法可以侦测到变化：使用`Object.defineProperty`和ES6的`Proxy`，这就是进行数据劫持或数据代理。

`Object.defineProperty方式`
```js
function observe(obj) { // 我们来用它使对象变成可观察的
    // 判断类型
    if (!obj || typeof obj !== 'object') return
    Object.keys(obj).forEach(key => {
        defineReactive(obj, key, obj[key])
    })
}

function defineReactive(obj, key, value) {
    // 递归子属性
    observe(value)
    Object.defineProperty(obj, key, {
        enumerable: true, //可枚举（可以遍历）
        configurable: true, //可配置（比如可以删除）
        get: function reactiveGetter() {
            console.log('get', value) // 监听
            return value
        },
        set: function reactiveSetter(newVal) {
            observe(newVal) //如果赋值是一个对象，也要递归子属性
            if (newVal !== value) {
                console.log('set', newVal) // 监听
                render()
                value = newVal
            }
        }
    })
}
```
但是有几个问题是：
1. 无法检测到对象属性的添加或删除
2. Object.defineProperty 不能监听数组的变化，需要进行数组方法的重写

```proxy方式```
```js
let handler = {
    get(target, key) {
        // 如果取的值是对象就在对这个对象进行数据劫持
        if (typeof target[key] == 'object' && target[key] !== null) {
            return new Proxy(target[key], handler)
        }
        return Reflect.get(target, key)
    },
    set(target, key, value) {
        if (key === 'length') return true
        render()
        return Reflect.set(target, key, value)
    }
}

let proxy = new Proxy(obj, handler)
```

`Proxy` 的代理是针对整个对象的，而不是对象的某个属性，因此不同于 `Object.defineProperty` 的必须遍历对象每个属性，`Proxy` 只需要做一层代理就可以监听同级结构下的所有属性变化，当然对于深层结构，递归还是需要进行的。此外`Proxy`支持代理数组的变化。

**2. 收集视图依赖了哪些数据?**

收集依赖需要为依赖找一个存储依赖的地方，为此我们创建了Dep,它用来收集依赖、删除依赖和向依赖发送消息等。

先来实现一个订阅者 Dep 类，用于解耦属性的依赖收集和派发更新操作，说得具体点，它的主要作用是用来存放 Watcher 观察者对象。我们可以把Watcher理解成一个中介的角色，数据发生变化时通知它，然后它再通知其他地方。

```js
class Dep {
    constructor() {
        /* 用来存放Watcher对象的数组 */
        this.subs = [];
    }

    /* 在subs中添加一个Watcher对象 */
    addSub(sub) {
        this.subs.push(sub);
    }

    /* 通知所有Watcher对象更新视图 */
    notify() {
        this.subs.forEach((sub) => {
            sub.update();
        })
    }
}
```

- 用 `addSub` 方法可以在目前的 `Dep` 对象中增加一个 `Watcher` 的订阅操作；
- 用 `notify` 方法通知目前 `Dep` 对象的 `subs` 中的所有 `Watcher` 对象触发更新操作。

**3. 数据变化时，如何自动“通知”需要更新的视图部分，并进行更新？**

Vue 中定义一个 Watcher 类来表示观察订阅依赖。

<img src="https://i.loli.net/2020/08/20/3qNO1cjwxFrluUR.png" alt="vue4" width="60%" />

当属性发生变化后，我们要通知用到数据的地方，而使用这个数据的地方有很多，而且类型还不一样，既有可能是模板，也有可能是用户写的一个watch,这时需要抽象出一个能集中处理这些情况的类。然后，我们在依赖收集阶段只收集这个封装好的类的实例进来，通知也只通知它一个，再由它负责通知其他地方。

```js
class Watcher {
    constructor(obj, key, cb) {
        // 将 Dep.target 指向自己
        // 然后触发属性的 getter 添加监听
        // 最后将 Dep.target 置空
        Dep.target = this
        this.cb = cb
        this.obj = obj
        this.key = key
        this.value = obj[key]
        Dep.target = null
    }
    update() {
        // 获得新值
        this.value = this.obj[this.key]
            // 我们定义一个 cb 函数，这个函数用来模拟视图更新，调用它即代表更新视图
        this.cb(this.value)
    }
}
```

### 模板编译渲染函数原理

模板转换成浏览器认识的HTML过程如下：
1. template -> AST render （compiler解析template）
2. AST render -> vNode (render方法运行)
3. vNode -> DOM (vdom.patch)

<img src="https://i.loli.net/2020/08/20/iMeg3Z7AWp4BIqD.png" alt="vue5" width="80%" />

1. parse 函数，主要功能是将 template字符串解析成 AST,采用了 jQuery 作者 John Resig 的 HTML Parser。前面定义了ASTElement的数据结构，parse 函数就是将template里的结构（指令，属性，标签等）转换为AST形式存进ASTElement中，最后解析生成AST。
2. optimize 函数（src/compiler/optimizer.js）主要功能就是标记静态节点，为后面 patch 过程中对比新旧 VNode 树形结构做优化。被标记为 static 的节点在后面的 diff 算法中会被直接忽略，不做详细的比较。
3. generate 函数（src/compiler/codegen/index.js）主要功能就是根据 AST 结构拼接生成 render 函数的字符串。

下面我们来详细分析这三个函数：

**1. parse(解析器)**

Vue 通过下面几个正则表达式去匹配开始结束标签、标签名、属性等等。

```js
const attribute = /^\s*([^\s"'<>\/=]+)(?:\s*(=)\s*(?:"([^"]*)"+|'([^']*)'+|([^\s"'=<>`]+)))?/
const ncname = '[a-zA-Z_][\\w\\-\\.]*'
const qnameCapture = `((?:${ncname}\\:)?${ncname})`
const startTagOpen = new RegExp(`^<${qnameCapture}`)
const startTagClose = /^\s*(\/?)>/
const endTag = new RegExp(`^<\\/${qnameCapture}[^>]*>`)
const doctype = /^<!DOCTYPE [^>]+>/i
const comment = /^<!\--/
const conditionalComment = /^<!\[/
```

parse 函数就是不断的重复这个工作，然后将 template 转换成 AST，在解析过程中，其实对于标签与标签之间的空格，Vue 也做了优化处理，有些元素之间的空格是没用的。

**2. optimize(优化器)**
从代码中的注释我们可以看出，优化器的目的就是去找出 AST 中纯静态的子树：
- 把纯静态子树提升为常量，每次重新渲染的时候就不需要创建新的节点了
- 在 patch 的时候就可以跳过它们

```js
export function optimize (root: ?ASTElement, options: CompilerOptions) {
  // 判断 root 是否存在
  if (!root) return
  // 判断是否是静态的属性
  // 'type,tag,attrsList,attrsMap,plain,parent,children,attrs'
  isStaticKey = genStaticKeysCached(options.staticKeys || '')
  // 判断是否是平台保留的标签，html 或者 svg 的
  isPlatformReservedTag = options.isReservedTag || no
  // 第一遍遍历: 给所有静态节点打上是否是静态节点的标记
  markStatic(root)
  // 第二遍遍历:标记所有静态根节点
  markStaticRoots(root, false)
}
```

第一遍遍历： markStatic 就是一个递归的过程，不断地去检查 AST 上的节点，然后打上标记。
第二遍遍历：标记静态根节点，那么我们对静态根节点的定义是什么，首先根节点的意思就是他不能是叶子节点，起码要有子节点，并且它是静态的。在这里 Vue 做了一个说明，如果一个静态节点它只拥有一个子节点并且这个子节点是文本节点，那么就不做静态处理，它的成本大于收益，不如直接渲染。

**3. generate(代码生成器)**
在这个函数中，将 AST 转换成为 render 函数字符串。代码量还是挺多的，值得好好看看~

### vue生命周期钩子实现

**实例生命周期钩子**

每个Vue实例在被创建时都要经过一系列的初始化过程一一例如，需要设置数据监听、编译模板、 将实例挂载到DOM并在数据变化时更新DOM等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。比如created钩子可以用来在一个实例被创建之后执行代码，也有一些其它的钩子，在实例生命周期的不同阶段被调用，如mounted、updated和destroyed。生命周期钩子的this上下文指 向调用它的Vue实例。

**生命周期示意图**

<img src="https://i.loli.net/2020/08/20/z8TBS6bnH7Ihq3Z.png" alt="vue6" width="100%" />

### vue模板编译原理
模板编译过程经历了三个阶段：
1. 初始化
2. 选项处理
3. 编译器（词法分析）

要让外部拿到Vue，就必须在root（即window）上挂载Vue。

<img src="https://i.loli.net/2020/08/20/zaD8urN3qO2xpJS.png" alt="vue7" width="80%" />

其中的warn是不允许直接调用Vue函数，一定要创建实例。_init是选项处理。

我们来看一下vue源码中是如何实现的：

<img src="https://i.loli.net/2020/08/20/yj2Cx3QO98gXioK.png" alt="vue8" width="80%" />

<img src="https://i.loli.net/2020/08/20/wHSIW6nQCfzKoPu.png" alt="vue9" width="80%" />

看一下vue.$options.render是什么？

<img src="https://i.loli.net/2020/08/20/Vgmqjh4xCMBHNvJ.png" alt="vue10" width="80%" />

它是一个渲染函数，code是渲染函数所需要的字符串，接下来看vue中render函数是在哪里创建的：

<img src="https://i.loli.net/2020/08/20/mQuwx4rqEhKYMdT.png" alt="vue11" width="80%" />

<img src="https://i.loli.net/2020/08/20/CkewLj79ARrJ4bK.png" alt="vue12" width="80%" />

继续往下找compiled.render是什么：

<img src="https://i.loli.net/2020/08/20/aLkfc9ZMYDboQNm.png" alt="vue13" width="80%" />

休息一下，下面继续完善代码：

<img src="https://i.loli.net/2020/08/20/vGD98iyIMcZb4CR.png" alt="vue14" width="80%" />

我们看看vue.$options到底是什么东东：

<img src="https://i.loli.net/2020/08/20/qlrcfiVQKOkmY2B.png" alt="vue15" width="80%" />

由上图可知：vue.$options是一个对象，其中包括components、directives、filter等等，但是我们并没有传入这些属性，它是如何创建的呢，接下来我们在vue源码中一探究竟：

<img src="https://i.loli.net/2020/08/20/HeQ4PquTthGfxOS.png" alt="vue16" width="80%" />

Ctor.super是vue子类上才会有的一个属性，为什么需要校验Ctor.super呢？因为我们所有的组件都是vue子类的实例对象，在这里需要辨别是根实例还是组件的实例。

鼓足一口气，了解了原理后，我们添加以下几行代码：

<img src="https://i.loli.net/2020/08/20/8YEFcCsoBIZHS2r.png" alt="vue17" width="80%" />

<img src="https://i.loli.net/2020/08/20/94xBcH7ietUAOMK.png" alt="vue18" width="80%" />

为什么一定要拼接's'？为什么不能直接在数组中加上's'？因为vue还考虑到了复用性的问题，还要给其他地方进行使用。

接下来定义mergeOptions方法： 

<img src="https://i.loli.net/2020/08/20/PvqbGZsQyI3jCRJ.png" alt="vue19" width="80%" />

查看vue源码是如何实现的：

<img src="https://i.loli.net/2020/08/20/VhoRJ7pABiv5GCe.png" alt="vue20" width="80%" />

我们知道了函数基本输入和内容后，继续编写代码：

<img src="https://i.loli.net/2020/08/20/ez2RQGXfVHOoKkC.png" alt="vue21" width="80%" />

mergeOptions方法就是一个工具函数，为什么要命名为parent、child、vm呢？（有点奇怪）

因为在这里，我们不仅要处理根实例上选项规范检查、策略处理、选项的合并，还要处理组件里的这些选项。因为在组件中，parent指向的是此组件的父组件的options，child指向的是此组件的options，vm指向的是当前组件实例。

接下来完善mergeOptions函数：

<img src="https://i.loli.net/2020/08/20/eSNBOq2A3XCdycD.png" alt="vue22" width="80%" />

<img src="https://i.loli.net/2020/08/20/SrkjmEitObnhX87.png" alt="vue23" width="80%" />

这里让我想到了之前编写axios也实现过合并配置。 原则：以默认配置优先，以用户配置覆盖。

这里的strats是什么东东？

<img src="https://i.loli.net/2020/08/20/m7rNPHw5VXTWzOE.png" alt="vue24" width="80%" />

strats是所有的自定义策略的挂载点。

<img src="https://i.loli.net/2020/08/20/detEbwY2oRvVmOk.png" alt="vue25" width="80%" />

自定义策略最主要的目的是怎么生成当前选项的值。这里的key是el data components directives filters，检测有没有编写这些key对应的策略，如果有，则根据这些自定义策略的返回值来决定最终这个选项会生成什么样的结果。

利用vue的源码继续完善我们的代码：

<img src="https://i.loli.net/2020/08/20/6SB3LxQkUXWbE4e.png" alt="vue26" width="80%" />

在这里有个疑惑，为什么不能直接使用var strats={}创建对象呢？查看vue官网：

<img src="https://i.loli.net/2020/08/20/qS8bBrTpR1gQc5U.png" alt="vue27" width="80%" />

原来这是为了给用户为额外传入的属性进行自定义配置的接口。

问题又来了，Vue.config.optiomMergeStr...为什么能够直接调用Vue.config.xx的呢？难道是vue向外暴露的config？

<img src="https://i.loli.net/2020/08/20/PMULkdthKcnW7vs.png" alt="vue28" width="80%" />

读一读vue源码：

<img src="https://i.loli.net/2020/08/20/XVWuTHIhqYEjA5J.png" alt="vue29" width="80%" />

可见不能修改config，只能获取config。

因此我们可以明白为什么要绕一圈写，不直接写strats={}的原因了：因为Vue.config.optiomMergeStrategies是允许自定义策略扩展的接口，用户可以自己配置，让功能变得更加强大。 

<img src="https://i.loli.net/2020/08/20/Ge5ICV6hlJknr8B.png" alt="vue30" width="80%" />

接下来来到最后一步了：编译器的实现

<img src="https://i.loli.net/2020/08/20/KWUaJqlyztLXjGM.png" alt="vue31" width="80%" />

需要实现parse、optimize、generate

**如何追踪变化：**

当你把一个普通的js对象传入Vue实例作为data选项，Vue将遍历此对象所有的属性, 并使用`Object.defineProperty`把这些属性全部转为getter/setter。这些getter/setter对用户来说是不可见的，但是在内部它们让Vue能够追踪依赖，在属性被访问和修改时通知变更。每个组件实例都对应一个watcher实例，它会在组件渲染的过程中把“接触”过的数据属性记录为依赖。之后当依赖项的setter触发时，会通知watcher,从而使它关联的组件重新渲染。

**相应原理：**

<img src="https://i.loli.net/2020/08/20/g2BIceL4bzTji6o.png" alt="vue32" width="60%" />

**怎么把模板编译为virtual DOM？**

分词示例：
```
<div id="app" v-if=’ret"> {{root}}</div>

标答：div...
指令：v-if...
B性：id...
标识符：{{}}插值 
token “分词”
```

<img src="https://i.loli.net/2020/08/20/91ovXYpjFbgzsEd.png" alt="vue33" width="80%" />

可见是一系列的正则文法，哪些是标签、属性、指令…… 词法分词具体看parseHTML函数

<img src="https://i.loli.net/2020/08/20/YwVsgQrvfypd6FW.png" alt="vue34" width="80%" />

做完词法分析后，下一步是做语法分析：抽象语法树AST（待补充……）