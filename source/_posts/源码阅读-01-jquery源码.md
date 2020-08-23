---
title: 源码阅读-01-jquery源码
date: 2020-08-15
categories: 源码
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

jquery诞生于2006年1月，至今已经存在14年多的时间了，相信前端开发者都对他有所听闻。虽然现在涌现了很多优秀的前端框架，如vue、react等等，也逐渐取代了传统的jquery开发模式，但是jq作为一款曾风靡前端圈的框架，其优秀的设计思想还是值得开发者好好学习一下的。下面，我们来看jquery具体的源码内容：

### 关于如何读源码
曾经，我去阅读源码时，总是喜欢一句不漏的从上到下阅读，怕漏掉什么核心代码导致不理解程序的详细流程，遇到一个函数或类就跳过去看，然后……花费了很多的时间而且效果相当不理想。不仅对整体的框架没有大体了解，也让自己像只无头苍蝇，不知道如何抓住重点去深入研究背后的原理。后来看了不少大佬阅读的方式，对我也是收获颇多：不要采用DFS（深度遍历）的方式阅读源码，而是应该先理清框架的架构，再从入口出发，依照流程读下去。这个过程首先应该对框架有一个整体的了解，建议画个流程图或思维脑图，掌握源码的整体处理流程。接下来可以针对源码功能进行个分类，进行深入研究：如阅读koa时，可以分为框架设计和基础结构、中间件实现机制（洋葱模型）、同一错误处理、委托模式、接口安全保障……阅读vue时可以分为框架设计、完整流程、响应式原理、MVVM模型实现、编译原理……哪个部分感兴趣就可以深入探索，整理成文方便自己回顾，会不知不觉地学习到一些优秀的代码架构设计思维和优秀的技巧。少年啊，Fighting！！！

### jquery架构设计
jquery的设计理念是：The Write Less,Do More（写更少，做更多），具有简洁的API、优雅的链式、强大的查询与便捷的操作。

jq的三大设计思想：
1. 利用工厂模式，无new化构建对象
2. 模块划分明确
3. 开闭原则的优秀体现

jquery的整体架构设计如下（此图来自于慕课网）：

<img src="https://i.loli.net/2020/08/20/kGEoPl3beZruBgd.png" alt="jq架构设计" width="80%" />

将框架的各部分作精简，提出大体上的架构，知道jquery框架的整体构建思路，再针对各部分进行深入挖掘学习，效果会更好。整体的代码组织架构如下：

```js
<script type="text/javascript">
;(function(global, factory) {
    factory(global);
}(typeof window !== "undefined" ? window : this, function(window, noGlobal) {
    var jQuery = function( selector, context ) {
		return new jQuery.fn.init( selector, context );
	};
	jQuery.fn = jQuery.prototype = {};
	// 核心方法
	// 回调系统
	// 异步队列
	// 数据缓存
	// 队列操作
	// 选择器引
	// 属性操作
	// 节点遍历
	// 文档处理
	// 样式操作
	// 属性操作
	// 事件体系
	// AJAX交互
	// 动画引擎
	return jQuery;
}));
```

以下是整体框架的另一种详细展示:

```JavaScript
// 匿名函数自执行,与其他代码避免冲突
(function(window, undefined) {

    // 21-94 
    // 定义一系列变量和函数
    jQuery = function() {

    }

    // 96-281
    // 给jQuery对象添加一些方法和属性
    jQuery.fn = jQuery.prototype = {}

    // 285-347
    // jQuery继承方法,很方便去扩展方法
    jQuery.extend = jQuery.fn.extend = function() {}

    // 349-816
    // 扩展一些工具方法(静态方法)
    jQuery.extend({})

    // 877-2868
    // Sizzle:复杂选择器的实现 
    // Sizzle CSS Selector Engine

    // 2903 - 3065
    // 回调对象,函数的统一管理
    jQuery.Callbacks = function(options) {}

    // 3066-3206
    // Deferred:延迟对象,对异步的统一管理
    jQuery.extend({})

    // 3206-3220
    // 功能检测:通过功能判断浏览器
    jQuery.support = (function(support) {})({})

    // 3333-3678
    // data():数据缓存
    function Data() {}
    Data.prototype = {}

    // 3679-3823
    // 队列管理queue dequeue
    jQuery.extend({
        queue: function() {},
        dequeue: function() {}
    })
    jQuery.fn.extend({
        queue: function() {},
        dequeue: function() {}
    })

    // 3824-4278
    // 对元素属性的操作

    // 事件操作的相关方法

    // 5161-6025
    // dom操作封装(添加\删除\筛选)

    // 6025-6638
    // CSS方法(样式操作)

    // 6638-7913
    // ajax操作

    // 7914-8584
    // animate运动方法

    // 8585-8792
    // 位置与尺寸的方法 offsetTop等

    // 8804-8821
    // jquery支持模块化的模式

    // 向外提供接口,在window中绑定全局变量
    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

### 问题解答
**1. 为什么要传入window、undefined？**

<img src="https://i.loli.net/2020/08/20/XEVk1QDrOaI9ChK.png" alt="jq-q1" width="80%" />

这个问题涉及到作用域链问题：因为js查找变量是根据作用域链查找的，window是做顶端的一层，传入window能减少逐级向上查找的时间。undefined是js变量，而不是关键字，但null是关键字，没必要像undefined一样查找，因此传undefined不传null。

**2. 为什么jq要使用工厂方法，向外暴露的是工厂方法，而vue向外暴露的是类？**

<img src="https://i.loli.net/2020/08/20/cprPGZwA6vFdgVT.png" alt="jq-q2" width="80%" />

<img src="https://i.loli.net/2020/08/20/OjiHvEGk1gLlrXP.png" alt="jq-q3" width="80%" />

因为vue项目中有且只有一个根实例new Vue()，通过改变该实例数据去改变页面展示，不能使用工厂模式；而jq需要用到大量的DOM对象，框架是直接对DOM进行操作的，因此选用了工厂方法。如果我们以后要自己写轮子，要思考一下应用场景，选择暴露工厂方法或类。

**3. jq如何实现的无new化创建对象的呢？应用中使用$()而不是new $()，可以使用以下代码实现吗？**

以下的写法有问题：方法内部又调用了此方法，会造成死循环。

<img src="https://i.loli.net/2020/08/20/PijH74A5tqEuGna.png" alt="jq-q4" width="80%" />

以下的写法每次都要同时向这三个对象上注入相同的方法，又会显得“很蠢”

<img src="https://i.loli.net/2020/08/20/T4XBW9ijp3oVL7d.png" alt="jq-q5" width="80%" />

jquery是采用共享原型的方法解决了这个问题：

<img src="https://i.loli.net/2020/08/20/St4QCxYDpwNZuFi.png" alt="jq-q6" width="80%" />

最终形成的init的的构造图：

<img src="https://i.loli.net/2020/08/20/sNlFrJ7TIiowSUu.png" alt="jq-init" width="80%" />

最终通过原型传递解决问题，把jQuery的原型传递给jQuery.prototype.init.prototype。换句话说jQuery的原型对象覆盖了init构造器的原型对象，因为是引用传递所以不需要担心这个循环引用的性能问题。

**4. jq如何实现模块划分明确的？（当时并没有模块化管理方案）**

<img src="https://i.loli.net/2020/08/20/gHzu413O5yG9bcL.png" alt="jq-q7" width="80%" />

由上图可见，jq没有将所有的方法一股脑地写入prototype中，而是通过extends方法分别将不同的功能模块扩展到原型链上。可以极大方便之后的修改，互不干扰，清晰明了，管理起来非常方便。

**5. extends方法如何实现的？ 以下代码可以实现效果吗？**

<img src="https://i.loli.net/2020/08/20/VhOKNsLAQjISP39.png" alt="jq-q8" width="80%" />

当传入的只有一个对象时，extend函数将对象的属性全部赋值给jquery对象，当传入的有两个对象Object1、Object2时，extend将Object2的属性全部赋值给Object1。但是以上的代码有个问题：两个for in循环使得代码累赘，如果要实现深拷贝，要同时修改多处，不优雅。

这时我们可以采用享元模式，减少对象的数量，而jquery正是采用了这样的做法——对这些对象分析，分析出私有的数据和方法，分析出公共的数据和方法。接下来看看采用享元模式修改的代码：

<img src="https://i.loli.net/2020/08/20/IsQuA5lEFYUcb2W.png" alt="jq-q9" width="80%" />

其中要注意注意考虑健壮性：如未传入任何参数，传入的参数不是对象等。要注意参数的处理（多态、健壮性、错误处理），两方面：1）多态：支持多种参数，针对不同类型参数做不同的事；2）健壮性：传错了参数或忘记传入参数导致程序报错。

**6. 模块检测支持如何实现？**

支持amd、cmd、commonjs等模块，如下图所示：

<img src="https://i.loli.net/2020/08/20/V3cRjvKzGa4M5Tw.png" alt="jq-q10" width="80%" />

如vue中也进行了模块化检测：

<img src="https://i.loli.net/2020/08/20/zLk7JtvWXHaYANF.png" alt="jq-q11" width="80%" />

### 模块解读
#### 定义一系列变量和函数
```JavaScript
// 匿名函数自执行,与其他代码避免冲突

// window 作为参数传入,是为了提高查询速度,也方便压缩时不失去其含义(window变为w等变量仍然会代表window变量)
// undefined 在某些浏览器下是可以被修改掉的,因此应该作为参数传递
(function(window, undefined) {

    //"use strict";//不建议使用

    // 21-94 
    // 定义一系列变量和函数
    var
        rootjQuery, //在后面会赋值为jQuery(document)
        readyList, //DOM加载
        core_strundefined = typeof undefined, //在老版本浏览器下xmlNode.method !== undefined,应该使用typeof undefined=='undefined'

        // 存成变量,有利于压缩
        location = window.location,
        document = window.document,
        docElem = document.documentElement,

        // 为了防止冲突,如引入的其他库中也存在$或jQuery
        _jQuery = window.jQuery,
        _$ = window.$,

        class2type = {}, //jquery中类型判断,存储的是js中的类型  $.type

        core_deletedIds = [], //与数据缓存有关

        core_version = "2.0.3",

        // 常用数组方法存储
        core_concat = core_deletedIds.concat,
        core_push = core_deletedIds.push,
        core_slice = core_deletedIds.slice,
        core_indexOf = core_deletedIds.indexOf,
        core_toString = class2type.toString,
        core_hasOwn = class2type.hasOwnProperty,
        core_trim = core_version.trim,

        // 相当于$()与jQuery(),返回的是对象,后面才可以调用方法
        jQuery = function(selector, context) {
            return new jQuery.fn.init(selector, context, rootjQuery);
        },

        core_pnum = /[+-]?(?:\d*\.|)\d+(?:[eE][+-]?\d+|)/.source, // 匹配数字正则
        core_rnotwhite = /\S+/g, // 匹配空格正则
        rquickExpr = /^(?:\s*(<[\w\W]+>)[^>]*|#([\w-]*))$/, // 匹配html标签,并且防止xss漏洞
        rsingleTag = /^<(\w+)\s*\/?>(?:<\/\1>|)$/,
        rmsPrefix = /^-ms-/, //检测-ms-前缀.与css驼峰转码有关
        rdashAlpha = /-([\da-z])/gi,

        // 转驼峰的回调函数
        fcamelCase = function(all, letter) {
            return letter.toUpperCase();
        },

        // 加载完毕后的处理(移除事件监听)
        completed = function() {
            document.removeEventListener("DOMContentLoaded", completed, false);
            window.removeEventListener("load", completed, false);
            jQuery.ready();
        };


    //对象的沿用,因此针对jQuery.prototype的原型修改和在jQuery.fn.init.prototype原型上进行修改的效果是一样的,因为是同一引用
    // 请看part2
    jQuery.fn = jQuery.prototype = {
        // ......
    }
    jQuery.fn.init.prototype = jQuery.fn;

    // 向外提供接口,在window中绑定全局变量
    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

#### 给jQuery对象添加一些方法和属性
```JavaScript
(function(window, undefined) {
    // 96-281
    // 给jQuery对象添加一些方法和属性
    jQuery.fn = jQuery.prototype = {
        jquery: core_version, //获取jQuery版本,$().jquery获取版本号
        constructor: jQuery, //js中自动生成 AAA.prototype.constructor=AAA,因为写的是jQuery.prototype ={}形式,因此需要修正constructor指向
        // 补充js知识:
        // function AAA {};
        // 方法1
        // AAA.prototype.name = 'zhang';
        // AAA.prototype.age = 12;
        // AAA.prototype.constructor=AAA; 成立
        // 方法2
        // AAA.prototype = {
        //     name: 'zhang',
        //     age: 12
        // }
        // AAA.prototype.constructor=Object; 成立
        // 因此需要手动修复constructor指向,AAA.prototype.constructor=AAA

        init: function(selector, context, rootjQuery) {
            var match, elem;

            // 过滤不正确元素 $(""), $(null), $(undefined), $(false)
            if (!selector) {
                return this;
            }

            // 补充js知识:
            // $('li').css('background','red');
            // 如果存为带有length属性的json变量,可以遍历赋值
            // this = {
            //     0: 'li',
            //     1: 'li',
            //     2: 'li',
            //     length: 3
            // }
            // for (var i = 0; i < this.length; i++) {
            //     this[i].style.background = 'red';
            // }

            // 根据selector种类进行处理
            // 1)如果selector是字符串
            if (typeof selector === "string") {
                // 检测是否为标签(创建标签)
                if (selector.charAt(0) === "<" && selector.charAt(selector.length - 1) === ">" && selector.length >= 3) {
                    match = [null, selector, null];
                } else {
                    match = rquickExpr.exec(selector);
                }

                // 创建id或是创建标签 $('#id) $('<div>hello</div>')
                if (match && (match[1] || !context)) {
                    // 创建标签
                    if (match[1]) {
                        context = context instanceof jQuery ? context[0] : context; //创建标签的上下文,制定document(考虑iframe,需要传入contentWindow.document)
                        // jQuery.parseHTML:将字符串转化为DOM数组
                        // jQuery.merge是数组的合并操作(对外提供数组合并,对内提供特殊的json合并)
                        jQuery.merge(this, jQuery.parseHTML(
                            match[1],
                            context && context.nodeType ? context.ownerDocument || context : document,
                            true //parseHTML这个参数是看script是否能添加进来
                        ));
                        // 处理: $(html, props)形式,如$("li",{title:'hello'})
                        // rsingleTag匹配单标签
                        if (rsingleTag.test(match[1]) && jQuery.isPlainObject(context)) {
                            for (match in context) {
                                // 执行jQuery方法,如$("li",{html:'text'}),执行$('li').html('text')方法 
                                if (jQuery.isFunction(this[match])) {
                                    this[match](context[match]);
                                } else {
                                    // 添加属性,如$("li",{title:'hello'})
                                    this.attr(match, context[match]);
                                }
                            }
                        }
                        return this;
                    } else {
                        // 获取id的element
                        elem = document.getElementById(match[2]); //想要的id
                        // 存成json形式
                        if (elem && elem.parentNode) {
                            this.length = 1;
                            this[0] = elem;
                        }
                        this.context = document;
                        this.selector = selector;
                        return this;
                    }
                } else if (!context || context.jquery) {
                    // 处理 $(expr, $(...))
                    // find->Sissle库
                    // rootjQuery是jQuery(document)
                    return (context || rootjQuery).find(selector);
                } else {
                    // 处理$(expr, context)
                    return this.constructor(context).find(selector);
                }
            } else if (selector.nodeType) {
                // 2) 如果selector是DOM节点, 如$(this) $(document)
                this.context = this[0] = selector;
                this.length = 1;
                return this;
            } else if (jQuery.isFunction(selector)) {
                // 3)selector是函数

                // $(function() {}) 还是调的$(document).ready(function() {})方法
                return rootjQuery.ready(selector);
            }

            // 对特殊情况作处理,如$({}) $([]) $($('div'))
            if (selector.selector !== undefined) {
                this.selector = selector.selector;
                this.context = selector.context;
            }
            // jQuery.makeArray方法将伪数组转化为真正的数组,传递第二个参数是属于内部方法,最后会转化为json(含有length属性)
            return jQuery.makeArray(selector, this);
        },

        selector: "",
        length: 0,

        // 方法定义

        // 转数组
        toArray: function() {
            // 调用slice方法,在json环境下去执行.会返回数组
            // 如:
            // let arr = {
            //     0: 'hello',
            //     1: 'hello',
            //     2: 'hello',
            //     length: 3
            // }
            // let realArr = arr.slice() //转化为了真正的数组['hello','hello','hello']
            return core_slice.call(this);
        },

        // 转原生集合,可以调用原生DOM方法
        get: function(num) {
            return num == null ?
                this.toArray() :
                (num < 0 ? this[this.length + num] : this[num]); //找集合中的第num个
        },

        // jQuery的入栈处理(先进后出)
        pushStack: function(elems) {
            var ret = jQuery.merge(this.constructor(), elems);
            // 举例:$('div').pushStack($('span')).css('background','red').end().css('background','yellow')
            // 含义是:将span元素变red,div元素变yellow
            ret.prevObject = this; //可以通过end找到stack的下一级
            ret.context = this.context;
            return ret;
        },

        // 通过工具方法jQuery.each实现
        each: function(callback, args) {
            return jQuery.each(this, callback, args);
        },
        // 调用jQuery.ready
        ready: function(fn) {
            jQuery.ready.promise().done(fn);
            return this;
        },

        slice: function() {
            // 入栈
            return this.pushStack(core_slice.apply(this, arguments));
        },
        // 找第一项
        first: function() {
            return this.eq(0);
        },
        // 找最后一项
        last: function() {
            return this.eq(-1);
        },
        // 找第i项
        eq: function(i) {
            var len = this.length,
                j = +i + (i < 0 ? len : 0);
            return this.pushStack(j >= 0 && j < len ? [this[j]] : []);
        },
        // map操作
        map: function(callback) {
            // 调用jQuery.map
            return this.pushStack(jQuery.map(this, function(elem, i) {
                return callback.call(elem, i, elem);
            }));
        },
        // 回溯到栈的下一层
        end: function() {
            return this.prevObject || this.constructor(null);
        },

        // 数组方法,作为内部使用,不建议在外部使用
        push: core_push,
        sort: [].sort,
        splice: [].splice
    };

    // 对象的沿用,因此针对jQuery.prototype的原型修改和在jQuery.fn.init.prototype原型上进行修改的效果是一样的,因为是同一引用
    jQuery.fn.init.prototype = jQuery.fn;

    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

#### 3. extend继承方法实现
```JavaScript
(function(window, undefined) {
    jQuery.fn.init.prototype = jQuery.fn;

    // 285-347
    // jQuery继承方法,很方便去扩展方法

    // jQuery.extend扩展静态方法,jQuery.fn.extend扩展实例方法(jQuery.fn.extend相当于jQuery.prototype.extend)

    // 基本使用:
    // $.extend() 
    // 1)当只写一个对象自变量的时候,是JQ中扩展插件的形式
    // 如:
    // $.extend({ //扩展工具方法
    //     aa:function(){},
    //     bb:function(){}
    // }) 
    // $.aa  \   $.bb
    // 2)当写多个对象的时候,后面的对象都是扩展到第一个对象之上
    // 如:
    // $.extend(a,{name:'wang',age:21})
    // 3)还可以做深拷贝和浅拷贝
    // var a={a:1},b={b:1}
    // $.extend(a,b) 浅拷贝
    // $.extend(true,a,b) 深拷贝

    // $.fn.extend()
    // 如:
    // $.extend({ //扩展JQ实例方法
    //     aa:function(){},
    //     bb:function(){}
    // }) 
    // $('div').aa  \  $('div').bb

    jQuery.extend = jQuery.fn.extend = function() {
        var options, name, src, copy, copyIsArray, clone,
            target = arguments[0] || {},
            i = 1,
            length = arguments.length,
            deep = false;

        // 看是不是深拷贝
        if (typeof target === "boolean") {
            deep = target;
            target = arguments[1] || {};
            i = 2;
        }

        // 看参数是否正确
        if (typeof target !== "object" && !jQuery.isFunction(target)) {
            target = {};
        }

        // 看是不是插件情况下
        if (length === i) {
            target = this;
            --i;
        }

        // 可能有多个对象的情况下
        for (; i < length; i++) {
            if ((options = arguments[i]) != null) {
                for (name in options) {
                    src = target[name];
                    copy = options[name];

                    // 防止循环引用
                    if (target === copy) {
                        continue;
                    }
                    // 深拷贝
                    if (deep && copy && (jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)))) {
                        // 补充实例:
                        // var a={person:{name:'zhang'}},b={person:{age:23}}
                        // $.extend(true,a,b)

                        // JQ:拷贝继承
                        // JS:类式继承/原型继承
                        if (copyIsArray) {
                            copyIsArray = false;
                            clone = src && jQuery.isArray(src) ? src : [];
                        } else {
                            clone = src && jQuery.isPlainObject(src) ? src : {};
                        }
                        // 使用递归
                        target[name] = jQuery.extend(deep, clone, copy);
                    } else if (copy !== undefined) {
                        // 浅拷贝,value不是对象类型,直接赋值即可
                        target[name] = copy;
                    }
                }
            }
        }
        return target;
    };

    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

#### 4. 工具方法汇总
所含有的工具方法：

```JavaScript
(function(window, undefined) {
    jQuery.extend({
        noConflict: function(deep) {
            //...
        },
        ready: function(wait) {
            //...
        },
        // 检测是否为函数
        isFunction: function(obj) {
            //...
        },
        // 检测是否为数组
        isArray: Array.isArray,
        // 检测是否为window对象
        isWindow: function(obj) {
            //...
        },
        // 检测是否为数字
        // 使用typeof方法判断不太靠谱,能把NaN判断为number类型
        isNumeric: function(obj) {
            //...
        },
        // 判断数据类型
        type: function(obj) {
            //...
        },
        // 检测是否是普通对象
        // 非普通对象的情况:type不是"[object Object]" \ dom元素 \ window
        isPlainObject: function(obj) {
            //...
        },
        // 检测是否为空对象
        isEmptyObject: function(obj) {
            //...
        },
        // 抛出异常
        error: function(msg) {
            //...
        },

        // 解析节点,将字符串转化为HTML DOM数组
        parseHTML: function(data, context, keepScripts) {
            //...
        },

        // 解析JSON,不兼容IE6/7
        parseJSON: JSON.parse,

        // 解析XML
        parseXML: function(data) {
           //...
        },
        // 返回空函数
        noop: function() {},

        // 在全局环境下执行js代码
        globalEval: function(code) {
            //...
        },

        // 转换CSS
        camelCase: function(string) {
            //...
        },
        // 是否为指定节点名
        nodeName: function(elem, name) {
            //...
        },

        // args参数仅作为内部使用
        each: function(obj, callback, args) {
            //...
        },

        trim: function(text) {
            //...
        },

        // results仅作为内部使用
        // 将伪数组转化为真正的数组
        makeArray: function(arr, results) {
           //...
        },

        inArray: function(elem, arr, i) {
            //...
        },

        merge: function(first, second) {
           //...
        },

        grep: function(elems, callback, inv) {
            //...
        },

        // arg参数仅作为内部使用
        map: function(elems, callback, arg) {
            //...
        },
        // 绑定函数到指定的上下文
        proxy: function(fn, context) {
            //...
        },

        access: function(elems, fn, key, value, chainable, emptyGet, raw) {
            //...
        },

        now: Date.now,

        // A method for quickly swapping in/out CSS properties to get correct calculations.
        swap: function(elem, options, callback, args) {
           //...
        }
    });

    jQuery.ready.promise = function(obj) {
        //...
    };

    // 计算class2type map
    jQuery.each("Boolean Number String Function Array Date RegExp Object Error".split(" "), function(i, name) {
        class2type["[object " + name + "]"] = name.toLowerCase();
    });

    function isArraylike(obj) {
        //...
    }
    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

工具类方法详细展示：

```JavaScript
(function(window, undefined) {

    // 349-816
    // 扩展一些工具方法(静态方法),jQuery最底层的方法
    jQuery.extend({
        // 生成唯一jQuery字符串(内部方法,对外未提供接口),唯一性,可以做映射关系
        expando: "jQuery" + (core_version + Math.random()).replace(/\D/g, ""),
        // 防止冲突
        // 举例:
        // var _$ = $.noConflict()
        // _$(function(){
        //      alert($)  //仍然弹出原始定义的$
        // })

        // 在前面part1部分,
        // 已经定义过两个变量:
        // _jQuery=window.jQuery
        // _$=window.$

        noConflict: function(deep) {
            if (window.$ === jQuery) {
                window.$ = _$;
            }

            if (deep && window.jQuery === jQuery) {
                window.jQuery = _jQuery;
            }

            return jQuery;
        },

        // $(function(){}) 等DOM加载完.时机早于window.onload,原生中的DOM加载结束事件是DOMContentLoaded
        // window.onload=function(){} 等全部资源加载完
        isReady: false,

        readyWait: 1,

        holdReady: function(hold) {
            if (hold) {
                jQuery.readyWait++;
            } else {
                jQuery.ready(true);
            }
        },

        // 这是工具方法,$.ready()
        ready: function(wait) {
            if (wait === true ? --jQuery.readyWait : jQuery.isReady) {
                return;
            }
            jQuery.isReady = true;
            if (wait !== true && --jQuery.readyWait > 0) {
                return;
            }
            readyList.resolveWith(document, [jQuery]);
            if (jQuery.fn.trigger) {
                jQuery(document).trigger("ready").off("ready");
            }
        },

        // 检测是否为函数
        isFunction: function(obj) {
            return jQuery.type(obj) === "function";
        },
        // 检测是否为数组
        isArray: Array.isArray,
        // 检测是否为window对象
        isWindow: function(obj) {
            return obj != null && obj === obj.window;
        },
        // 检测是否为数字
        // 使用typeof方法判断不太靠谱,能把NaN判断为number类型
        isNumeric: function(obj) {
            return !isNaN(parseFloat(obj)) && isFinite(obj);
        },
        // 判断数据类型
        type: function(obj) {
            if (obj == null) {
                return String(obj);
            }
            return typeof obj === "object" || typeof obj === "function" ?
                class2type[core_toString.call(obj)] || "object" :
                typeof obj;
        },
        // 检测是否是普通对象
        // 非普通对象的情况:type不是"[object Object]" \ dom元素 \ window
        isPlainObject: function(obj) {
            if (jQuery.type(obj) !== "object" || obj.nodeType || jQuery.isWindow(obj)) {
                return false;
            }
            try {
                if (obj.constructor &&
                    !core_hasOwn.call(obj.constructor.prototype, "isPrototypeOf")) {
                    return false;
                }
            } catch (e) {
                return false;
            }
            return true;
        },
        // 检测是否为空对象
        isEmptyObject: function(obj) {
            var name;
            for (name in obj) {
                return false;
            }
            return true;
        },
        // 抛出异常
        error: function(msg) {
            throw new Error(msg);
        },

        // 解析节点,将字符串转化为HTML DOM数组
        parseHTML: function(data, context, keepScripts) {
            if (!data || typeof data !== "string") {
                return null;
            }
            if (typeof context === "boolean") {
                keepScripts = context;
                context = false;
            }
            context = context || document;
            // rsingleTag为单标签正则
            var parsed = rsingleTag.exec(data),
                scripts = !keepScripts && [];

            // 处理单标签创建问题
            if (parsed) {
                return [context.createElement(parsed[1])];
            }
            // 处理多标签创建问题
            parsed = jQuery.buildFragment([data], context, scripts);

            if (scripts) {
                jQuery(scripts).remove();
            }

            return jQuery.merge([], parsed.childNodes);
        },

        // 解析JSON,不兼容IE6/7
        parseJSON: JSON.parse,

        // 解析XML
        parseXML: function(data) {
            var xml, tmp;
            if (!data || typeof data !== "string") {
                return null;
            }

            // 支持 IE9
            try {
                tmp = new DOMParser();
                xml = tmp.parseFromString(data, "text/xml");
            } catch (e) {
                xml = undefined;
            }

            if (!xml || xml.getElementsByTagName("parsererror").length) {
                jQuery.error("Invalid XML: " + data);
            }
            return xml;
        },
        // 返回空函数
        noop: function() {},

        // 在全局环境下执行js代码
        globalEval: function(code) {
            var script,
                indirect = eval;

            code = jQuery.trim(code);

            if (code) {
                // 在严格模式下,不支持eval,则创建script标签插入document,执行此代码
                if (code.indexOf("use strict") === 1) {
                    script = document.createElement("script");
                    script.text = code;
                    document.head.appendChild(script).parentNode.removeChild(script);
                } else {
                    // 正常模式下,直接使用eval解析js即可
                    indirect(code);
                }
            }
        },

        // 转换CSS
        camelCase: function(string) {
            return string.replace(rmsPrefix, "ms-").replace(rdashAlpha, fcamelCase);
        },
        // 是否为指定节点名
        nodeName: function(elem, name) {
            return elem.nodeName && elem.nodeName.toLowerCase() === name.toLowerCase();
        },

        // args参数仅作为内部使用
        each: function(obj, callback, args) {
            var value,
                i = 0,
                length = obj.length,
                isArray = isArraylike(obj);

            if (args) {
                if (isArray) {
                    for (; i < length; i++) {
                        value = callback.apply(obj[i], args);

                        if (value === false) {
                            break;
                        }
                    }
                } else {
                    for (i in obj) {
                        value = callback.apply(obj[i], args);

                        if (value === false) {
                            break;
                        }
                    }
                }
            } else {
                if (isArray) {
                    for (; i < length; i++) {
                        value = callback.call(obj[i], i, obj[i]);

                        if (value === false) {
                            break;
                        }
                    }
                } else {
                    for (i in obj) {
                        value = callback.call(obj[i], i, obj[i]);

                        if (value === false) {
                            break;
                        }
                    }
                }
            }

            return obj;
        },

        trim: function(text) {
            return text == null ? "" : core_trim.call(text);
        },

        // results仅作为内部使用
        // 将伪数组转化为真正的数组
        makeArray: function(arr, results) {
            var ret = results || [];

            if (arr != null) {
                if (isArraylike(Object(arr))) {
                    jQuery.merge(ret,
                        typeof arr === "string" ? [arr] : arr
                    );
                } else {
                    core_push.call(ret, arr);
                }
            }

            return ret;
        },

        inArray: function(elem, arr, i) {
            return arr == null ? -1 : core_indexOf.call(arr, elem, i);
        },

        merge: function(first, second) {
            var l = second.length,
                i = first.length,
                j = 0;

            if (typeof l === "number") {
                for (; j < l; j++) {
                    first[i++] = second[j];
                }
            } else {
                while (second[j] !== undefined) {
                    first[i++] = second[j++];
                }
            }

            first.length = i;

            return first;
        },

        grep: function(elems, callback, inv) {
            var retVal,
                ret = [],
                i = 0,
                length = elems.length;
            inv = !!inv;

            for (; i < length; i++) {
                retVal = !!callback(elems[i], i);
                if (inv !== retVal) {
                    ret.push(elems[i]);
                }
            }

            return ret;
        },

        // arg参数仅作为内部使用
        map: function(elems, callback, arg) {
            var value,
                i = 0,
                length = elems.length,
                isArray = isArraylike(elems),
                ret = [];

            if (isArray) {
                for (; i < length; i++) {
                    value = callback(elems[i], i, arg);

                    if (value != null) {
                        ret[ret.length] = value;
                    }
                }
            } else {
                for (i in elems) {
                    value = callback(elems[i], i, arg);

                    if (value != null) {
                        ret[ret.length] = value;
                    }
                }
            }

            return core_concat.apply([], ret);
        },

        guid: 1, //GUID

        // 绑定函数到指定的上下文
        proxy: function(fn, context) {
            var tmp, args, proxy;

            if (typeof context === "string") {
                tmp = fn[context];
                context = fn;
                fn = tmp;
            }

            if (!jQuery.isFunction(fn)) {
                return undefined;
            }

            args = core_slice.call(arguments, 2);
            proxy = function() {
                return fn.apply(context || this, args.concat(core_slice.call(arguments)));
            };

            proxy.guid = fn.guid = fn.guid || jQuery.guid++;

            return proxy;
        },

        access: function(elems, fn, key, value, chainable, emptyGet, raw) {
            var i = 0,
                length = elems.length,
                bulk = key == null;

            // Sets many values
            if (jQuery.type(key) === "object") {
                chainable = true;
                for (i in key) {
                    jQuery.access(elems, fn, i, key[i], true, emptyGet, raw);
                }

                // Sets one value
            } else if (value !== undefined) {
                chainable = true;

                if (!jQuery.isFunction(value)) {
                    raw = true;
                }

                if (bulk) {
                    // Bulk operations run against the entire set
                    if (raw) {
                        fn.call(elems, value);
                        fn = null;

                        // ...except when executing function values
                    } else {
                        bulk = fn;
                        fn = function(elem, key, value) {
                            return bulk.call(jQuery(elem), value);
                        };
                    }
                }

                if (fn) {
                    for (; i < length; i++) {
                        fn(elems[i], key, raw ? value : value.call(elems[i], i, fn(elems[i], key)));
                    }
                }
            }

            return chainable ?
                elems :

                // Gets
                bulk ?
                fn.call(elems) :
                length ? fn(elems[0], key) : emptyGet;
        },

        now: Date.now,

        // A method for quickly swapping in/out CSS properties to get correct calculations.
        swap: function(elem, options, callback, args) {
            var ret, name,
                old = {};

            // Remember the old values, and insert the new ones
            for (name in options) {
                old[name] = elem.style[name];
                elem.style[name] = options[name];
            }

            ret = callback.apply(elem, args || []);

            // Revert the old values
            for (name in options) {
                elem.style[name] = old[name];
            }

            return ret;
        }
    });

    jQuery.ready.promise = function(obj) {
        if (!readyList) {

            readyList = jQuery.Deferred();

            // Catch cases where $(document).ready() is called after the browser event has already occurred.
            // we once tried to use readyState "interactive" here, but it caused issues like the one
            // discovered by ChrisS here: http://bugs.jquery.com/ticket/12282#comment:15
            if (document.readyState === "complete") {
                // Handle it asynchronously to allow scripts the opportunity to delay ready
                setTimeout(jQuery.ready);
            } else {
                document.addEventListener("DOMContentLoaded", completed, false);
                window.addEventListener("load", completed, false);
            }
        }
        return readyList.promise(obj);
    };

    // 计算class2type map
    jQuery.each("Boolean Number String Function Array Date RegExp Object Error".split(" "), function(i, name) {
        class2type["[object " + name + "]"] = name.toLowerCase();
    });

    function isArraylike(obj) {
        var length = obj.length,
            type = jQuery.type(obj);

        if (jQuery.isWindow(obj)) {
            return false;
        }

        if (obj.nodeType === 1 && length) {
            return true;
        }

        return type === "array" || type !== "function" &&
            (length === 0 ||
                typeof length === "number" && length > 0 && (length - 1) in obj);
    }
    if (typeof window === "object" && typeof window.document === "object") {
        window.jQuery = window.$ = jQuery;
    }
})(window);
```

#### 选择器
jquery的定位是为了简化繁琐的DOM操作，选择器是一个十分关键的模块。而jquery是采用的Sizzle引擎（一个独立全新的选择器引擎）实现的选择器功能。下面我们来看看Sizzle的设计思路：

> 在需要递进式匹配时，比如$("div a") 这样的匹配时，执行的操纵都是先匹配页面中div然后再匹配它的节点下的a标签，之后返回结果。我们知道CSS的匹配规则是从右边向左筛选，jQuery在Sizzle中延续了这样的算法，先搜寻页面中所有的a标签，在之后的操纵中再往后判定它的父节点（包括父节点以上）是否为div，一层一层往上过滤，最后返回该操纵序列。

```js
var Sizzle =
    (function(window) {
        //...
        Sizzle.matches = function(expr, elements) {
            return Sizzle(expr, null, null, elements);
        };
        Sizzle.matchesSelector = function(elem, expr) {
           //...
            return Sizzle(expr, document, null, [elem]).length > 0;
        };
        Sizzle.contains = function(context, elem) {
            //...
            return contains(context, elem);
        };
        Sizzle.attr = function(elem, name) {
           //...
        };
        Sizzle.escape = function(sel) {
            return (sel + "").replace(rcssescape, fcssescape);
        };
        Sizzle.error = function(msg) {
            throw new Error("Syntax error, unrecognized expression: " + msg);
        };
        Sizzle.uniqueSort = function(results) {
            //...
            return results;
        };
        getText = Sizzle.getText = function(elem) {
            //...
            return ret;
        };
        Expr = Sizzle.selectors = {
            //...
        };
        //...
        //...
        tokenize = Sizzle.tokenize = function(selector, parseOnly) {
            //...
        }

        compile = Sizzle.compile = function(selector, match /* Internal Use Only */ ) {
            //...
        };

        select = Sizzle.select = function(selector, context, results, seed) {
                //...
            }
            //...
        return Sizzle;
    })(window);

jQuery.find = Sizzle;
jQuery.expr = Sizzle.selectors;
jQuery.expr[":"] = jQuery.expr.pseudos;
jQuery.uniqueSort = jQuery.unique = Sizzle.uniqueSort;
jQuery.text = Sizzle.getText;
jQuery.isXMLDoc = Sizzle.isXML;
jQuery.contains = Sizzle.contains;
jQuery.escapeSelector = Sizzle.escape;
```

#### AJAX
在数据请求的过程中，我们可能会遇到如下问题：
1. 跨域
2. json的格式
3. dataType
4. AJAX乱码问题
5. 页面缓存
6. 状态的跟踪
7. 不同平台兼容

jquery解决了以上这些问题，并且在此基础上做了扩展。jq实现了以下的属性和方法：
```
readyState
status
statusText
responseXML and/or responseText 当底层的请求分别作出XML和/或文本响应
setRequestHeader(name, value) 从标准出发，通过替换旧的值为新的值，而不是替换的新值到旧值
getAllResponseHeaders()
getResponseHeader()
abort()
```

为了实现以上这些功能，jQuery 在对 jqXHR 做2个处理：
1. 异步队列 deferred
2. 回调队列 Callbacks

```js
// Deferreds
deferred = jQuery.Deferred(),
//所有的回调队列，不管任何时候增加的回调保证只触发一次
completeDeferred = jQuery.Callbacks("once memory"),
```

jq源码中实现的ajax封装：
```js
jQuery.extend({
    active: 0,
    lastModified: {},
    etag: {},
    ajaxSettings: {
        url: location.href,
        type: "GET",
        isLocal: rlocalProtocol.test(location.protocol),
        global: true,
        processData: true,
        async: true,
        contentType: "application/x-www-form-urlencoded; charset=UTF-8",
        accepts: {
            "*": allTypes,
            text: "text/plain",
            html: "text/html",
            xml: "application/xml, text/xml",
            json: "application/json, text/javascript"
        },
        contents: {
            xml: /\bxml\b/,
            html: /\bhtml/,
            json: /\bjson\b/
        },
        responseFields: {
            xml: "responseXML",
            text: "responseText",
            json: "responseJSON"
        },
        converters: {
            "* text": String,
            "text html": true,
            "text json": JSON.parse,
            "text xml": jQuery.parseXML
        },
        flatOptions: {
            url: true,
            context: true
        }
    },
    ajaxSetup: function(target, settings) {
        return settings ?
            ajaxExtend(ajaxExtend(target, jQuery.ajaxSettings), settings) :
            ajaxExtend(jQuery.ajaxSettings, target);
    },
    ajaxPrefilter: addToPrefiltersOrTransports(prefilters),
    ajaxTransport: addToPrefiltersOrTransports(transports),
    ajax: function(url, options) {
        if (typeof url === "object") {
            options = url;
            url = undefined;
        }
        options = options || {};
        var transport,
            cacheURL,
            responseHeadersString,
            responseHeaders,
            timeoutTimer,
            urlAnchor,
            completed,
            fireGlobals,
            i,
            uncached,
            s = jQuery.ajaxSetup({}, options),
            callbackContext = s.context || s,
            globalEventContext = s.context &&
            (callbackContext.nodeType || callbackContext.jquery) ?
            jQuery(callbackContext) :
            jQuery.event,
            deferred = jQuery.Deferred(),
            completeDeferred = jQuery.Callbacks("once memory"),
            statusCode = s.statusCode || {},
            requestHeaders = {},
            requestHeadersNames = {},
            strAbort = "canceled",
            jqXHR = {
                readyState: 0,
                getResponseHeader: function(key) {
                    var match;
                    if (completed) {
                        if (!responseHeaders) {
                            responseHeaders = {};
                            while ((match = rheaders.exec(responseHeadersString))) {
                                responseHeaders[match[1].toLowerCase() + " "] =
                                    (responseHeaders[match[1].toLowerCase() + " "] || [])
                                    .concat(match[2]);
                            }
                        }
                        match = responseHeaders[key.toLowerCase() + " "];
                    }
                    return match == null ? null : match.join(", ");
                },
                getAllResponseHeaders: function() {
                    return completed ? responseHeadersString : null;
                },
                setRequestHeader: function(name, value) {
                    if (completed == null) {
                        name = requestHeadersNames[name.toLowerCase()] =
                            requestHeadersNames[name.toLowerCase()] || name;
                        requestHeaders[name] = value;
                    }
                    return this;
                },
                overrideMimeType: function(type) {
                    if (completed == null) {
                        s.mimeType = type;
                    }
                    return this;
                },
                statusCode: function(map) {
                    var code;
                    if (map) {
                        if (completed) {
                            jqXHR.always(map[jqXHR.status]);
                        } else {
                            for (code in map) {
                                statusCode[code] = [statusCode[code], map[code]];
                            }
                        }
                    }
                    return this;
                },
                abort: function(statusText) {
                    var finalText = statusText || strAbort;
                    if (transport) {
                        transport.abort(finalText);
                    }
                    done(0, finalText);
                    return this;
                }
            };
    }); 
```


#### 动画
jQuery 中有一个 Queue 队列的接口，是内部专门为动画服务的。`$("#card").slideUp().fadeIn()`是定义的一组动画链式序列，当 slideUp 运行时，fadeIn 被放到 fx 队列中，当 slideUp 完成后，从队列中被取出运行。jquery提供了队列操作的API，来看看代码：

```js
     jQuery.extend({
            queue: function(elem, type, data) {
                var queue;
                if (elem) {
                    type = (type || "fx") + "queue";
                    queue = dataPriv.get(elem, type);
                    if (data) {
                        if (!queue || Array.isArray(data)) {
                            queue = dataPriv.access(elem, type, jQuery.makeArray(data));
                        } else {
                            queue.push(data);
                        }
                    }
                    return queue || [];
                }
            },
            dequeue: function(elem, type) {
                type = type || "fx";
                var queue = jQuery.queue(elem, type),
                    startLength = queue.length,
                    fn = queue.shift(),
                    hooks = jQuery._queueHooks(elem, type),
                    next = function() {
                        jQuery.dequeue(elem, type);
                    };
                if (fn === "inprogress") {
                    fn = queue.shift();
                    startLength--;
                }
                if (fn) {
                    if (type === "fx") {
                        queue.unshift("inprogress");
                    }
                    delete hooks.stop;
                    fn.call(elem, next, hooks);
                }
                if (!startLength && hooks) {
                    hooks.empty.fire();
                }
            },
            // dequeue不仅是取出来还需要执行，在执行的时候把next与hooks传递给外部的回调，在内部可以传递一个引用出去，又能提供外部调用或者执行。
            _queueHooks: function(elem, type) {
                var key = type + "queueHooks";
                return dataPriv.get(elem, key) || dataPriv.access(elem, key, {
                    empty: jQuery.Callbacks("once memory").add(function() {
                        dataPriv.remove(elem, [type + "queue", key]);
                    })
                });
            }
        });
        jQuery.fn.extend({
            queue: function(type, data) {
                var setter = 2;
                if (typeof type !== "string") {
                    data = type;
                    type = "fx";
                    setter--;
                }
                if (arguments.length < setter) {
                    return jQuery.queue(this[0], type);
                }
                return data === undefined ?
                    this :
                    this.each(function() {
                        var queue = jQuery.queue(this, type, data);
                        jQuery._queueHooks(this, type);
                        if (type === "fx" && queue[0] !== "inprogress") {
                            jQuery.dequeue(this, type);
                        }
                    });
            },
            dequeue: function(type) {
                return this.each(function() {
                    jQuery.dequeue(this, type);
                });
            },
            clearQueue: function(type) {
                return this.queue(type || "fx", []);
            },
           //...
        });
```

举个栗子：

```js
book.animate({
    left: '+=50',
}).animate({
    left: '+=100',
}).animate({
    left: '-=50',
});
```

**jquery是如何处理这一组动画的？**
1. 设置一个队列，在执行第一个 animate 方法的时候加入队列就开始执行动画，因为动画自己在执行的时候就会产生异步的时间差
2. 在这个时间差里继续去加入之后的动画 animate 进去队列，然后在每一个动画结束之后去取出队列中的第一个 animate 方法开始执行，依次循环下去


jquery是通过 queue 调度动画的之间的衔接，Animation 方法执行单个动画的封装。

**动画的设计可以考虑以下几点：**
1. 参数的多形式传递
2. 基于 promise 的事件反馈
3. 增加属性的 show/hide/toggle 的快捷方式
4. 可以给 css 属性设置独立的缓动函数

**如何实现动画效果呢？我们应该考虑：**
- 起点位置
- 终点位置
- 运动的时间
- 每次定时器改变的坐标值
- 单位的换算(px、em、%)

```js
function Tween(value, prop, animation) {
	this.elem    = animation.elem;
	this.prop    = prop;
	this.easing  = "swing"; //动画缓动算法
	this.options = animation.options;
	//获取初始值
	this.start   = this.now = this.get();
	//动画最终值
	this.end     = value;
	//单位
	this.unit    = "px"
}

function getStyles(elem) {
	return elem.ownerDocument.defaultView.getComputedStyle(elem, null);
};

function swing(p) {
	return 0.5 - Math.cos(p * Math.PI) / 2;
}

Tween.prototype = {
	//获取元素的当前属性
	get: function() {
		var computed = getStyles(this.elem);
		var ret = computed.getPropertyValue(this.prop) || computed[this.prop];
		return parseFloat(ret);
	},
	//运行动画
	run:function(percent){
		var eased
		//根据缓动算法改变percent
		this.pos = eased = swing(percent);
		//获取具体的改变坐标值
		this.now = (this.end - this.start) * eased + this.start;
		//最终改变坐标
		this.elem.style[this.prop] = this.now + "px";
		return this;
	}
}
function Animation(elem, properties, options){
	//动画对象
	var animation = {
		elem            : elem,
		props           : properties,
		originalOptions : options,
		options         : options,
		startTime       : Animation.fxNow || createFxNow(),//动画开始时间
		tweens          : [] //存放每个属性的缓动对象，用于动画
	}

	//生成属性对应的动画算法对象
	for (var k in properties) {
		// tweens保存每一个属性对应的缓动控制对象
		animation.tweens.push( new Tween(properties[k], k, animation) )
	}

	//动画状态
	var stopped;
	//动画的定时器调用包装器
	var tick = function() {
		if (stopped) {
			return false;
		}
		//动画时间算法
		var currentTime = Animation.fxNow || createFxNow
			//运动时间递减
			remaining = Math.max(0, animation.startTime + animation.options.duration - currentTime),
			temp = remaining / animation.options.duration || 0,
			percent = 1 - temp;

		var index = 0,
			length = animation.tweens.length;

		//执行动画改变
		for (; index < length; index++) {
			//percent改变值
			animation.tweens[index].run(percent);
		}

		//是否继续，还是停止
		if (percent <= 1 && length) {
			return remaining;
		} else {
			//停止
			return false;
		}

	}
	tick.elem = elem;
	tick.anim = animation

	Animation.fx.timer(tick)
}	

//创建开始时间
function createFxNow() {
	setTimeout(function() {
		Animation.fxNow = undefined;
	});
	return (Animation.fxNow = Date.now());
}
//用于定时器调用
Animation.timers =[]

Animation.fx = {
	//开始动画队列
	timer: function(timer) {
		Animation.timers.push(timer);
		if (timer()) {
			//开始执行动画
			Animation.fx.start();
		} else {
			Animation.timers.pop();
		}
	},
	//开始循环
	start: function() {
		if (!Animation.timerId) {
			Animation.timerId = setInterval(Animation.fx.tick, 13);
		}
	},
	//停止循环
	stop:function(){
		clearInterval(Animation.timerId);
		Animation.timerId = null;
	},
	//循环的的检测
	tick: function() {
		var timer,
			i = 0,
			timers = Animation.timers;

		Animation.fxNow = Date.now();

		for (; i < timers.length; i++) {
			timer = timers[i];
			if (!timer() && timers[i] === timer) {
				//如果完成了就删除这个动画
				timers.splice(i--, 1);
			}
		}

		if (!timers.length) {
			Animation.fx.stop();
		}
		Animation.fxNow = undefined;
	}
}
```