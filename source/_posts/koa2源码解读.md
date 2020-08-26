---
title: koa2源码解读
date: 2020-08-22
categories: 源码
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

### 关于如何读源码
曾经，我去阅读源码时，总是喜欢一句不漏的从上到下阅读，怕漏掉什么核心代码导致不理解程序的详细流程，遇到一个函数或类就跳过去看，然后……花费了很多的时间而且效果相当不理想。不仅对整体的框架没有大体了解，也让自己像只无头苍蝇，不知道如何抓住重点去深入研究背后的原理。后来看了不少大佬阅读的方式，对我也是收获颇多：不要采用DFS（深度遍历）的方式阅读源码，而是应该先理清框架的架构，再从入口出发，依照流程读下去。这个过程首先应该对框架有一个整体的了解，建议画个流程图或思维脑图，掌握源码的整体处理流程。接下来可以针对源码功能进行个分类，进行深入研究：如阅读koa时，可以分为框架设计和基础结构、中间件实现机制（洋葱模型）、同一错误处理、委托模式、接口安全保障……阅读vue时可以分为框架设计、完整流程、响应式原理、MVVM模型实现、编译原理……哪个部分感兴趣就可以深入探索，整理成文方便自己回顾，会不知不觉地学习到一些优秀的代码架构设计思维和优秀的技巧。少年啊，Fighting！！！

### koa源码设计
#### koa的基础结构
koa是一个精简的node框架：
- 基于node原生req和res为request和response对象赋能，并基于它们封装成一个context对象。
- 基于async/await（generator）的中间件洋葱模型机制。

```
── lib
   ├── application.js
   ├── context.js
   ├── request.js
   └── response.js

── lib
   ├── new Koa()  || ctx.app
   ├── ctx
   ├── ctx.req  || ctx.request
   └── ctx.res  || ctx.response
```

<img src="https://i.loli.net/2020/08/20/zhWHZTo6nmLqcuj.png" alt="koa架构" width="100%" />

#### koa源码基础骨架
`application.js`
application.js是koa的主入口，也是核心部分，主要干了以下几件事情：
1. 启动框架
2. 实现洋葱模型中间件机制
3. 封装高内聚的context
4. 实现异步函数的统一错误处理机制

`context.js`
context.js主要干了两件事情：
1. 错误事件处理
2. 代理response对象和request对象的部分属性和方法

`request.js`
request对象基于node原生req封装了一系列便利属性和方法，供处理请求时调用。所以当你访问ctx.request.xxx的时候，实际上是在访问request对象上的赋值器（setter）和取值器（getter）。

`response.js`
response对象基于node原生res封装了一系列便利属性和方法，供处理请求时调用。所以当你访问ctx.response.xxx的时候，实际上是在访问response对象上的赋值器（setter）和取值器（getter）。

4个文件的代码结构如下：

<img src="https://i.loli.net/2020/08/20/M2mh9OlVn1xU6pC.png" alt="koa代码结构1" width="100%" />

<img src="https://i.loli.net/2020/08/20/cSXONHWAEr4ofJ6.png" alt="koa代码结构2" width="100%" />

### koa工作流

**Koa整个流程可以分成三步:**
1. 初始化阶段
new初始化一个实例，use添加中间件到middleware数组，listen 合成中间件fnMiddleware，返回一个callback函数给http.createServer，开启服务器，等待http请求。
2. 请求阶段
每次请求，createContext生成一个新的ctx，传给fnMiddleware，触发中间件的整个流程。
3. 响应阶段
整个中间件完成后，调用respond方法，对请求做最后的处理，返回响应给客户端。

### koa中间件机制与实现
koa中间件机制是采用koa-compose实现的，compose函数接收middleware数组作为参数，middleware中每个对象都是async函数，返回一个以context和next作为入参的函数，我们跟源码一样，称其为fnMiddleware在外部调用this.handleRequest的最后一行，运行了中间件：`fnMiddleware(ctx).then(handleResponse).catch(onerror);`

<img src="https://i.loli.net/2020/08/20/5AdrkLY7UVQ2y3i.png" alt="koa中间件机制" width="100%" />

<img src="https://i.loli.net/2020/08/20/MCoFGjgJHyUhnrR.png" alt="koa中间件机制2" width="100%" />

### 异步函数的统一错误处理机制
1. 中间件捕获
2. 框架捕获

<img src="https://i.loli.net/2020/08/20/xIJ1moV9iKnXczh.png" alt="koa错误捕获" width="100%" />

### 委托模式在koa中的应用
delegates库由大名鼎鼎的 TJ 所写，可以帮我们方便快捷地使用设计模式当中的委托模式（Delegation Pattern），即外层暴露的对象将请求委托给内部的其他对象进行处理

```
delegates 基本用法就是将内部对象的变量或者函数绑定在暴露在外层的变量上，直接通过 delegates 方法进行如下委托，基本的委托方式包含：
getter：外部对象可以直接访问内部对象的值
setter：外部对象可以直接修改内部对象的值
access：包含 getter 与 setter 的功能
method：外部对象可以直接调用内部对象的函数
```

delegates 原理就是__defineGetter__和__defineSetter__
method是委托方法，getter委托getter,access委托getter和setter。

在application.createContext函数中，
被创建的context对象会挂载基于request.js实现的request对象和基于response.js实现的response对象。
下面2个delegate的作用是让context对象代理request和response的部分属性和方法

context.request的许多属性都被委托在context上了
context.response的许多方法都被委托在context上了

为什么response.js和request.js使用get set代理，而context.js使用delegate代理?
原因主要是: set和get方法里面还可以加入一些自己的逻辑处理。而delegate就比较纯粹了，只代理属性。

<img src="https://i.loli.net/2020/08/20/zBUQwGZKtL4vAHV.png" alt="koa委托" width="80%" />

### koa接口安全

<img src="https://i.loli.net/2020/08/20/ICRGLY5a9Vlswri.png" alt="koa接口安全" width="100%" />

### koa2源码+注释展示
`application.js`

```js
'use strict';

// Koa整个流程可以分成三步:
// 【1.初始化阶段】
// new初始化一个实例，use添加中间件到middleware数组，listen 合成中间件fnMiddleware，返回一个callback函数给http.createServer，开启服务器，等待http请求。
// 【2.请求阶段】
// 每次请求，createContext生成一个新的ctx，传给fnMiddleware，触发中间件的整个流程。
// 【3.响应阶段】
// 整个中间件完成后，调用respond方法，对请求做最后的处理，返回响应给客户端。

// application.js是koa的主入口，也是核心部分，主要干了以下几件事情：
// 1. 启动框架
// 2. 实现洋葱模型中间件机制
// 3. 封装高内聚的context
// 4. 实现异步函数的统一错误处理机制

const isGeneratorFunction = require('is-generator-function');
const debug = require('debug')('koa:application');
const onFinished = require('on-finished');
const response = require('./response');
const compose = require('koa-compose');
const context = require('./context');
const request = require('./request');
const statuses = require('statuses');
const Emitter = require('events');
const util = require('util');
const Stream = require('stream');
const http = require('http');
const only = require('only');
const convert = require('koa-convert');
const deprecate = require('depd')('koa');
const {
    HttpError
} = require('http-errors');

// 当实例化koa的时候，koa做了以下2件事：
// 继承Emitter，具备处理异步事件的能力。然而koa是如何处理，现在还不得而知，这里打个问号。
// 在创建实例过程中，有三个对象作为实例的属性被初始化，分别是context、request、response。还有我们熟悉的存放中间件的数组mddleware。这里需要注意，是使用Object.create(xxx)对this.xxx进行赋值。

module.exports = class Application extends Emitter {
    constructor(options) {
        super();
        options = options || {};
        this.proxy = options.proxy || false;
        this.subdomainOffset = options.subdomainOffset || 2;
        this.proxyIpHeader = options.proxyIpHeader || 'X-Forwarded-For';
        this.maxIpsCount = options.maxIpsCount || 0;
        this.env = options.env || process.env.NODE_ENV || 'development';
        if (options.keys) this.keys = options.keys;

        this.middleware = []; //中间件数组
        // 通过context.js、request.js、response.js创建对应的context、request、response
        // Object.create(xxx)作用：根据xxx创建一个新对象，并且将xxx的属性和方法作为新的对象的proto。
        this.context = Object.create(context);
        this.request = Object.create(request);
        this.response = Object.create(response);
        // 以context为例，其实是创建一个新对象，使用context对象来提供新创建对象的proto，并且将这个对象赋值给this.context，实现了类继承的作用。为什么不直接用this.context=context呢？这样会导致两者指向同一片内存，而不是实现继承的目的。

        if (util.inspect.custom) {
            this[util.inspect.custom] = this.inspect;
        }
    }

    // 创建服务器
    // 这里使用了node原生http.createServer创建服务器，并把this.callback()作为参数传递进去。可以知道，this.callback()返回的一定是这种形式：(req, res) => {}。
    listen(...args) {
        debug('listen');
        const server = http.createServer(this.callback()); //this.callback()是需要重点关注的部分，其实对应了http.createServer的参数(req, res)=> {}
        return server.listen(...args);
    }

    toJSON() {
        return only(this, [
            'subdomainOffset',
            'proxy',
            'env'
        ]);
    }

    inspect() {
        return this.toJSON();
    }

    // 通过调用koa应用实例的use函数
    // 当我们执行app.use的时候，koa做了这2件事情：
    // 1. 判断是否是generator函数，如果是，使用koa-convert做转换（koa3将不再支持generator）。
    // 2. 所有传入use的方法，会被push到middleware中。
    use(fn) {
        if (typeof fn !== 'function') throw new TypeError('middleware must be a function!');
        // koa2处于对koa1版本的兼容，中间件函数如果是generator函数的话，会使用koa-convert进行转换为“类async函数”。
        if (isGeneratorFunction(fn)) {
            // 如何将generator函数转为类async函数?
            // generator和async有什么区别？唯一的区别就是async会自动执行，而generator每次都要调用next函数。
            // 如何让generator自动执行next函数？我们只要找到一个合适的方法让g.next()一直持续下去就可以自动执行了。
            // 所以问题的关键在于yield的value必须是一个Promise。那么我们来看看co是如何把这些都东西都转化为Promise的：
            // 【co的思想】
            // 把一个generator封装在一个Promise对象中，然后再这个Promise对象中再次把它的gen.next()也封装出Promise对象，相当于这个子Promise对象完成的时候也重复调用gen.next()。当所有迭代完成时，把父Promise对象resolve掉。这就成了一个类async函数了。
            deprecate('Support for generators will be removed in v3. ' +
                'See the documentation for examples of how to convert old middleware ' +
                'https://github.com/koajs/koa/blob/master/docs/migration.md');
            fn = convert(fn);
        }
        debug('use %s', fn._name || fn.name || '-');
        this.middleware.push(fn);
        return this;
    }

    // 返回一个类似(req, res) => {}的函数，该函数会作为参数传递给上文的listen函数中的http.createServer函数，作为请求处理的函数

    // 1. compose(this.middleware)做了什么事情（使用了koa-compose包）
    // 2. 如何实现洋葱式调用的？
    // 3. context是如何处理的？createContext的作用是什么？
    // 4. koa的统一错误处理机制是如何实现的？
    callback() {
        const fn = compose(this.middleware); // 将所有传入use的函数通过koa-compose组合一下
        // Koa使用了koa-compose实现了中间件机制，源码如下：

        function compose(middleware) {
            return function(context, next) {
                let index = -1
                return dispatch(0)

                function dispatch(i) {
                    if (i <= index) return Promise.reject(new Error('next() called multiple times'))
                    index = i
                    let fn = middleware[i]
                    if (i === middleware.length) fn = next
                    if (!fn) return Promise.resolve()
                    try {
                        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
                    } catch (err) {
                        return Promise.reject(err)
                    }
                }
            }
        }

        // compose函数接收middleware数组作为参数，middleware中每个对象都是async函数，返回一个以context和next作为入参的函数，我们跟源码一样，称其为fnMiddleware
        // 在外部调用this.handleRequest的最后一行，运行了中间件：fnMiddleware(ctx).then(handleResponse).catch(onerror);

        // 【next到底是啥？洋葱模型是怎么实现的？】
        // next就是一个包裹了dispatch的函数
        // 在第n个中间件中执行next，就是执行dispatch(n+1)，也就是进入第n+1个中间件
        // 因为dispatch返回的都是Promise，所以在第n个中间件await next(); 进入第n+1个中间件。
        // 当第n+1个中间件执行完成后，可以返回第n个中间件
        // 如果在某个中间件中不再调用next，那么它之后的所有中间件都不会再调用了


        // 中间件执行顺序原理：
        // 0：fnMiddleware(ctx)运行；
        // 0： 执行dispatch(0)；
        // 0： 进入dispatch函数，此时，fn就是第一个中间件，它是一个async函数，async函数会返回一个Promise对象，Promise.resolve()中若传入一个Promise对象的话，那么Promise.resolve将原封不动地返回这个Promise对象。
        // 0：进入到第一个中间件代码内部，先执行“console.log(“1-start”)”
        // 0：然后执行“await next()”，并开始等待next执行返回
        // 1：进入到next函数后，执行的是dispatch(1)，于是老的dispatch(0)压栈，开始从头执行dispatch(1)，即把第二个中间件函数交给fn，然后开始执行，这就完成了程序的控制权从第一个中间件到第二个中间件的转移。下图是执行dispatch(1)时函数内变量的值：
        // 1：进入到第二个中间件代码内部，先执行“console.log(“2-start”)”。然后执行“await next()”并等待next执行返回
        // 2：进入next函数后，主要执行dispatch(2)，于是老的dispatch(1)压栈，从头开始执行dispatch(2)。返回Promise.resolve()， 此时第二个中间件的next函数返回了。
        // 2： 所以接下来执行“ console.log(“2 - end”)”
        // 1： 由此第二个中间件执行完成， 把程序控制权交给第一个中间件。 第一个中间件执行“ console.log(“1 - end”)”
        // 0： 终于完成了所有中间件的执行， 如果中间没有异常， 则返回Promise.resolve()， 执行handleResponse回调； 如有异常， 则返回Promies.reject(err)， 执行onerror回调。

        if (!this.listenerCount('error')) this.on('error', this.onerror);

        const handleRequest = (req, res) => {
            const ctx = this.createContext(req, res); //  基于req、res封装出更强大的ctx
            return this.handleRequest(ctx, fn);
        };

        return handleRequest;
    }


    handleRequest(ctx, fnMiddleware) {
        const res = ctx.res;
        res.statusCode = 404;

        // application.js也有onerror函数，但这里使用了context的onerror，
        // 出错执行的回调函数是context.js的onerror函数，因为使用了this.app.emit('error', err, this)，因此在app上监听onerror事件，就能处理所有中间件的错误
        const onerror = err => ctx.onerror(err);
        const handleResponse = () => respond(ctx);
        onFinished(res, onerror);

        // 这里是中间件如果执行出错的话，都能执行到onerror的关键！！！

        // Koa异常捕获的两种方式：
        // 1. 中间件捕获(Promise catch)，如以下代码 fnMiddleware(ctx).then(handleResponse).catch(onerror)
        // 捕获全局异常的中间件
        // app.use(async(ctx, next) => {
        //         try {
        //             await next()
        //         } catch (error) {
        //             return ctx.body = 'error'
        //         }
        //     })
        // 2. 框架捕获(Emitter error)，Application继承原生的Emitter，从而实现error监听
        // 事件监听
        // app.on('error', err => {
        //     console.log('error happends: ', err.stack);
        // });

        // koa为什么能实现异步函数的统一错误处理？
        // 1. sync函数返回一个Promise对象
        // 2. async函数内部抛出错误，会导致Promise对象变为reject状态。抛出的错误会被catch的回调函数(上面为onerror)捕获到。
        // 3. await命令后面的Promise对象如果变为reject状态，reject的参数也可以被catch的回调函数(上面为onerror)捕获到。
        return fnMiddleware(ctx).then(handleResponse).catch(onerror);
    }

    // context使用node原生的http监听回调函数中的req、res来进一步封装，意味着对于每一个http请求，koa都会创建一个context并共享给所有的全局中间件使用，当所有的中间件执行完后，会将所有的数据统一交给res进行返回。所以，在每个中间件中我们才能取得req的数据进行处理，最后ctx再把要返回的body给res进行返回。
    // 请记住句话：每一个请求都有唯一一个context对象，所有的关于请求和响应的东西都放在其里面。

    createContext(req, res) {
        // context必须作为一个临时对象存在，所有的东西都必须放进一个对象
        // 使用了Object.create的方法创建一个全新的对象，通过原型链继承原来的属性。这样可以有效的防止污染原来的对象。
        const context = Object.create(this.context);
        const request = context.request = Object.create(this.request);
        const response = context.response = Object.create(this.response);

        // 为什么app、req、res、ctx也存放在了request、和response对象中呢？
        // 使它们同时共享一个app、req、res、ctx，是为了将处理职责进行转移，当用户访问时，只需要ctx就可以获取koa提供的所有数据和方法，而koa会继续将这些职责进行划分，比如request是进一步封装req的，response是进一步封装res的，这样职责得到了分散，降低了耦合度，同时共享所有资源使context具有高内聚的性质，内部元素互相能访问到。
        context.app = request.app = response.app = this;
        context.req = request.req = response.req = req;
        context.res = request.res = response.res = res;
        request.ctx = response.ctx = context;
        request.response = response;
        response.request = request;
        context.originalUrl = request.originalUrl = req.url;
        context.state = {}; //这里的state是专门负责保存单个请求状态的空对象，可以根据需要来管理内部内容。
        return context;
    }

    onerror(err) {
        const isNativeError =
            Object.prototype.toString.call(err) === '[object Error]' ||
            err instanceof Error;
        if (!isNativeError) throw new TypeError(util.format('non-error thrown: %j', err));

        if (404 === err.status || err.expose) return;
        if (this.silent) return;

        const msg = err.stack || err.toString();
        console.error(`\n${msg.replace(/^/gm, '  ')}\n`);
    }
};

function respond(ctx) {
    if (false === ctx.respond) return;

    if (!ctx.writable) return;

    const res = ctx.res;
    let body = ctx.body;
    const code = ctx.status;

    if (statuses.empty[code]) {
        // strip headers
        ctx.body = null;
        return res.end();
    }

    if ('HEAD' === ctx.method) {
        if (!res.headersSent && !ctx.response.has('Content-Length')) {
            const {
                length
            } = ctx.response;
            if (Number.isInteger(length)) ctx.length = length;
        }
        return res.end();
    }

    if (null == body) {
        if (ctx.response._explicitNullBody) {
            ctx.response.remove('Content-Type');
            ctx.response.remove('Transfer-Encoding');
            return res.end();
        }
        if (ctx.req.httpVersionMajor >= 2) {
            body = String(code);
        } else {
            body = ctx.message || String(code);
        }
        if (!res.headersSent) {
            ctx.type = 'text';
            ctx.length = Buffer.byteLength(body);
        }
        return res.end(body);
    }

    if (Buffer.isBuffer(body)) return res.end(body);
    if ('string' === typeof body) return res.end(body);
    if (body instanceof Stream) return body.pipe(res);

    body = JSON.stringify(body);
    if (!res.headersSent) {
        ctx.length = Buffer.byteLength(body);
    }
    res.end(body);
}

module.exports.HttpError = HttpError;
```

`context.js`
```js
'use strict';

// context.js主要干了两件事情：
// 1. 错误事件处理
// 2. 代理response对象和request对象的部分属性和方法

const util = require('util');
const createError = require('http-errors');
const httpAssert = require('http-assert');
const delegate = require('delegates');
const statuses = require('statuses');
const Cookies = require('cookies');
const context = require('./2 req-res-application封装/context');

const COOKIES = Symbol('context#cookies');


const proto = module.exports = {
    // context自身的方法
    inspect() {
        if (this === proto) return this;
        return this.toJSON();
    },

    toJSON() {
        return {
            request: this.request.toJSON(),
            response: this.response.toJSON(),
            app: this.app.toJSON(),
            originalUrl: this.originalUrl,
            req: '<original node req>',
            res: '<original node res>',
            socket: '<original node socket>'
        };
    },

    assert: httpAssert,
    throw (...args) {
        throw createError(...args);
    },

    onerror(err) {
        if (null == err) return;
        const isNativeError =
            Object.prototype.toString.call(err) === '[object Error]' ||
            err instanceof Error;
        if (!isNativeError) err = new Error(util.format('non-error thrown: %j', err));

        let headerSent = false;
        if (this.headerSent || !this.writable) {
            headerSent = err.headerSent = true;
        }
        // 这里的this.app是对application的引用，当context.js调用onerror时，其实是触发application实例的error事件 。该事件是基于“Application类继承自EventEmitter”这一事实。
        this.app.emit('error', err, this);
        if (headerSent) {
            return;
        }

        const {
            res
        } = this;
        if (typeof res.getHeaderNames === 'function') {
            res.getHeaderNames().forEach(name => res.removeHeader(name));
        } else {
            res._headers = {}; // Node < 7.7
        }

        // then set those specified
        this.set(err.headers);

        // force text/plain
        this.type = 'text';

        let statusCode = err.status || err.statusCode;

        // ENOENT support
        if ('ENOENT' === err.code) statusCode = 404;

        // default to 500
        if ('number' !== typeof statusCode || !statuses[statusCode]) statusCode = 500;

        // respond
        const code = statuses[statusCode];
        const msg = err.expose ? err.message : code;
        this.status = err.status = statusCode;
        this.length = Buffer.byteLength(msg);
        res.end(msg);
    },

    get cookies() {
        if (!this[COOKIES]) {
            this[COOKIES] = new Cookies(this.req, this.res, {
                keys: this.app.keys,
                secure: this.request.secure
            });
        }
        return this[COOKIES];
    },

    set cookies(_cookies) {
        this[COOKIES] = _cookies;
    }
};


if (util.inspect.custom) {
    module.exports[util.inspect.custom] = module.exports.inspect;
}

// 委托模式

// delegates库由大名鼎鼎的 TJ 所写，可以帮我们方便快捷地使用设计模式当中的委托模式（Delegation Pattern），即外层暴露的对象将请求委托给内部的其他对象进行处理

// delegates 基本用法就是将内部对象的变量或者函数绑定在暴露在外层的变量上，直接通过 delegates 方法进行如下委托，基本的委托方式包含：
// getter：外部对象可以直接访问内部对象的值
// setter：外部对象可以直接修改内部对象的值
// access：包含 getter 与 setter 的功能
// method：外部对象可以直接调用内部对象的函数

// delegates 原理就是__defineGetter__和__defineSetter__
// method是委托方法，getter委托getter,access委托getter和setter。

// 在application.createContext函数中，
// 被创建的context对象会挂载基于request.js实现的request对象和基于response.js实现的response对象。
// 下面2个delegate的作用是让context对象代理request和response的部分属性和方法

// context.request的许多属性都被委托在context上了
// context.response的许多方法都被委托在context上了

// 为什么response.js和request.js使用get set代理，而context.js使用delegate代理?
// 原因主要是: set和get方法里面还可以加入一些自己的逻辑处理。而delegate就比较纯粹了，只代理属性。

delegate(proto, 'response')
    .method('attachment')
    .method('redirect')
    .method('remove')
    .method('vary')
    .method('has')
    .method('set')
    .method('append')
    .method('flushHeaders')
    .access('status')
    .access('message')
    .access('body')
    .access('length')
    .access('type')
    .access('lastModified')
    .access('etag')
    .getter('headerSent')
    .getter('writable');

delegate(proto, 'request')
    .method('acceptsLanguages')
    .method('acceptsEncodings')
    .method('acceptsCharsets')
    .method('accepts')
    .method('get')
    .method('is')
    .access('querystring')
    .access('idempotent')
    .access('socket')
    .access('search')
    .access('method')
    .access('query')
    .access('path')
    .access('url')
    .access('accept')
    .getter('origin')
    .getter('href')
    .getter('subdomains')
    .getter('protocol')
    .getter('host')
    .getter('hostname')
    .getter('URL')
    .getter('header')
    .getter('headers')
    .getter('secure')
    .getter('stale')
    .getter('fresh')
    .getter('ips')
    .getter('ip');
```

`request.s`

```js
'use strict';

const URL = require('url').URL;
const net = require('net');
const accepts = require('accepts');
const contentType = require('content-type');
const stringify = require('url').format;
const parse = require('parseurl');
const qs = require('querystring');
const typeis = require('type-is');
const fresh = require('fresh');
const only = require('only');
const util = require('util');

const IP = Symbol('context#ip');

module.exports = {

    // 在application.js的createContext函数中，会把node原生的req作为request对象(即request.js封装的对象)的属性
    // request对象会基于req封装很多便利的属性和方法

    // 大量类似的工具属性和方法 get set

    // request对象基于node原生req封装了一系列便利属性和方法，供处理请求时调用。
    // 所以当你访问ctx.request.xxx的时候，实际上是在访问request对象上的赋值器（setter）和取值器（getter）。

    get header() {
        return this.req.headers;
    },

    set header(val) {
        this.req.headers = val;
    },
    get headers() {
        return this.req.headers;
    },
    set headers(val) {
        this.req.headers = val;
    },
    get url() {
        return this.req.url;
    },
    set url(val) {
        this.req.url = val;
    },
    get origin() {
        return `${this.protocol}://${this.host}`;
    },
    get href() {
        // support: `GET http://example.com/foo`
        if (/^https?:\/\//i.test(this.originalUrl)) return this.originalUrl;
        return this.origin + this.originalUrl;
    },
    get method() {
        return this.req.method;
    },
    set method(val) {
        this.req.method = val;
    },
    get path() {
        return parse(this.req).pathname;
    },
    set path(path) {
        const url = parse(this.req);
        if (url.pathname === path) return;

        url.pathname = path;
        url.path = null;

        this.url = stringify(url);
    },
    get query() {
        const str = this.querystring;
        const c = this._querycache = this._querycache || {};
        return c[str] || (c[str] = qs.parse(str));
    },
    set query(obj) {
        this.querystring = qs.stringify(obj);
    },
    get querystring() {
        if (!this.req) return '';
        return parse(this.req).query || '';
    },
    set querystring(str) {
        const url = parse(this.req);
        if (url.search === `?${str}`) return;

        url.search = str;
        url.path = null;

        this.url = stringify(url);
    },
    get search() {
        if (!this.querystring) return '';
        return `?${this.querystring}`;
    },
    set search(str) {
        this.querystring = str;
    },
    get host() {
        const proxy = this.app.proxy;
        let host = proxy && this.get('X-Forwarded-Host');
        if (!host) {
            if (this.req.httpVersionMajor >= 2) host = this.get(':authority');
            if (!host) host = this.get('Host');
        }
        if (!host) return '';
        return host.split(/\s*,\s*/, 1)[0];
    },
    get hostname() {
        const host = this.host;
        if (!host) return '';
        if ('[' === host[0]) return this.URL.hostname || ''; // IPv6
        return host.split(':', 1)[0];
    },
    get URL() {
        /* istanbul ignore else */
        if (!this.memoizedURL) {
            const originalUrl = this.originalUrl || ''; // avoid undefined in template string
            try {
                this.memoizedURL = new URL(`${this.origin}${originalUrl}`);
            } catch (err) {
                this.memoizedURL = Object.create(null);
            }
        }
        return this.memoizedURL;
    },
    get fresh() {
        const method = this.method;
        const s = this.ctx.status;

        // GET or HEAD for weak freshness validation only
        if ('GET' !== method && 'HEAD' !== method) return false;

        // 2xx or 304 as per rfc2616 14.26
        if ((s >= 200 && s < 300) || 304 === s) {
            return fresh(this.header, this.response.header);
        }
        return false;
    },
    get stale() {
        return !this.fresh;
    },
    get idempotent() {
        const methods = ['GET', 'HEAD', 'PUT', 'DELETE', 'OPTIONS', 'TRACE'];
        return !!~methods.indexOf(this.method);
    },
    get socket() {
        return this.req.socket;
    },
    get charset() {
        try {
            const {
                parameters
            } = contentType.parse(this.req);
            return parameters.charset || '';
        } catch (e) {
            return '';
        }
    },
    get length() {
        const len = this.get('Content-Length');
        if (len === '') return;
        return ~~len;
    },
    get protocol() {
        if (this.socket.encrypted) return 'https';
        if (!this.app.proxy) return 'http';
        const proto = this.get('X-Forwarded-Proto');
        return proto ? proto.split(/\s*,\s*/, 1)[0] : 'http';
    },
    get secure() {
        return 'https' === this.protocol;
    },
    get ips() {
        const proxy = this.app.proxy;
        const val = this.get(this.app.proxyIpHeader);
        let ips = proxy && val ?
            val.split(/\s*,\s*/) : [];
        if (this.app.maxIpsCount > 0) {
            ips = ips.slice(-this.app.maxIpsCount);
        }
        return ips;
    },
    get ip() {
        if (!this[IP]) {
            this[IP] = this.ips[0] || this.socket.remoteAddress || '';
        }
        return this[IP];
    },
    set ip(_ip) {
        this[IP] = _ip;
    },
    get subdomains() {
        const offset = this.app.subdomainOffset;
        const hostname = this.hostname;
        if (net.isIP(hostname)) return [];
        return hostname
            .split('.')
            .reverse()
            .slice(offset);
    },
    get accept() {
        return this._accept || (this._accept = accepts(this.req));
    },
    set accept(obj) {
        this._accept = obj;
    },
    accepts(...args) {
        return this.accept.types(...args);
    },
    acceptsEncodings(...args) {
        return this.accept.encodings(...args);
    },
    acceptsCharsets(...args) {
        return this.accept.charsets(...args);
    },
    acceptsLanguages(...args) {
        return this.accept.languages(...args);
    },
    is(type, ...types) {
        return typeis(this.req, type, ...types);
    },
    get type() {
        const type = this.get('Content-Type');
        if (!type) return '';
        return type.split(';')[0];
    },
    get(field) {
        const req = this.req;
        switch (field = field.toLowerCase()) {
            case 'referer':
            case 'referrer':
                return req.headers.referrer || req.headers.referer || '';
            default:
                return req.headers[field] || '';
        }
    },
    inspect() {
        if (!this.req) return;
        return this.toJSON();
    },
    toJSON() {
        return only(this, [
            'method',
            'url',
            'header'
        ]);
    }
};


if (util.inspect.custom) {
    module.exports[util.inspect.custom] = module.exports.inspect;
}
```

`response.js`

```js
'use strict';

// response对象与request对象类似

const contentDisposition = require('content-disposition');
const getType = require('cache-content-type');
const onFinish = require('on-finished');
const escape = require('escape-html');
const typeis = require('type-is').is;
const statuses = require('statuses');
const destroy = require('destroy');
const assert = require('assert');
const extname = require('path').extname;
const vary = require('vary');
const only = require('only');
const util = require('util');
const encodeUrl = require('encodeurl');
const Stream = require('stream');


module.exports = {
    // 在application.js的createContext函数中，会把node原生的res作为request对象(即responsejs封装的对象)的属性
    // response对象会基于req封装很多便利的属性和方法

    // 大量类似的工具属性和方法 get set

    // response对象基于node原生res封装了一系列便利属性和方法，供处理请求时调用。
    // 所以当你访问ctx.response.xxx的时候，实际上是在访问response对象上的赋值器（setter）和取值器（getter）。

    get socket() {
        return this.res.socket;
    },

    get header() {
        const {
            res
        } = this;
        return typeof res.getHeaders === 'function' ?
            res.getHeaders() :
            res._headers || {}; // Node < 7.7
    },
    get headers() {
        return this.header;
    },
    get status() {
        return this.res.statusCode;
    },
    set status(code) {
        if (this.headerSent) return;

        assert(Number.isInteger(code), 'status code must be a number');
        assert(code >= 100 && code <= 999, `invalid status code: ${code}`);
        this._explicitStatus = true;
        this.res.statusCode = code;
        if (this.req.httpVersionMajor < 2) this.res.statusMessage = statuses[code];
        if (this.body && statuses.empty[code]) this.body = null;
    },
    get message() {
        return this.res.statusMessage || statuses[this.status];
    },
    set message(msg) {
        this.res.statusMessage = msg;
    },
    get body() {
        return this._body;
    },
    set body(val) {
        // 这里对body进行操作并没有使用原生的this.res.end，因为在我们编写koa代码的时候，会对body进行多次的读取和修改，所以真正返回浏览器信息的操作是在application.js里进行封装和操作
        const original = this._body;
        this._body = val;

        // no content
        if (null == val) {
            if (!statuses.empty[this.status]) this.status = 204;
            if (val === null) this._explicitNullBody = true;
            this.remove('Content-Type');
            this.remove('Content-Length');
            this.remove('Transfer-Encoding');
            return;
        }

        // set the status
        if (!this._explicitStatus) this.status = 200;

        // set the content-type only if not yet set
        const setType = !this.has('Content-Type');

        // string
        if ('string' === typeof val) {
            if (setType) this.type = /^\s*</.test(val) ? 'html' : 'text';
            this.length = Buffer.byteLength(val);
            return;
        }

        // buffer
        if (Buffer.isBuffer(val)) {
            if (setType) this.type = 'bin';
            this.length = val.length;
            return;
        }

        // stream
        if (val instanceof Stream) {
            onFinish(this.res, destroy.bind(null, val));
            if (original != val) {
                val.once('error', err => this.ctx.onerror(err));
                // overwriting
                if (null != original) this.remove('Content-Length');
            }

            if (setType) this.type = 'bin';
            return;
        }

        // json
        this.remove('Content-Length');
        this.type = 'json';
    },
    set length(n) {
        this.set('Content-Length', n);
    },
    get length() {
        if (this.has('Content-Length')) {
            return parseInt(this.get('Content-Length'), 10) || 0;
        }

        const {
            body
        } = this;
        if (!body || body instanceof Stream) return undefined;
        if ('string' === typeof body) return Buffer.byteLength(body);
        if (Buffer.isBuffer(body)) return body.length;
        return Buffer.byteLength(JSON.stringify(body));
    },
    get headerSent() {
        return this.res.headersSent;
    },
    vary(field) {
        if (this.headerSent) return;

        vary(this.res, field);
    },
    redirect(url, alt) {
        // location
        if ('back' === url) url = this.ctx.get('Referrer') || alt || '/';
        this.set('Location', encodeUrl(url));

        // status
        if (!statuses.redirect[this.status]) this.status = 302;

        // html
        if (this.ctx.accepts('html')) {
            url = escape(url);
            this.type = 'text/html; charset=utf-8';
            this.body = `Redirecting to <a href="${url}">${url}</a>.`;
            return;
        }

        // text
        this.type = 'text/plain; charset=utf-8';
        this.body = `Redirecting to ${url}.`;
    },
    attachment(filename, options) {
        if (filename) this.type = extname(filename);
        this.set('Content-Disposition', contentDisposition(filename, options));
    },
    set type(type) {
        type = getType(type);
        if (type) {
            this.set('Content-Type', type);
        } else {
            this.remove('Content-Type');
        }
    },
    set lastModified(val) {
        if ('string' === typeof val) val = new Date(val);
        this.set('Last-Modified', val.toUTCString());
    },
    get lastModified() {
        const date = this.get('last-modified');
        if (date) return new Date(date);
    },
    set etag(val) {
        if (!/^(W\/)?"/.test(val)) val = `"${val}"`;
        this.set('ETag', val);
    },
    get etag() {
        return this.get('ETag');
    },
    get type() {
        const type = this.get('Content-Type');
        if (!type) return '';
        return type.split(';', 1)[0];
    },
    is(type, ...types) {
        return typeis(this.type, type, ...types);
    },
    get(field) {
        return this.header[field.toLowerCase()] || '';
    },
    has(field) {
        return typeof this.res.hasHeader === 'function' ?
            this.res.hasHeader(field)
            // Node < 7.7
            :
            field.toLowerCase() in this.headers;
    },
    set(field, val) {
        if (this.headerSent) return;

        if (2 === arguments.length) {
            if (Array.isArray(val)) val = val.map(v => typeof v === 'string' ? v : String(v));
            else if (typeof val !== 'string') val = String(val);
            this.res.setHeader(field, val);
        } else {
            for (const key in field) {
                this.set(key, field[key]);
            }
        }
    },
    append(field, val) {
        const prev = this.get(field);

        if (prev) {
            val = Array.isArray(prev) ?
                prev.concat(val) : [prev].concat(val);
        }

        return this.set(field, val);
    },
    remove(field) {
        if (this.headerSent) return;

        this.res.removeHeader(field);
    },
    get writable() {
        if (this.res.writableEnded || this.res.finished) return false;
        const socket = this.res.socket;
        if (!socket) return true;
        return socket.writable;
    },
    inspect() {
        if (!this.res) return;
        const o = this.toJSON();
        o.body = this.body;
        return o;
    },
    toJSON() {
        return only(this, [
            'status',
            'message',
            'header'
        ]);
    },
    flushHeaders() {
        this.res.flushHeaders();
    }
};
if (util.inspect.custom) {
    module.exports[util.inspect.custom] = module.exports.inspect;
}
```