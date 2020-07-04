---
title: jquery源码解析
date: 2019-08-11
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

拜读一个开源框架，最想学到的就是设计的思想和实现的技巧。

<!-- more -->

#### 简化框架

将框架的各部分作精简,提出大体上的架构,知道jquery框架的整体构建思路,再针对各部分进行深入挖掘学习,效果会更好.

以下是提炼出的整体框架:
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

接下来,针对各个部分进行深入地解析和学习.

#### 1. 定义一系列变量和函数
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

#### 2. 给jQuery对象添加一些方法和属性
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

        /**
         * selector:选择的元素
         * context:上下文,如$("li" "ul")
         */
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

#### 3. extend继承方法
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

#### 4. 工具方法
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

未完待续...