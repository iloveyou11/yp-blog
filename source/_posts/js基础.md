---
title: feiyue大佬的js总结
date: 2019-07-24
categories: JavaScript
tags:
  - JavaScript
comments: true
cover_picture: /images/banner.jpg
---

以下内容是feiyue大佬辛苦整理的内容，供我们学习分享。感谢大佬的付出。

- 面试工程师（年薪20-30万） —— 以基础知识为主
- 面试技术专家（年薪30-50万） —— 以工作经验为主
- 面试架构师（年薪50万以上） —— 以解决方案为主

<!-- more -->

**本教程主要针对第一种，把前端面试涉及到的基础知识讲明白，而且本教程只有 JS 相关的基础知识**。工作经验这个是没法传授的，只能自己在实际工作中慢慢总结。解决方案就更是不可复制的。有没有发现 —— 越往上越无法言传，越不能结构化 —— 很简单，越复杂越多变的东西，越无法结构化。

另外，在面试过程中，有很多的技巧和注意事项，会让你加分不少，教程的最后会教给大家。

##### 关于基础

《喜剧之王》中一本《演员的自我修养》多次出现在镜头中，如果有人问我“程序员的自我修养”最重要的是什么 ——— 我肯定回答是**基础知识**。这也是为何 BAT 等给钱最多的公司面试时如此重视基础知识的原因。

如果你有疑问，你现在就可以先让自己变傻 —— 先不要问为什么，把本教程所有的基础知识都学会再说。如果你觉得自己很聪明，那你将学不好编程。想做好任何事情，首先要让自己变成一个傻子。

随着你对基础知识的逐渐掌握，你会发现你之前的一些疑问，都会慢慢消解。学习后面的任何知识，也将变得更加有效率，比如 Vue React 等框架。

### 几个面试题

-----

##### 出题

先来看几个最常见的JS面试题。无论是从网上查找还是你们自己面试的经历，以下几个题目都是最常见的，当然还有其他常见的题目，就不在这里全部列出了，后面会详细介绍。

- JS中使用`typeof`能得到的哪些类型
- 何时使用`===`何时使用`==`
- `window.onload`和`DOMContentLoaded` 的区别
- 用 JS 创建 10 个`<a>`标签，点击的时候弹出来对应的序号
- 简述如何实现一个模块加载器，实现类似`require.js`的基本功能
- 实现数组的随机排序

##### 思考

面对这些题目，有些可能你已经有了答案，有些可能一时半会儿想不来，有些可能一点都不了解。没关系，我们这里先不做解答，先要根据以上题目**思考以下几个问题**：

- 拿到一个面试题，你第一时间看到的是什么
- 又如何看待网上搜出来的永远也看不完的题海
- 如何对待接下来遇到的面试题？

到这里，建议大家不要急于看接下来的教程，而是花几分钟的时间自己去想一下以上提出来的几点，第一章的几节内容，都会根据这几点思考来展开讲述。

##### 接下来

下一节，将一起讨论一下以上的几个思考问题

### 如何搞定所有面试题

##### 思考的解答

对上一节的思考，做一下解答。

###### 拿到一个面试题，你第一时间看到的是什么？

应该是知识点，这道题的考点，面试官出这道题，到底想考你什么。

###### 又如何看待网上搜出来的永远也看不完的题海？

以不变应万变，看那些题目考察的知识点是什么？题可以千变万化，但是知识点就这么多

###### 如何对待接下来的面试题？

先通过已有的题目，捋出前端考察的所有知识点（即本教程），然后再拿着这些知识点，去对应接下来的面试题。


##### 题目的知识点

然后，我们把上一节每个题目考察的知识点总结一下，要核心知识点，牵强附会的先不管

###### JS中使用`typeof`能得到的哪些类型

**JS 基础，变量的类型。** 同类型的题目还有：如何判断一个变量是数组？值类型和引用类型的区别？如何实现深度拷贝？

###### 何时使用`===`何时使用`==`

**变量的运算，强制类型转换。** 同类型的题目还有：快速将字符串转换为数字`+str`，以及快速将数字转换为字符串`num + ''`

###### window.onload 和 DOMContentLoaded 的区别

**页面渲染过程。** 同类型的题目还有：为何把`<script>`放在`<body>`底部，把 CSS 引用放在`<head>`中

###### 用 JS 创建 10 个`<a>`标签，点击的时候弹出来对应的序号

**闭包和作用域。** 同类型的题目还有：jquery 或者 zepto 等为何代码都包括在一个`(function(window){...})(window)`中

###### 简述如何实现一个模块加载器，实现类似 require.js 的基本功能

**JS 模块化。** 同类型的题目还有：有哪些常用的模块化管理标准（AMD CommonJS UMD）？分别应用于什么场景。

###### 实现数组的随机排序

**基本算法。** 同类型的题目还有：数组元素的去重。

##### 接下来

上面介绍了好多个知识点，那么前端一共有多少个知识点？什么样的知识体系才能覆盖所有的面试题？下一节解答。

### 前端知识体系

##### 如何梳理前端知识点

> 很早之前写过一篇博客[《自己总结的web前端知识体系大全》](http://www.cnblogs.com/wangfupeng1988/p/4649709.html)，如今有将近10万浏览量

前端开发到底需要哪些基础知识，它都有哪些内容，是一个什么样的体系。提到“体系”这个词你也不必害怕，其实内容不是特别多，而且跟着我的思路来，保证你不会混乱。

学习计算机编程，你会经常听到“标准”或者“协议”这些词汇，他俩表达的一个意思，就是确保双方沟通的格式的一致性。前端开发中需要哪些标准或者协议呢？

- **W3C 标准**

如 HTML CSS JS-WEB-API（如 DOM BOM 操作） HTTP 等等。官网：https://www.w3.org/TR/

- **ECMA 262 标准**

ES基础语法（变量、原型、闭包、异步等语法）。官网：https://www.ecma-international.org/publications/standards/Ecma-262.htm

另外，还有我们需要重点了解的知识

- **开发环境**

代码版本管理，代码架构方式、如模块化、工程化等

- **运行环境**

浏览器或者app中的webview


##### 前端知识体系

总结来说，前端知识体系中，与 JS 相关的基础知识有：

- ES 基础语法
- JS-WEB-API
- 开发环境
- 运行环境
- 其他综合性问题，如性能优化

> 咱们常说的 javascript 其实可以拆分成 ES基础语法 和 JS-WEB-API 两部分来看待，而且这两部分属于两个标准体系。

以上基础知识可以涵盖所有的 JS 基础面试题，本教程后面会使用常见面试题目配合以上知识做详细的讲解。因此，从题目到知识，再从知识反推到题目，才是最正确、高效的学习方式。

### 题目

- JS中使用`typeof`能得到的哪些类型
- 何时使用`===`何时使用`==`
- JS中有哪些内置函数
- JS变量按照存储方式区分为哪些类型，并描述其特点
- 如何理解JSON

请先自己思考每个题目的具体考点是什么？

### 知识点

##### 变量类型

JS变量最基本的分类就是**值类型**和**引用类型**，两者有何区别呢，可以通过例子看出来。

以下是值类型的一个例子

```javascript
var a = 100
var b = a
a = 200
console.log(b)
```

以下是引用类型的一个例子

```javascript
var a = {age:20}
var b = a
b.age = 21
console.log(a.age)
```

`typeof`可以知道一个值类型是什么类型，而对于引用类型，它就无能为力了。但是它可以将引用类型区分出`function`，为什么 ———— 因为`function`相对于其他引用类型（如对象、数组）来说，具有非常特殊的意义，JS 中的函数非常重要，接下来的原型、作用域都会深入讲解函数。

JS 中的某些表现，就已经体现了函数的特殊意义，例如：对象和数组，JS中没有内置的（不考虑 JS-WEB-API），而函数却内置了很多，例如 `Object` `Array` `Boolean` `Number` `String` `Function` `Date` `RegExp` `Error`。这些函数 JS 本身就有，要是没有它们，就没法愉快的写 JS 代码了。因为他们是基础数据类型的构造函数（后面会讲解）

`typeof`可以区分类型有`number` `string` `boolean` `undefined`（值类型） `function` `object`（引用类型）

```javascript
// 特例
typeof null // object 因为 null 也是引用类型。null 就相当于引用类型中的 undefined
```

那么针对第二个例子，如何将`a`的内容复制给`b`，并且保证`b`的修改不会影响到`a`呢？那就需要**深度复制**，意思就是对`a`的属性进行递归遍历，再依次复制，这块我们会放在后面专门讲解。

##### 变量计算

> 本节专门讲解值类型的计算，引用类型的计算放在后面作为“JS 算法”统一讲解。

组简单的计算，就是数字的加减乘除、字符串的拼接和替换，这个太简单了，这里不提了。但是 JS 在值类型的运算过程中，特别需要注意和利用**强制类型转换**这一特性，有以下场景：

- **字符串拼接**
- **`==`**
- **逻辑运算（`if` `!` `||` `&&`）**

字符串拼接最常见的错误如下，特别要注意。如何规避呢 ———— 对进行计算的变量通过`typeof`来判断类型 ———— 太麻烦？编码本身就是一个体力活！

```javascript
var a = 100 + 10   // 110
var b = 100 + '10' // '10010'
```

接下来，`==`也会进行强制类型转换，如

```javascript
100 == '100'   // true
0 == ''  // true
null == undefined  // true
```

针对`100 == '100'`就是和拼接字符串一样的类型转换，而针对下面两个例子，就是一个逻辑运算上的强制类型转换（马上会讲解）。所以，要求你写 JS 代码时，所有的地方都要使用`===`而不能使用`==`，但是阅读 jquery 源码后我发现一个特例，就是`obj.a == null`，使用很简洁。

最后，逻辑运算中的强制类型转换，先以`if`为例说明

```javascript
var a = true
if (a) {
    // ....
}
var b = 100
if (b) {
    // ....
}
var c = ''
if (c) {
    // ....
}
```

所有经过`if`判断的变量，都会进行逻辑运算的强制类型转换，转换为`true`或者`false`。

```javascript
console.log(10 && 0)  // 0
console.log('' || 'abc')  // 'abc'
console.log(!window.abc)  // true

// 判断一个变量会被当做 true 还是 false
var a = 100
console.log(!!a)
```

日常开发中，以下变量会被转换为`false`

- 0
- NaN
- ''
- null
- undefined
- false 本身

除了以上几个，其他的都会被转换为`true`。**除了`if`之外，`!` `||` `&&`这三个运算符也会进行同样的转换，跟`if`是一个道理**。因此，如何快速判断一个变量将会被`if`转换为什么呢？————`!!a`

### 答题

###### JS中使用`typeof`能得到的哪些类型

针对这个题目，可以通过以下程序进行验证

```javascript
typeof undefined // undefined
typeof 'abc' // string
typeof 123 // number
typeof true // boolean
typeof {}  // object
typeof [] // object
typeof null // object
typeof console.log // function
```


###### 何时使用`===` 何时使用`==`

首先你得明白两者的区别。`==`会先试图类型转换，然后再比较，而`===`不会类型转换，直接比较。如下例子：

```javascript
1 == '1' // true
1 === '1' // false
0 == false // true
0 === false // false
null == undefined // true
null === undefined // false
```

根据 jQuery 源码中的写法，只推荐在一个地方用`==`，其他地方都必须用`===`。这个用`==`的地方就是：

```javascript
if (obj.a == null) {  // 这里相当于 obj.a === null || obj.a === undefined ，简写形式
}
```

编程是需要绝对严谨的态度，我们只在这一个地方让它进行类型转换，来简化我们的写法，因为这个场景非常简单和固定。而其他场景下，我们都必须使用`===`，除非有特殊的业务需要。

###### JS中有哪些内置函数 —— 数据封装类对象

`Object` `Array` `Boolean` `Number` `String` `Function` `Date` `RegExp` `Error`

对于这种问题，回复时能把基本常用的回答上来就可以，没必要背书把所有的都写上。

###### JS变量按照存储方式区分为哪些类型，并描述其特点

- 值类型 `undefined` `string` `number` `boolean`
- 引用类型 `object` `function`

最后补充一点，在 JS 中，所有的引用类型都可以自由设置属性

```javascript
var obj = {}
obj.a = 100

var arr = []
arr.a = 100

function fn() {}
fn.a = 100
```

###### 如何理解JSON

这个问题，很容易被一些初学者误答。其实，JSON 是什么？从 JS 角度回答，太简单了，`console.log(JSON)`得到`JSON`只是一个对象，有`parse`和`stringify`两个方法，使用也非常简单

```javascript
JSON.stringify({a:10, b:20})
JOSN.parse('{"a":10,"b":20}')
```

我之所以误答，就是怕初学者把这个问题搞大，因为 json 也是一种数据格式，这一点和 xml 一样。但是在 JS 的面试题中，如果问到这个问题，直接说明`parse`和`stringify`两个方法的用法即可，面试官如果有追问，你再去继续回答。

### 题目

- 如何**准确**判断一个变量是数组类型
- 写一个原型链继承的例子
- 描述 new 一个对象的过程
- zepto（或其他框架） 源码中如何使用原型链

### 知识点

JS 是基于原型的语言，原型理解起来非常简单。任何长存不会被遗弃和提到的东西，都是最简单的东西。

##### 构造函数

所有的 JS 入门教程都会有类似这样的例子

```javascript
function Foo(name, age) {
    this.name = name
    this.age = age
    this.class = 'class-1'
    // return this  // 默认有这一行
}
var f = new Foo('zhangsan', 20)
// var f1 = new Foo('lisi', 22)  // 创建多个对象
```

以上示例是通过`new Foo`创建出来一个`f`对象，对象有`name` `age` `class`三个属性，这样我们就称`Foo`是`f`的**构造函数**。构造函数这个概念在高级语言中都存在，它就像一个模板一样，可以创建出若干个示例。

函数执行的时候，如果前面带有`new`，那么函数内部的`this`在执行时就完全不一样了，不带`new`的情况我们下一章节会讲到。带`new`执行时，函数中的`this`就会变成一个空对象，让程序为其属性赋值，然后最终返回。`return this`是默认执行的，如何验证？———— 你可以最后加一个`return {x:10}`试一下。返回之后，`f`就被赋值成了这个新对象，这样就创建完成了。

##### 构造函数 - 扩展

- `var a = {}`其实是`var a = new Object()`的语法糖
- `var a = []`其实是`var a = new Array()`的语法糖
- `function Foo(){...}`其实是`var Foo = new Function(...)`的语法糖

大家看到以上几点，明白我要表达的意思了吗？

**如何判断一个函数是否是一个变量的构造函数呢 ———— 使用`instanceof`**，原理接下来就会讲到。

##### 几个要点

以下要点，要全部明白并且记住！！！在此我会详细解释

- **所有的引用类型（数组、对象、函数），都具有对象特性，即可自由扩展属性（除了`null`意外）**
- **所有的引用类型（数组、对象、函数），都有一个`__proto__`属性，属性值是一个普通的对象**
- **所有的函数，都有一个`prototype`属性，属性值也是一个普通的对象**
- **所有的引用类型（数组、对象、函数），`__proto__`属性值指向它的构造函数的`prototype`属性值**

```javascript
var obj = {}; obj.a = 100;
var arr = []; arr.a = 100;
function fn () {}
fn.a = 100;

console.log(obj.__proto__);
console.log(arr.__proto__);
console.log(fn.__proto__);

console.log(fn.prototype)

console.log(obj.__proto__ === Object.prototype)
```

##### 原型

我们先将一开始的示例做一下改动，然后看一下执行的效果

```javascript
// 构造函数
function Foo(name, age) {
    this.name = name
}
Foo.prototype.alertName = function () {
    alert(this.name)
}
// 创建示例
var f = new Foo('zhangsan')
f.printName = function () {
    console.log(this.name)
}
// 测试
f.printName()
f.alertName()
```

执行`printName`时很好理解，但是执行`alertName`时发生了什么？这里再记住一个重点 **当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的`__proto__`（即它的构造函数的`prototype`）中寻找**

那么如何判断一个这个属性是不是对象本身的属性呢？使用`hasOwnProperty`，常用的地方是遍历一个对象的时候

```javascript
var item
for (item in f) {
    // 高级浏览器已经在 for in 中屏蔽了来自原型的属性，但是这里建议大家还是加上这个判断，保证程序的健壮性
    if (f.hasOwnProperty(item)) {
        console.log(item)
    }
}
```

##### 原型链

还是接着上面的示例，执行`f.toString()`时，又发生了什么？因为`f`本身没有`toString()`，并且`f.__proto__`（即`Foo.prototype`）中也没有`toString`。这个问题还是得拿出刚才那句话————**当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的`__proto__`（即它的构造函数的`prototype`）中寻找**

如果在`f.__proto__`中没有找到`toString`，那么就继续去`f.__proto__.__proto__`中寻找，因为`f.__proto__`就是一个普通的对象而已嘛！

这样一直往上找，你会发现是一个链式的结构，所以叫做“原型链”。直到找到最上层都没有找到，那么就宣告失败，返回`undefined`。最上层是什么 ———— `Object.prototype.__proto__ === null`

##### 原型链中的`this`

所有的从原型或者更高级的原型中得到、执行的方法，其中的`this`在执行时，就指向了当前这个触发事件执行的对象。因此`printName`和`alertName`中的`this`都是`f`。

![1550221325541](http://i1.fuimg.com/695012/62d125f20e037dc4.png)

##### instanceof

开始介绍的`instanceof`这里再讲一下原理。如果要计算`f instanceof Foo`是不是正确，就要判断`f`的原型一层一层往上，能否对应到`Foo.prototype`。同理，如果要计算`f instanceof Object`是不是正确，就要判断`f`的原型一层一层往上，能否对应到`Object.prototype`

##### 扩展参考

讲解原型和原型链，绝对要参考我之前写过的文章[《深入理解JS原型和闭包系列博客》](http://www.cnblogs.com/wangfupeng1988/p/3977924.html)

### 答题


###### 如何**准确**判断一个变量是数组类型

只有`instanceof`才能判断一个对象是否是真正的数组，`instanceof`的具体原理后面会讲到。

```javascript
var arr = []
arr instanceof Array // true
typeof arr // object，typeof 是无法判断是否是数组的
```

扩展：实际应用中，和数组同样重要、起同样作用并且更加灵活的数据结构还是“伪数组”或者“类数据”（jquery 就用到了）。因此，在实际应用中，只需要判断`length`属性是否是数字即可。

```javascript
var arr = []
var likeArr = {
    0: 'aaa',
    1: 'bbb',
    2: 'ccc',
    length: 3
}

typeof arr.length === 'number' // true
typeof likeArr.length === 'number' // true
```

PS：**为何需要要扩展补充？** 在面试过程中，面试官很希望能从你那里得到“惊喜”，即面的他提问的问题，你正确回答之后，还能有所补充，这样你会加分不少。在日常工作中也一样，如果你能在完成工作之后再去考虑如何更有质量、更有效率的完成工作，或者通过本次工作的总结出一种方式，能更好的完成接下来的工作，那你的 leader 绝对高看你一眼。这其实就是我们所说的**积极、主动和工作热情**，光嘴说没用，你得实干。

###### 写一个原型链继承的例子

接下来继续回答这个问题。你看其他人的培训或者看书，这个例子一般都会给你弄一些小猫小狗小动物来演示，例如

```javascript
// 动物
function Animal() {
    this.eat = function () {
        console.log('animal eat')
    }
}
// 狗
function Dog() {
    this.bark = function () {
        console.log('dog bark')
    }
}
Dog.prototype = new Animal()
// 哈士奇
var hashiqi = new Dog()

// 其实，书中是为了演示了而演示，真正的实际 JS 开发中，根本不推荐这种两层甚至三层的继承。因为工作中项目本身业务就非常复杂，代码设计上就尽量求简，越简单的东西才越容易扩展和改变。
```

这种方式也可以准确回答问题，但是这并不是面试官真正想要的，面试官想要的是一个真正基于实战的 demo 而不是只看到了书本确没有应用（我可以帮助你解读面试官的诉求，这是面试中非常关键的一点）

接下来，我将根据自己的开源项目 [wangEditor](https://github.com/wangfupeng1988/wangEditor) 中的[一段源码](https://github.com/wangfupeng1988/wangEditor/blob/v3/src/js/util/dom-core.js)进行简化，通过实际的业务场景来使用原型和继承

```JavaScript
function Element(id) {
    this.element = document.getElementById(id)
}

Element.prototype.html = function(val) {
    var elem = this.element
    if (val) {
        elem.innerHTML = val
        return this //链式操作
    } else {
        return elem.innerHTML
    }
}

Element.prototype.on = function(type, fn) {
    var elem = this.element
    elem.addEventListener(type, fn)
    return this
}

var div1 = new Element('div1')
div1.html('<p>hello</p>').on('click', function() {
    alert('clicked')
})
```



```javascript
// 构造函数
function DomElement(selector) {
    var result = document.querySelectorAll(selector)
    var length = result.length
    var i
    for (i = 0; i < length; i++) {
        this[i] = selectorResult[i]
    }
    this.length = length
}
// 修改原型
DomElement.prototype = {
    constructor: DomElement,
    get: function (index) {
        return this[index]
    },
    forEach: function (fn) {
        var i
        for (i = 0; i < this.length; i++) {
            const elem = this[i]
            const result = fn.call(elem, elem, i)
            if (result === false) {
                break
            }
        }
        return this
    },
    on: function (type, fn) {
        return this.forEach(elem => {
            elem.addEventListener(type, fn, false)
        })
    }
}

// 使用
var $div = new DomElement('div')
$div.on('click', function() {
    console.log('click')
})
```


###### 描述 new 一个对象的过程

```javascript
function Foo(name) {
    this.name = name
    this.type = 'foo'
}
var foo = new Foo('beijing')
```

- 创建一个新对象
- `this`指向这个新对象
- 执行代码，即对`this`赋值
- 返回`this`

###### zepto（或其他框架） 源码中如何使用原型链

解读优秀开源框架的源码或者设计，是成为程序大牛的毕竟之路。当然，解读源码是一件非常枯燥、成本非常高的事儿。因此我强烈推荐，不要去一意孤行闭门造车的解读枯燥的源码，而是通过在网上找到一些优秀的材料，来解读一个框架的设计。

就像一本厚厚的书，如何快速掌握书的内容？———— 知道个大致的故事结构，然后去看看别人（最好是名人）的一些点评，这样虽然花费时间很少，但是你也能比那些闷着头把书看完的人，了解到的东西更多。而且这种方式你可以高效率的阅读很多书，增加你的见识。

参考我在慕课网的视频教程[zepto设计和源码](http://www.imooc.com/learn/745)，免费的。

### 题目

- 说一下对变量提升的理解
- 说明 this 几种不同的使用场景
- 创建 10 个`<a>`标签，点击的时候弹出来对应的序号
- 如何理解作用域
- 实际开发中闭包的应用

### 知识点

本节我们将全面梳理 JS 代码的执行过程，涉及到的概念有非常多。所以大家一定要耐心听，如果你此前不了解这一块，而通过本教程掌握了这方面知识，对你的基础知识将会有质的提醒。

##### 执行上下文

先看下面的例子，你可能会对结果比较差异。当然，我不建议在实际开发中通过这种方式来炫技，我们这里演示纯粹是为了讲解知识点做一个铺垫。

```javascript
console.log(a)  // undefined
var a = 100

fn('zhangsan')  // 'zhangsan' 20
function fn(name) {
    age = 20
    console.log(name, age)
    var age
}
```

在一段 JS 脚本（即一个`<script>`标签中）执行之前，会先创建一个**全局执行上下文**环境，先把代码中即将执行的（内部函数的不算，因为你不知道函数何时执行）变量、函数声明（和“函数表达式”的区别）都拿出来。变量先暂时赋值为`undefined`，函数则先声明好可使用。这一步做完了，然后再开始正式执行程序。再次强调，这是在代码执行之前才开始的工作。

另外，一个函数在执行之前，也会创建一个**函数执行上下文**环境，跟**全局上下文**差不多，不过**函数执行上线文**中会多出`this` `arguments`和函数的参数。参数和`arguments`好理解，这里的`this`咱们需要专门讲解。

总结一下

- 范围：一段`<script>`或者一个函数
- 全局：变量定义，函数声明
- 函数：变量定义，函数声明，this，arguments

##### this

先搞明白一个很重要的概念 ———— **`this`的值是在执行的时候才能确认，定义的时候不能确认！** 为什么呢 ———— 因为`this`是执行上下文环境的一部分，而执行上下文需要在代码执行之前确定，而不是定义的时候。看如下例子

```javascript
var a = {
    name: 'A',
    fn: function () {
        console.log(this.name)
    }
}
a.fn()  // this === a
a.fn.call({name: 'B'})  // this === {name: 'B'}
var fn1 = a.fn
fn1()  // this === window
```

`this`执行会有不同，主要集中在这几个场景中

- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行
- 用于`call` `apply` `bind`

前两种情况咱们之前都介绍过了，这里只是统一的提出来，汇总一下，不再详细讲了。这里主要说第三种

```javascript
function fn() {
    console.log(this)
}
fn()  // window
fn.call({a:100})  // {a:100}  和 call 同理的还有 apply bind
```

##### 作用域

作为有 JS 基础的同学，你应该了解 JS 没有块级作用域。例如

```javascript
if (true) {
    var name = 'zhangsan'
}
console.log(name)
```

从上面的例子可以体会到作用域的概念，作用域就是一个独立的地盘，让变量不会外泄、暴露出去。上面的`name`就被暴露出去了，因此，**JS 没有块级作用域，只有全局作用域和函数作用域**。

```javascript
var a = 100
function fn() {
    var a = 200
    console.log('fn', a)
}
console.log('global', a)
fn()
```

全局作用域就是最外层的作用域，如果我们写了很多行 JS 代码，变量定义都没有用函数包括，那么他们就全部都在全局作用域中。这样的坏处就是很容易装车。

```javascript
// 张三写的代码中
var data = {a:100}

// 李四写的代码中
var data = {x:true}
```

这就是为何 jquery zepto 等库的源码，所有的代码都会放在`(function(){....})()`中。因为放在里面的所有变量，都不会被外泄和暴露，不会污染到外面，不会对其他的库或者 JS 脚本造成影响。这是函数作用域的一个体现。

##### 作用域链

首先认识一下什么叫做**自由变量**。如下代码中，`console.log(a)`要得到`a`变量，但是在当前的作用域中没有定义`a`（可对比一下`b`）。当前作用域没有定义的变量，这成为**自由变量**。自由变量如何得到 ———— 向父级作用域寻找。

```javascript
var a = 100
function fn() {
    var b = 200
    console.log(a)
    console.log(b)
}
fn()
```

如果父级也没呢？再一层一层向上寻找，直到找到全局作用域还是没找到，就宣布放弃。这种一层一层的关系，就是**作用域链**。

```javascript
var a = 100
function F1() {
    var b = 200
    function F2() {
        var c = 300
        console.log(a)
        console.log(b)
        console.log(c)
    }
    F2()
}
F1()
```

##### 闭包

直接看一个例子

```javascript
function F1() {
    var a = 100
    return function () {
        console.log(a)
    }
}
var f1 = F1()
var a = 200
f1()
```

自由变量将从作用域链中去寻找，但是**依据的是函数定义时的作用域链，而不是函数执行时**，以上这个例子就是闭包。闭包主要有两个应用场景：

- 函数作为返回值，上面的例子就是
- 函数作为参数传递，看以下例子

```javascript
function F1() {
    var a = 100
    return function () {
        console.log(a)
    }
}
function F2(f1) {
    var a = 200
    console.log(f1())
}
var f1 = F1()
F2(f1)
```

##### 扩展阅读

请参考我之前写过的文章[《深入理解JS原型和闭包系列博客》](http://www.cnblogs.com/wangfupeng1988/p/3977924.html)

### 解题


###### 说一下对变量提升的理解

函数执行时会先创建当前的上下文环境，其中这两点会产生“变量提升”的效果

- 变量定义
- 函数声明

###### 说明 this 几种不同的使用场景

- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行
- call apply bind

###### 创建 10 个`<a>`标签，点击的时候弹出来对应的序号

错误的写法

```javascript
var i, a
for (i = 0; i < 10; i++) {
    a = document.createElement('a')
    a.innerHTML = i + '<br>'
    a.addEventListener('click', function (e) {
        e.preventDefault()
        alert(i)
    })
    document.body.appendChild(a)
}
```

正确的写法

```javascript
var i
for (i = 0; i < 10; i++) {
    (function (i) {
        var a = document.createElement('a')
        a.innerHTML = i + '<br>'
        a.addEventListener('click', function (e) {
            e.preventDefault()
            alert(i)
        })
        document.body.appendChild(a)
    })(i)
}
```

上面的回答已经结束了，但是还有一点可以优化，如果能做到，那将会给你加分。提示一下，是关于 DOM 操作的性能问题的。这里先按下不表，等后面讲解性能问题的时候再说。有兴趣的可以先去查查`DocumentFragment`

###### 如何理解作用域

根据我之前写过的[《深入理解JS原型和闭包》](http://www.cnblogs.com/wangfupeng1988/p/3977924.html)系列博客，详细讲解作用域和闭包。

###### 实际开发中闭包的应用

闭包的实际应用，主要是用来封装变量。即把变量隐藏起来，不让外面拿到和修改。

```javascript
function isFirstLoad() {
    var _list = []

    return function (id) {
        if (_list.indexOf(id) >= 0) {
            return false
        } else {
            _list.push(id)
            return true
        }
    }
}

// 使用
var firstLoad = isFirstLoad()
firstLoad(10) // true
firstLoad(10) // false
firstLoad(20) // true
```

### 题目

- 同步和异步的区别是什么？分别举一个同步和异步的例子
- 一个关于`setTimeout`的笔试题
- 前端使用异步的场景有哪些

### 知识点

##### 什么是异步

先看下面的 demo，根据程序阅读起来表达的意思，应该是先打印`100`，1秒钟之后打印`200`，最后打印`300`。但是实际运营根本不是那么回事。

```javascript
console.log(100)
setTimeout(function () {
    console.log(200)
}, 1000)
console.log(300)
```

再对比以下程序。先打印`100`，再弹出`200`（等待用户确认），最后打印`300`。这个运行效果就符合预期要求。

```javascript
console.log(100)
alert(200)  // 1秒钟之后点击确认
console.log(300)
```

这俩到底有何区别？———— 第一个示例中间的步骤根本没有阻塞接下来程序的运行，而第二个示例却阻塞了后面程序的运行。前面这种表现就叫做**异步**（后面这个叫做**同步**）

为何需要异步呢？如果第一个示例中间步骤是一个 ajax 请求，现在网络比较慢，请求需要5秒钟。如果是同步，这5秒钟页面就卡死在这里啥也干不了了。

最后，前端 JS 脚本用到异步的场景主要有两个：

- 定时 `setTimeout` `setInverval`
- 网络请求，如 `ajax` `<img>`加载
- 事件绑定（后面会有解释）

ajax 代码示例

```javascript
console.log('start')
$.get('./data1.json', function (data1) {
    console.log(data1)
})
console.log('end')
```

img 代码示例（常用语打点统计）

```javascript
console.log('start')
var img = document.createElement('img')
img.onload = function () {
    console.log('loaded')
}
img.src = '/xxx.png'
console.log('end')
```

事件绑定

```javascript
console.log('start')
document.getElementById('btn1').addEventListener('click', function () {
    alert('clicked')
})
console.log('end')
```

##### 异步和单线程

JS 在客户端运行的时候，只有一个线程可运行，因此想要两件事儿同时干是不可能的。如果没有异步，我们只能同步干，就像第二个示例一样，等待过程中卡住了，但是有了异步就没有问题了。那么单线程是如何实现异步的呢？

```javascript
console.log(100)
setTimeout(function () {
    console.log(200)
})
console.log(300)
```

那上面的示例来说，有以下几点。重点从这个过程中体会**单线程**这个概念，即事情都是一步一步做的，不能两件事儿一起做。

- 执行第一行，打印`100`
- 执行`setTimeout`后，传入`setTimeout`的函数会被暂存起来，不会立即执行。
- 执行最后一行，打印`300`
- 待所有程序执行完，处于空闲状态时，会立马看有没有暂存起来的要执行。
- 发现暂存起来的`setTimeout`中的函数无需等待时间，就立即来过来执行

下面再来一个`setTimeout`的例子。规则和上面的一样，只不过这里暂存起来的函数，需要等待 1s 之后才能被执行。

```javascript
console.log(100)
setTimeout(function () {
    console.log(200)
}, 1000)
console.log(300)
```

下面再来一个 ajax 的例子。规则也是一样的，只不过这里暂存起来的函数，要等待网络请求返回之后才能被执行，具体时间不一定。

```javascript
console.log(100)
$.get('./data.json', function (data) {
    console.log(200)
})
console.log(300)
```

最后再解释一下事件绑定，如下代码。其实事件绑定的实现原理和上面的是一样的，也是会把时间暂存，但是要等待用户点击只有，才能被执行。原理是一样的，因此事件绑定在原理上来说，可以算作是异步。但是从设计上来说，还是分开好理解一些。

```javascript
console.log(100)
$btn.click(function () {
    console.log(200)
})
console.log(300)
```

**重点：异步的实现机制，以及对单线程的理解**





--------

下面的暂时先不讲

##### 异步的问题和解决方案

异步遇到的最大的问题

- callback-hell 
- 易读性差，即书写顺序和执行顺序不一致

```javascript
console.log('start')
$.get('./data1.json', function (data1) {
    console.log(data1)
    $.get('./data2.json', function (data2) {
        console.log(data2)
        $.get('./data3.json', function (data3) {
            console.log(data3)
            $.get('./data4.json', function (data4) {
                console.log(data4)
                // ...继续嵌套...
            })
        })
    })
})
console.log('end')
```

不过目前已经有了非常明确的解决方案 —— Promise，并且 Promise 放在 ES6 的标准中了。很遗憾本教程的范围不包括 ES6 ，因为 ES6 包含的内容太多了，放在这个教程中会很庞大，成本太高。

> 要想把异步讲全面，那得单独需要一门课程花5-7个小时去讲解（JS、jquery、ES6、node）。如果这样一讲，那就又带出了ES6的很多知识，又得花额外的时间去讲解，这样算下来，就得10多个小时。

我提供了一个参考链接，如果大家有本节课的基础，再去看参考链接的内容，应该能掌握异步更高级的知识。

##### 参考和扩展阅读

- [深入理解 JavaScript 异步系列（1）——基础](http://www.cnblogs.com/wangfupeng1988/p/6513070.html)
- [深入理解 JavaScript 异步系列（2）—— jquery的解决方案](http://www.cnblogs.com/wangfupeng1988/p/6515779.html)
- [深入理解 JavaScript 异步系列（3）—— ES6 中的 Promise](http://www.cnblogs.com/wangfupeng1988/p/6515855.html)
- [深入理解 JavaScript 异步系列（4）—— Generator](http://www.cnblogs.com/wangfupeng1988/p/6532713.html)
- [深入理解 JavaScript 异步系列（5）—— async await](http://www.cnblogs.com/wangfupeng1988/p/6532734.html)

### 解答


###### 同步和异步的区别是什么？分别举一个同步和异步的例子

同步会阻塞代码执行，而异步不会。`alert`是同步，`setTimeout`是异步

###### 一个关于`setTimeout`的笔试题

面试题中，`setTimeout`的基本是必会出现的

```javascript
// 以下代码执行后，打印出来的结果是什么
console.log(1)
setTimeout(function () {
    console.log(2)
}, 0)
console.log(3)
setTimeout(function () {
    console.log(4)
}, 1000)
console.log(5)
```

该题目的答案是`1 3 5 2 4`，不知道跟你答对了没有。具体的原理，我们后面再详细讲解。

###### 前端使用异步的场景有哪些

- setTimeout setInterval
- 网络请求：ajax请求、img加载
- 事件绑定（可以说一下自己的理解）


### 题目

- 获取`2017-06-10`格式的日期
- 获取随机数，要求是长度一致的字符串格式
- 写一个能遍历对象和数组的`forEach`函数

### 知识点

##### 正则表达式

如果对正则不熟悉，则先通过[《30分钟学会正则表达》](https://deerchao.net/tutorials/regex/regex.htm)来了解一下正则表达式的。

`test`函数的用法

```javascript
var ua = navigator.userAgent
var reg = /\bMicroMessenger\b/i
console.log(reg.test(ua))
```

用于`replace`的示例

```javascript
function trim(str) {
    return str.replace(/(^\s+)|(\s+$)/g, '')
}
```

`match`函数的用法

```javascript
var url = 'http://www.abc.com/path/xxx.html?a=10&b=20&c=30#topic'  // 后面的 #topic 也可能没有
var reg = /\?(.+?)(#|$)/
var matchResult = url.match(reg)
console.log(matchResult[1]) // a=10&b=20&c=30
```


略过正则表达式，不讲

-----------------

##### 日期函数

日期函数最常用的 API 如下

```javascript
Date.now()  // 获取当前时间毫秒数
var dt = new Date()
dt.getTime()  // 获取毫秒数
dt.getFullYear()  // 年
dt.getMonth()  // 月（0 - 11）
dt.getDate()  // 日（0 - 31）
dt.getHours()  // 小时（0 - 23）
dt.getMinutes()  // 分钟（0 - 59）
dt.getSeconds()  // 秒（0 - 59）
```

##### Math

Math 最常用的只有一个 API —— `Math.random()`

##### 数组常用 API

- forEach
- every
- some
- sort
- map
- filter

forEach 举例

```javascript
var arr = [1,2,3]
arr.forEach(function (item, index) {
    // 遍历数组的所有元素
    console.log(index, item)
})
```

every 举例

```javascript
var arr = [1,2,3]
var result = arr.every(function (item, index) {
    // 用来判断所有的数组元素，都满足一个条件
    if (item < 4) {
        return ture
    }
})
console.log(result)
```

some 举例

```javascript
var arr = [1,2,3]
var result = arr.some(function (item, index) {
    // 用来判断所有的数组元素，只要有一个满足条件即可
    if (item < 2) {
        return ture
    }
})
console.log(result)
```

sort 举例

```javascript
var arr = [1,4,2,3,5]
var arr2 = arr.sort(function(a, b) {
    // 从小到大排序
    return a - b
    // 从大到小排序
    // return b - a
})
console.log(arr2)
```

map 举例

```javascript
var arr = [1,2,3,4]
var arr2 = arr.map(function(item, index) {
    // 将元素重新组装，并返回
    return '<b>' + item + '</b>'
})
console.log(arr2)
```

filter 举例

```javascript
var arr = [1,2,3]
var arr2 = arr.filter(function (item, index) {
    // 通过某一个条件过滤数组
    if (item >= 2) {
        return true
    }
})
console.log(arr2)
```

##### 对象常用 API

- for-in

```javascript
var obj = {
    x: 100,
    y: 200,
    z: 300
}
var key
for (key in obj) {
    // 注意这里的 hasOwnProperty，再讲原型链时候讲过了
    if (obj.hasOwnProperty(key)) {
        console.log(key, obj[key])
    }
}
```

### 解答

##### 获取`2017-06-10`格式的日期

```javascript
function formatDate(dt) {
    if (!dt) {
        dt = new Date()
    }
    var year = dt.getFullYear()
    var month = dt.getMonth() + 1
    var date = dt.getDate()
    if (month < 10) {
        // 强制类型转换
        month = '0' + month
    }
    if (date < 10) {
        // 强制类型转换
        date = '0' + date
    }
    // 强制类型转换
    return year + '-' + month + '-' + date
}
var dt = new Date()
var formatDate = formatDate(dt)
console.log(formatDate)
```

##### 获取随机数，要求是长度一直的字符串格式

使用`Math.random()`可获取字符串，但是返回的是一个小于 1 的小数，而且小数点后面长度不同

```javascript
var random = Math.random()
var random = random + '0000000000'  // 后面加上 10 个零
var random = random.slice(0, 10)
console.log(random)
```

##### 写一个能遍历对象和数组的`forEach`函数

遍历数组使用`forEach`，而遍历对象使用`for in`，但是在实际开发中，可以使用一个函数就遍历两者，jquery 就有这样的函数

```javascript
function forEach(obj, fn) {
    var key
    if (obj instanceof Array) {
        // 准确判断是不是数组
        obj.forEach(function (item, index) {
            fn(index, item)
        })
    } else {
        // 不是数组就是对象
        for (key in obj) {
            fn(key, obj[key])
        }
    }
}

var arr = [1,2,3]
// 注意，这里参数的顺序换了，为了和对象的遍历格式一致
forEach(arr, function (index, item) {
    console.log(index, item)
})

var obj = {x: 100, y: 200}
forEach(obj, function (key, value) {
    console.log(key, value)
})
```
### 从基础知识到JSWebAPI

##### JS基础知识

前面讲解的“JS基础知识”也叫做“ES基础知识”，其实是最基础的语法知识，它包含的内容

- 变量类型和计算
- 原型和原型链
- 闭包和作用域
- 异步和单线程
- 其他（如日期、Math、各种常用API）

这些知识如果从初学者看来，或者只从表面理解，对我们日常工作根本没什么作用。此前我们讲过，JS 基础语法内置的函数有`Object` `Array` `Boolean` `Number` `String` `Function` `Date` `RegExp` `Error`，另外还有`JSON` `Math`等常用的对象。我们拿到这些东西之后，貌似什么都干不了，连在网页中打印或者弹出一个提示都不能做。

但它是一个基础，越用JS你就越能发现。

##### JS-Web-API

之前的知识什么都干不了，那怎么办呢？别着急，浏览器有它的处理方式。

此前说过，以上的JS基础知识（或者ES基础知识）是一句 ECMA-262 标准来制定的，浏览器会遵循这个标准，即在浏览器中使用此前讲过的变量、原型、闭包、异步都是没有问题的。

在此基础之上，浏览器还得让 JS 参与更多的事情，即让开发者能通过 JS 操作网页的各个地方，因此浏览器需要为 JS 在使用这些基础知识的基础上，再开发新的能力。而这些能力就需要遵循 W3C 的标准。

W3C 规定了很多内容，例如 html 规则、css 规则、JS 接口的规则，而和 JS 相关的并且面试过程中经常出现的有：

- DOM 操作
- BOM 操作
- 事件绑定
- ajax 请求（包括 http 协议）
- 存储

那 W3C 是如何规定的，以及浏览器又是如何执行的呢。举例来说，要在页面弹框就需要`alert('123')`，它学全了其实是`window.alert('123')`，那么浏览器需要做

- 定义一个 window 全局变量
- window 是一个对象，给它定义一个 alert 属性，属性值是一个函数（该函数可以调起浏览器的弹框）

当然，window 对象上有非常多的属性，这些都是浏览器给它赋值上的，当然开发者也可以对它进行扩展（符合对象的特性，可自由扩展属性）

再举一个例子，根据 id 获取一个元素是`document.getElementById('id')`，那么浏览器需要做

- 定义一个 document 全局变量
- 给它定义一个 getElementById 的属性，属性值是一个方法

但是，W3C 没有规定任何语法的东西，什么变量类型、原型、作用域、异步，它都不管。它只管定义一些可以在浏览器中用于 JS 操作页面的 API 和对象。

OK，最后，我们回过头来问一句，对于所有的 JS 来说（不是光考虑基础知识层面），它内置的全局变量有哪些？这个问题我们现在可能回答不全面，但是我们至少知道，除了之前讲过的那些，还有`window` `document`这两个，其实还有其他的很多很多个，但是现在都不用管。而且，以后只要是遇到我们没有定义，就直接拿来用的全局变量，那肯定就是浏览器内置的JS全局变量。
例如 `navigator.userAgent`

##### 总结

常说的 JS（或浏览器中执行的JS）= JS基础知识（ECMA262标准）+ JS-Web-API（W3C标准）

### 题目

- DOM 是哪种基本的数据结构？
- DOM 操作的常用 API 有哪些
- DOM 节点的 attr 和 property 有何区别

### 知识点

##### 从 HTML 到 DOM

##### DOM 的本质

讲 DOM 先从 html 讲起，讲 html 先从 XML 讲起。XML 是一种可扩展的标记语言，所谓可扩展就是它可以描述任何结构化的数据，它是一棵树！

```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
  <other>
    <a></a>
    <b></b>
  </other>
</note>
```

HTML 是一个有既定标签标准的 XML 格式，标签的名字、层级关系和属性，都被标准化（否则浏览器无法解析）。同样，它也是一棵树。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div>
        <p>this is p</p>
    </div>
</body>
</html>
```

我们开发完的 html 代码会保存到一个文档中（一般以`.html`或者`.htm`结尾），文档放在服务器上，浏览器请求服务器，这个文档被返回。因此，最终浏览器拿到的是一个文档而已，文档的内容就是 html 格式的代码。

但是浏览器要把这个文档中的 html 按照标准渲染成一个页面，此时浏览器就需要将这堆代码处理成自己能理解的东西，也得处理成 JS 能理解的东西，因为还得允许 JS 修改页面内容呢。

基于以上需求，浏览器就需要把 html 转变成 DOM，html 是一棵树，DOM 也是一棵树。对 DOM 的理解，可以暂时先抛开浏览器的内部因此，先从 JS 着手，即可以认为 DOM 就是 JS 能识别的 html 结构，一个普通的 JS 对象或者数组。

【附带一个 chrome Element 的截图】

##### DOM 节点操作

###### 获取 DOM 节点

```javascript
var div1 = document.getElementById('div1') // 元素
var divList = document.getElementsByTagName('div')  // 集合
console.log(divList.length)
console.log(divList[0])

var containerList = document.getElementsByClassName('.container') // 集合
var pList = document.querySelectorAll('p') // 集合
```

###### prototype

DOM 节点就是一个 JS 对象，它符合之前讲述的对象的特征 ———— 可扩展属性

```javascript
var pList = document.querySelectorAll('p')
var p = pList[0]
console.log(p.style.width)  // 获取样式
p.style.width = '100px'  // 修改样式
console.log(p.className)  // 获取 class
p.className = 'p1'  // 修改 class

// 获取 nodeName 和 nodeType
console.log(p.nodeName)
console.log(p.nodeType)
```

###### Attribute

property 的获取和修改，是直接改变 JS 对象，而 Attibute 是直接改变 html 的属性。两种有很大的区别

```javascript
var pList = document.querySelectorAll('p')
var p = pList[0]
p.getAttribute('data-name')
p.setAttribute('data-name', 'imooc')
p.getAttribute('style')
p.setAttribute('style', 'font-size:30px;')
```

##### DOM 树操作

新增节点

```javascript
var div1 = document.getElementById('div1')
// 添加新节点
var p1 = document.createElement('p')
p1.innerHTML = 'this is p1'
div1.appendChild(p1) // 添加新创建的元素
// 移动已有节点
var p2 = document.getElementById('p2')
div1.appendChild(p2)
```

获取父元素

```javascript
var div1 = document.getElementById('div1')
var parent = div1.parentElement
```

获取子元素

```javascript
var div1 = document.getElementById('div1')
var child = div1.childNodes
```

删除节点

```javascript
var div1 = document.getElementById('div1')
var child = div1.childNodes
div1.removeChild(child[0])
```

还有其他操作的API，例如获取前一个节点、获取后一个节点等，但是面试过程中经常考到的就是上面几个。


### 解答

###### DOM 是哪种基本的数据结构？

树

###### DOM 操作的常用 API 有哪些

- 获取节点，以及获取节点的 Attribute 和 property 
- 获取父节点 获取子节点
- 新增节点，删除节点

###### DOM 节点的 Attribute 和 property 有何区别

- property 只是一个 JS 属性的修改
- attr 是对 html 标签属性的修改

### 题目

- 如何检测浏览器的类型
- 拆解url的各部分

### 知识点


DOM 是浏览器针对下载的 HTML 代码进行解析得到的 JS 可识别的数据对象。而 BOM（浏览器对象模型）是浏览器本身的一些信息的设置和获取，例如获取浏览器的宽度、高度，设置让浏览器跳转到哪个地址。

- navigator
- screen
- location
- history
  

这些对象就是一堆非常简单粗暴的 API 没人任何技术含量，讲起来一点意思都没有，大家去 MDN 或者 w3school 这种网站一查就都明白了。面试的时候，面试官基本不会出太多这方面的题目，因为只要基础知识过关了，这些 API 即便你记不住，上网一查也都知道了。


```javascript
// navigator
var ua = navigator.userAgent
var isChrome = ua.indexOf('Chrome')
console.log(isChrome)

// screen
console.log(screen.width)
console.log(screen.height)

// location
console.log(location.href)
console.log(location.protocol) // 'http:' 'https:'
console.log(location.pathname) // '/learn/199'
console.log(location.search)
console.log(location.hash)

// history
history.back()
history.forward()
```

### 解答

##### 如何检测浏览器的类型

```javascript
var ua = navigator.userAgent
var isChrome = ua.indexOf('Chrome')
console.log(isChrome)
```

##### 拆解url的各部分

```javascript
console.log(location.href)
console.log(location.protocol) // 'http:' 'https:'
console.log(location.pathname) // '/learn/199'
console.log(location.search)
console.log(location.hash)
```

##### 开发环境介绍

概述：面试官如果跟你聊到开发环境，那就是想试探你的工作经验到底如何。因为一个人的工作环境如何，直接能体现出他的工作产出效率如何。


该部分基本不会出具体的题目来让你回答，而是以聊天的形式让你去描述，因此我们该部分不会再为大家准备题目，而是直接讲述一个合格的前端工程师，他的开发环境应该是怎样的。


- IDE（写代码的效率）
- git（代码版本管理，多人协作开发）
- JS 模块化
- 打包工具
- 上线回滚的流程

### IDE

##### 题目

- 【面试】了解哪些IDE，日常用的最多的是哪个？

##### 解答

IDE 即你用什么编辑器来编写代码。这块肯定不会出笔试题，面试题也就基本简单聊一聊，但是就这么简单一聊，聪明的面试官就能管中窥豹，了解到你平常到底是否经常写代码。

###### 了解哪些IDE，日常用的最多的是哪个？

前端最常用的 IDE 有 [webstorm](https://www.jetbrains.com/webstorm/) [sublime](https://www.sublimetext.com/) [atom](https://atom.io/) 和 [vscode](https://code.visualstudio.com/)，我们可以分别是他们的官网看一下。

webstorm 是最强大的编辑器，因为它拥有各种强大的插件和功能，但是我没有用过，因为它收费。不是因为我舍不得花钱，是因为我觉得免费的 sublime 已经够我用了。因此，跟面试官聊到 webstorm 之后，没用过没事儿，一定要知道它：第一，强大；第二，收费。

sublime 是我日常用的编辑器，第一它免费，第二它轻量、高效，第三它插件非常多。用 sublime 一定要安装各种插件配合使用，可以去网上搜一下“sublime”常用插件的安装以及用法，还有它的各种快捷键，并且亲自使用它。这里就不一一演示了，网上的教程也很傻瓜式。

atom 是 github 出品的编辑器，跟 sublime 差不多，免费并且插件丰富，而且跟 sublime 相比风格上还有些小清新。但是我用过几次就不用了，因此它第一打开的时候会比较慢，卡一下才打开。当然总体来说也是很好用的，只是个人习惯问题。

vscode 是微软出品的轻量级（相对于 visual studio 来说）编辑器，微软做 IDE 那是出了名的好，出了名的大而全，因此 vscode 也有上述 sublime 和 atom 的各种优点，但是我也是因为个人习惯问题（本人不愿意尝试没有新意的新东西），用过几次就不用了。

总结一下：

- 如果你要走大牛、大咖、逼格的路线，就用 webstorm
- 如果你走普通、屌丝、低调路线，就用 sublime
- 如果你走小清新、个性路线，就用 vscode 或者 atom
- 如果你面试，最好有一个用的熟悉，其他都会一点

最后注意：千万不要说你使用 Dreamweaver 或者 notpad++ 写前端代码，会被人鄙视的。如果你不做 .net 也不要用 Visual Studio ，不做 java 也不要用 eclipse


##### 接下来

代码版本管理

### Git

##### 题目

- 【笔试】写出一些常用的 git 命令
- 【面试】简述多人使用 git 协作开发的基本流程

##### 解答

你此前做过的项目一定要用过 git ，而且必须是命令行，如果没用过，你自己也得恶补一下。对 git 的基本应用比较熟悉的同学，可以跳过这一节了。mac os 自带 git，windows 需要安装 git 客户端，自己去网上找。

国内比较好的 git 服务商是 coding.net ，国外的是大名鼎鼎的 github 但是很容易被墙。因此建议大家注册一个 coding.net 然后创建一个项目，来练练手。

###### 写出一些常用的 git 命令

- git add .
- git checkout xxx
- git commit -m "xxx"
- git push origin master
- git pull origin master
- git stash / git stash pop

###### 简述多人使用 git 协作开发的基本流程

- git branch
- git checkout -b xxx / git checkout xxx
- git merge xxx

###### 关于 svn

关于 svn 我的态度和针对 IE 低版本浏览器的态度一样，你要查询一下资料简单了解一下，面试的时候可能会问到，但是你只要熟悉了 git 的操作，面试官不会因为你不熟悉 sv 就难为你。但是前提是你要知道一点 svn 的基本命令，自己上网一查就行。

但是 svn 和 git 的区别你得了解。svn 是每一步操作都离不开服务器，创建分支，提交代码都需要连接服务器。而 git 就不一样了，你可以在本地创建分支，提交代码，最后再一起 push 到服务器上。因此，git 拥有 svn 的所有功能，但是却比 svn 强大的多。（git 是 linux 的创始人发明的东西，因此也倍得推崇）

##### 接下来

接下来介绍 JS 模块化的考点

### 模块化

这一小节就不出题目了，因为它本身就是一个题目，范围也比较单一，就是模块化。

##### 为何需要模块化

###### 原始情况

规模较大的前端项目，不可能使用一个 JS 文件就能写完，不同的功能需要封装到不同的 JS 文件中，这样便于开发也便于维护。

项目的基础库`util.js`

```js
function getFormatDate(date, type) {
    // type === 1 返回 2017-06-15
    // type === 2 返回 2017年6月15日 格式
    // ……
}
```

项目有好多个业务，不同业务需要的日期格式不一样，因此每个业务有一个基础库`a-util.js`

```js
function aGetFormatDate(date) {
    return getFormatDate(date, 2) // 要求返回 2017年6月15日 格式
}
```

具体落实这个业务的功能层面，就需要使用业务的基础库，定义`a.js`

```js
var dt = new Date()
console.log(aGetFormatDate(dt))
```

这样，我们再使用`a.js`的时候，就需要去这样引用

```html
<script src="util.js"></script>
<script src="a-util.js"></script>
<script src="a.js"></script>
```

这样使用会有两个问题：

- 这些代码中的函数必须是全局变量，才能暴露给使用方，但是全局变量会造成很严重的污染，很容易覆盖别人的或者被别人覆盖
- `a.js`知道要引用`a-util.js`，但是他知道还需要依赖于`util.js`吗？如果不知道，就漏掉，就会报错

###### 使用模块化之后

模块化之后，我们的代码大体上要这么写（只是代码描述，并不一定真的这么写）

util.js

```js
export {
    getFormatDate: function (date, type) {
        // type === 1 返回 2017-06-15
        // type === 2 返回 2017年6月15日 格式
    }
}
```

a-util.js

```js
var getFormatDate = require('util.js')
export {
    aGetFormatDate: function (date) {
        return getFormatDate(date, 2) // 要求返回 2017年6月15日 格式
    }
}
```

a.js

```js
var aGetFormatDate = require('a-util.js')
var dt = new Date()
console.log(aGetFormatDate(dt))
```

这样，我们在使用时

- 直接`<script src="a.js"></script>`，其他的根据依赖关系自动引用
- 那两个函数，没必要做成全局变量，不会带来污染和覆盖

以上只是我们理想的两个状态，接下来就说一下具体该如何去实现。

##### AMD

AMD 模块化规范是比较早提出的，现在也是比较成熟的模块化规范，代表工具是`require.js`，使用之后它会定义两个全局函数

- define 定义一个变量并返回，可供其他js引用
- require 引用其他已经定义好的变量
- 依赖的代码会自动、异步加载

拿上面的例子来做一个样例

首先是 util.js

```js
define(function () {
    return {
        getFormatDate: function (date, type) {
            if (type === 1) {
                return '2017-06-15'
            }
            if (type === 2) {
                return '2017年6月15日'
            }
        }
    }
})
```

然后是 a-util.js

```js
define(['./util.js'], function (util) {
    return {
        aGetFormatDate: function (date) {
            return util.getFormatDate(date, 2)
        }
    }
})
```

最后是 a.js

```js
define(['./a-util.js'], function (aUtil) {
    return {
        printDate: function (date) {
            console.log(aUtil.aGetFormatDate(date))
        }
    }
})
```

接下来是如何引用，我们还得定义一个`main.js`

```js
require(['./a.js'], function (a) {
    var date = new Date()
    a.printDate(date)
})
```

然后在页面中引用`<script src="js/require.js" data-main="./main.js"></script>`，运行时注意，各个js文件会异步加载

##### CommonJS

CommonJS 是 nodejs 中模块定义的规范，但是这种规范越来越被放在前端开发来使用（当然这需要构建工具的编译，下一节讲述），原因如下

- 前端开发依赖的插件和库，都可以从 npm 中获取
- 构建工具的高度自动化，使得使用 npm 的成本非常低

CommonJS 不会异步加载各个JS，而是同步一次性加载出来

我们先来看一下 CommonJS 的输入和输出都是什么规范，然后下一节通过结合构建工具和 npm 一起演示一下使用方法。

util.js

```js
module.exports = {
    getFormatDate: function (date, type) {
        if (type === 1) {
            return '2017-06-15'
        }
        if (type === 2) {
            return '2017年6月15日'
        }
    }
}
```

a-util.js

```js
var util = require('util.js')
module.exports = {
    aGetFormatDate: function (date) {
        return util.getFormatDate(date, 2)
    }
}
```

##### AMD 和 CommonJS 的不同使用场景

CommonJS 解决的问题和 AMD 一样，只不过是不同的标准而已，他们没有孰好孰坏之分，只是不同的工具使用场景不一样而已。

- 使用 AMD：各种代码都是自己定义的，不用依赖于 npm
- 使用 CommonJS：依赖于 npm

直接看代码演示 /code/webpack

### 上线和回归

##### 上线和回滚的流程

###### 介绍

- 上线和回滚是开发过程中的重要流程
- 各个公司上线和回滚的流程都不一样
- 由有具体的工具或者系统来搞定，无需我们关心细节
- 你也许没有体会过这类规范的流程，但是你要知道一些要点
- 只说要点，不详细讲解，也没法详细讲解

###### 上线原理

- 将测试完成的代码提交到git版本库的master分支
- 将当前服务器的代码全部打包并记录版本号，备份
- 将master分支的代码提交覆盖到线上服务器，生成新版本号

###### 回滚原理

- 将当前服务器的代码打包并记录版本号，备份
- 将备份的上一个版本号解压，覆盖到线上服务器，并生成新的版本号

##### linux 服务器的基本命令

线上服务器一般使用 linux 服务器，而且是 server 版本的 linux，没有桌面也没有鼠标，如何操作呢？

**登录**

入职之后，一般会有现有的用户名和密码，拿来之后直接登录就行。运行 `ssh name@server` 然后输入密码即可登录

**目录操作**

- 创建目录 `mkdir`
- 删除目录 `rm -rf`
- 定位目录 `cd `
- 查看目录文件 `ls` `ll`
- 修改目录名 `mv `
- 拷贝目录 `cp`

**文件操作**

- 创建文件 `touch ` `vi `
- 删除文件 `rm`
- 修改文件名 `mv`
- 拷贝文件 `cp` `scp`

**文件内容操作**

- 查看文件 `cat` `head` `tail`
- 编辑文件内容 `vi `
- 查找文件内容 `grep `

### 题目

- 从输入url到得到html的详细过程
- window.onload 和 DOMContentLoaded 的区别

### 知识点

##### 浏览器加载资源的过程

###### 加载资源的形式

- 输入 url 加载 html
- http://coding.m.imooc.com
- 加载 html 中的静态资源
- `<script src="/static/js/jquery.js"></script>`

###### 加载一个资源的过程

- 浏览器根据 DNS 服务器得到域名的 IP 地址
- 向这个 IP 的机器发送 http 请求
- 服务器收到、处理并返回 http 请求
- 浏览器得到返回内容

##### 浏览器渲染页面的过程

- 根据 HTML 结构生成 DOM Tree
- 根据 CSS 生成 CSS Rule
- 将 DOM 和 CSSOM 整合形成 RenderTree
- 根据 RenderTree 开始渲染和展示
- 遇到`<script>`时，会执行并阻塞渲染

##### 几个示例

###### 最简单的页面

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>test</p>
</body>
</html>
```

###### 引用 css

css 内容

```css
div {
    width: 100%;
    height: 100px;
    font-size: 50px;
}
```

html 内容

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="test.css">
</head>
<body>
    <div>test</div>
</body>
</html>
```

最后思考为何要把 css 放在 head 中？？？


###### 引入 js

js 内容

```js
document.getElementById('container').innerHTML = 'update by js'
```

html 内容

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container">default</div>
    <script src="index.js"></script>
    <p>test</p>
</body>
</html>
```

思考为何要把 JS 放在 body 最后？？？

###### 有图片

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>test</p>
    <p><img src="test.png"/></p>
    <p>test</p>
</body>
</html>
```

引出`window.onload`和`DOMContentLoaded`

```js
window.addEventListener('load', function () {
    // 页面的全部资源加载完才会执行，包括图片、视频等
})
document.addEventListener('DOMContentLoaded', function () {
    // DOM 渲染完即可执行，此时图片、视频还可能没有加载完
})
```

### 解答

##### 从输入url到得到html的详细过程

- 浏览器根据 DNS 服务器得到域名的 IP 地址
- 向这个 IP 的机器发送 http 请求
- 服务器收到、处理并返回 http 请求
- 浏览器得到返回内容

##### window.onload 和 DOMContentLoaded 的区别

- 页面的全部资源加载完才会执行，包括图片、视频等
- DOM 渲染完即可执行，此时图片、视频还没有加载完
