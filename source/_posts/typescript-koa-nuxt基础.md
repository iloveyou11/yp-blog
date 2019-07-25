---
title: 前端技术
date: 2019-07-24
categories: 前端技术
author: yangpei
tags:
  - typescript
  - koa
  - nuxt
comments: true
cover_picture: /images/banner.jpg
---

## typescript

### 0、基本配置（自动编译）

**vscode 配置自动编译 ts 代码**
1、运行 tsc --init，生成配置文件，改 outDir
1、运行 tsc --init，生成配置文件，改 outDir
2、运行任务，选择监视 tsconfig

### 一、typescript 的数据类型(存在类型校验)

**布尔、数字、字符串**

```typescript
let flag: boolean = true;
let num: number = 123;
let str: string = "hello";
```

**数组**

```typescript
let arr1: number[] = [1, 2, 3];
let arr2: any[] = [1, "ji", 3];
let arr3: Array<number> = [1, 2, 3];
```

**元祖(属于数组的一种)**

```typescript
let tuple: [number, string] = [123, "hello"];
```

**枚举**

```typescript
enum Status {
  success = 1,
  error = 2
}
enum Color {
  blue,
  red,
  white
} //如果未赋值，结果打印下标
//let s: Status = Status.success
//console.log(s);
```

**任意类型**

```typescript
let random: any = 123;
//null和undefined 其他类型的子类型
let num1: number | undefined; //定义未赋值就是undefined
let num2: number | null | undefined;
```

**空类型(方法没有返回值)**

```typescript
function run1(): void {
  console.log("void");
}
function run2(): number {
  return 1;
}
```

**never 类型(表示从来不会出现的值，基本用不到)**

```typescript
function error(message: string): never {
  throw new Error(message);
}
```

**对象**

```typescript
declare function create(o: object | null): void;
create({ prop: 0 });
```

## 二、typescript 函数

**1、函数声明**

```typescript
function fn1(): string {
  return "run";
}
let fn2 = function(): string {
  return "run2";
};
```

**2、方法传参**

```typescript
function getInfo1(name: string, age: number): string {
  return `${name}---${age}`;
}
let getInfo2 = function(name: string, age: number): string {
  return `${name}---${age}`;
};
```

**3、可选参数**

```typescript
function get(name: string, age?: number): string {
  if (age) {
    return `${name}---${age}`;
  } else {
    return `${name}---年龄保密`;
  }
}
```

**4、默认参数**

```typescript
function get1(name: string, age: number = 20): string {
  return `${name}---${age}`;
}
```

**5、剩余参数**

```typescript
function sum(a: number, b: number, c: number): number {
  return a + b + c;
}

// 三点运算符接收形参
function sum1(...result: number[]): number {
  let sum = 0;
  for (let i = 0; i < result.length; i++) {
    sum += result[i];
  }
  return sum;
}
function sum2(initNum: number, ...result: number[]): number {
  let sum = initNum;
  for (let i = 0; i < result.length; i++) {
    sum += result[i];
  }
  return sum;
}
```

**6、函数重载(es5 中同名方法会存在替换)**

```typescript
function info1(name: string): string;
function info1(age: number): number;
function info1(str: any): any {
  if (typeof str === "string") {
    return `我叫${str}`;
  } else {
    return `我的年龄是${str}`;
  }
}
//console.log(info1('Amy'));
//console.log(info1(12));

function info2(name: string): string;
function info2(name: string, age: number): string;
function info2(name: any, age?: any): any {
  if (age) {
    return `我叫${name},我的年龄是${age}`;
  } else {
    return `我叫${name}`;
  }
}
```

**7、箭头函数(this 指向上下文)**

```typescript
setTimeout(() => {
  alert("run");
}, 1000);
```

### 三、typescript 中类

es5 的继承(原型链+对象冒充实现继承)

```typescript
function Person(name, age) {
  this.name = name;
  this.age = age;
}
function Student() {
  Person.call(this, name, age);
  //对象冒充(只能继承构造函数中的属性和方法,可以向父类传参)
}
Student.prototype = new Person();
//原型链继承(既可以继承构造函数中的属性和方法，也可以继承原型链上的属性和方法,但是实例化时无法向父类传参)
Student.prototype = Person.prototype; //另一种写法
```

**1、定义类**

```typescript
class Person {
  name: string; //省略了public关键字
  constructor(name: string) {
    this.name = name;
  }
  run(): void {
    console.log(this.name);
  }
  getName(): string {
    return this.name;
  }
  setName(name: string): void {
    this.name = name;
  }
}
```

**2、实现继承 extends super**

```typescript
class Student extends Person {
​    constructor(name: string) {
​        super(name)
​    }
​    **子类扩展方法，子类也可以覆盖父类的方法
​    work(): void {
​        alert('在运动')
​    }
}
```

**3、类修饰符(public protected private)**
public:在类内部、子类、和外部均可以访问
protected:在类内部和子类可以访问，外部无法访问
private:私有，只有类内部可以访问
属性如果不加修饰符，默认是 public

**4、静态属性&&静态方法(直接使用类名调用即可)**
应用举例(jquery)

```typescript
function $(element) {
  return new Base(element);
}
//静态方法
$.get = function() {};
function Base(element) {
  this.element = element;
  this.css = function(attr, value) {
    this.element.style.attr = value;
  };
}
```

```typescript
class Person1 {
  name: string; //省略了public关键字
  constructor(name: string) {
    this.name = name;
  } //静态属性
  static sex = "男"; //静态方法，无法直接调用类的属性，只能调用静态属性
  static print(): void {
    alert("静态方法");
  } //实例方法
  getName(): string {
    return this.name;
  }
  setName(name: string): void {
    this.name = name;
  }
}
```

**5、多态(父类定义方法不去实现，让继承它的子类去实现，每个子类有不同的表现)**

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  eat() {
    console.log("吃的方法");
  }
}
class Dog extends Animal {
  constructor(name: string) {
    super(name);
  }
  eat() {
    return this.name + "吃肉";
  }
}
class Cat extends Animal {
  constructor(name: string) {
    super(name);
  }
  eat() {
    return this.name + "吃鱼";
  }
}
```

**6、抽象类(abstract 关键字，无法创造抽象类的实例)**
抽象类和抽象方法用来定义标准，要求子类必须实现抽象类中的所有方法

```typescript
abstract class Animal1 {
  public name: string;
  constructor(name: string) {
    this.name = name;
  }
  abstract eat(): any;
}
class Dog1 extends Animal1 {
  constructor(name: any) {
    super(name);
  }
  eat() {
    console.log(`${this.name}吃肉`);
  }
}
```

### 四、typescript 接口（定义标准）

**1、属性接口**

```typescript
//对传入地值进行约束
function print_label1(label: string): void {
  console.log(label);
}
function print_label2(labelInfo: { label: string }): void {
  console.log(labelInfo);
}
//接口（批量方法约束）
interface FullName {
  firstname: string;
  lastname: string; //可选属性 lastname?: string;
}
//传到的参数必须包含firstname、lastname
function printName(name: FullName) {
  console.log(`${name.firstname}---${name.lastname}`);
}
```

**2 函数类型接口**

```typescript
interface encrypt {
  (key: string, value: string): string;
}

let md5: encrypt = function(key: string, value: string) {
  return key + value;
};
let sha1: encrypt = function(key: string, value: string) {
  return key + "---" + value;
};
```

**3 可索引接口(对数组\对象的约束,不常用)**

```typescript
interface UserArr {
  [index: number]: string;
}
let arr: UserArr = ["1", "2", "3"];

interface UserObj {
  [index: string]: string;
}
let obj: UserObj = { name: "zhangsan", age: "12" };
```

**4 类类型接口(对类的约束,和抽象类很相似)**

```typescript
interface AnimalI {
  name: string;
  eat(str: string): void;
}
class a1 implements AnimalI {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  eat(food: string) {
    console.log(`我是${this.name},我吃${food}`);
  }
}
```

**5 接口扩展(接口集成接口,类实现接口时需要实现所有的方法)**

```typescript
interface Ani {
  eat(): void;
}
//接口也可以相互继承
interface Per extends Ani {
  work(): void;
}
class Programmer {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  coding(code: string) {
    console.log(this.name + code);
  }
}
//集成父类+实现接口
class Web1 extends Programmer implements Per {
  constructor(name: string) {
    super(name);
  }
  eat() {}
  work() {}
}
```

### 五 typescript 泛型

**1 泛型函数**

```typescript
//泛型就是解决类\接口\方法的复用性 以及对不特定数据类型的支持
function getData(value: string): string {
  return value;
}
//如果要传入什么类型,返回什么类型,就要使用到泛型
function getData1<T>(value: T): T {
  return value;
}
getData1<number>(123);
getData1<string>("abc");
```

**2 泛型类**

```typescript
//(原始)
class MinClass {
  list: number[] = [];
  add(value: number): void {
    this.list.push(value);
  }
  min(): number {
    let min = this.list[0];
    for (let i = 0; i < this.list.length; i++) {
      if (min > this.list[i]) {
        min = this.list[i];
      }
    }
    return min;
  }
}
//(泛型)
class MinClass1<T> {
  public list: T[] = [];
  add(value: T): void {
    this.list.push(value);
  }
  min(): T {
    let min = this.list[0];
    for (let i = 0; i < this.list.length; i++) {
      if (min > this.list[i]) {
        min = this.list[i];
      }
    }
    return min;
  }
}

let m1 = new MinClass1<number>();
m1.add(1);
m1.add(2);
m1.add(3);
alert(m1.min());

let m2 = new MinClass1<string>();
m2.add("a");
m2.add("b");
m2.add("c");
alert(m2.min());
```

**3 泛型接口**

```typescript
//(普通函数类型接口)
interface ConfigFn0 {
  (value1: string, value2: string): string;
}
let setData0: ConfigFn0 = function(value1: string, value2: string) {
  return value1 + value2;
};
//泛型接口(写法1)
interface ConfigFn1 {
  <T>(value1: T): T;
}
let setData1: ConfigFn1 = function<T>(value: T): T {
  return value;
};
setData1 < string > "abdc";
//泛型接口(写法2)
interface ConfigFn2<T> {
  (value1: T): T;
}
function myGetData<T>(value: T): T {
  return value;
}
let myFn: ConfigFn2<string> = myGetData;
```

### 六、其他特性

```typescript
function reverse<T>(items: T[]): T[] {
  const toreturn = [];
  for (let i = items.length - 1; i >= 0; i--) {
    toreturn.push(items[i]);
  }
  return toreturn;
}
const sample = [1, 2, 3];
const reversed = reverse(sample);
console.log(reversed); // 3, 2, 1
// Safety
reversed[0] = "1"; // Error
reversed = ["1", "2"]; // Error

reversed[0] = 1; // ok
reversed = [1, 2]; // ok
```

**2、联合类型**

```typescript
function formatCommandline(command: string[] | string) {
  let line = "";
  if (typeof command === "string") {
    line = command.trim();
  } else {
    line = command.join(" ").trim();
  }
  // Do stuff with line: string
}

interface Square {
  kind: "square";
  size: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

type Shape = Square | Rectangle;
```

**3、交叉类型**
在 JavaScript 中， extend 是一种非常常见的模式，在这种模式中，你可以从两个对象中创建一个新对象，新对象会拥有着两个对象所有的功能。交叉类型可以让你安全的使用此种模式：

```typescript
function extend<T, U>(first: T, second: U): T & U {
  const result = <T & U>{};
  for (let id in first) {
    (<T>result)[id] = first[id];
  }
  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      (<U>result)[id] = second[id];
    }
  }

  return result;
}
const x = extend({ a: "hello" }, { b: 42 });
// 现在 x 拥有了 a 属性与 b 属性
const a = x.a;
const b = x.b;
```

**4、元组类型**

```typescript
let nameNumber: [string, number];
// Ok
nameNumber = ["Jenny", 221345];
// Error
nameNumber = ["Jenny", "221345"];
// 将其与 TypeScript 中的解构一起使用：
const [name, num] = nameNumber;
```

**5、类型别名**

```typescript
type StrOrNum = string | number;
// 使用
let sample: StrOrNum;
sample = 123;
sample = "123";
// 会检查类型
sample = true; // Error

type Text = string | { text: string };
type Coordinates = [number, number];
type Callback = (data: string) => void;
```

**_tips_**
如果你需要使用类型注解的层次结构，请使用接口。它能使用 implements 和 extends
为一个简单的对象类型（像例子中的 Coordinates）使用类型别名，仅仅有一个语义化的作用。与此相似，当你想给一个联合类型和交叉类型使用一个语意化的名称时，一个类型别名将会是一个好的选择。

**6、类型断言**

```typescript
const foo = {};
foo.bar = 123; // Error: 'bar' 属性不存在于 ‘{}’
foo.bas = "hello"; // Error: 'bas' 属性不存在于 '{}'
// 这里的代码发出了错误警告，因为 foo 的类型推断为 {}，即是具有零属性的对象。因此，你不能在它的属性上添加 bar 或 bas
interface Foo {
  bar: number;
  bas: string;
}
const foo = {} as Foo;
foo.bar = 123;
foo.bas = "hello";
```

双重断言

```typescript
function handler(event: Event) {
  const element = event as HTMLElement; // Error: 'Event' 和 'HTMLElement' 中的任何一个都不能赋值给另外一个
}
//如果你仍然想使用那个类型，你可以使用双重断言。首先断言成兼容所有类型的 any，编译器将不会报错：
function handler(event: Event) {
  const element = (event as any) as HTMLElement; // ok
}
```

**7、类型保护**

```typescript
function doSome(x: number | string) {
  if (typeof x === "string") {
    // 在这个块中，TypeScript 知道 `x` 的类型必须是 `string`
    console.log(x.subtr(1)); // Error: 'subtr' 方法并没有存在于 `string` 上
    console.log(x.substr(1)); // ok
  }
  x.substr(1); // Error: 无法保证 `x` 是 `string` 类型
}
function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  }
  if (arg instanceof Bar) {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 这个块中，一定是 'Bar'
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
function doStuff(q: A | B) {
  if ("x" in q) {
    // q: A
  } else {
    // q: B
  }
}
function doStuff(arg: Foo | Bar) {
  if (arg.kind === "foo") {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 一定是 Bar
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
```

**8、自变量类型**

```typescript
type CardinalDirection = "North" | "East" | "South" | "West";

function move(distance: number, direction: CardinalDirection) {
  // ...
}
move(1, "North"); // ok
move(1, "Nurth"); // Error

type OneToFive = 1 | 2 | 3 | 4 | 5;
type Bools = true | false;
```

**_使用用例_**
TypeScript 枚举类型是基于数字的，你可以使用带字符串字面量的联合类型，来模拟一个基于字符串的枚举类型，就好像上文中提出的 CardinalDirection。你甚至可以使用下面的函数来生成 key: value 的结构：
// 用于创建字符串列表映射至 `K: V` 的函数

```typescript
// 用于创建字符串列表映射至 `K: V` 的函数
function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}
// 创建 K: V
const Direction = strEnum(["North", "South", "East", "West"]);
// 创建一个类型
type Direction = keyof typeof Direction;
// 简单的使用
let sample: Direction;
sample = Direction.North; // Okay
sample = "North"; // Okay
sample = "AnythingElse"; // ERROR!
```

**9、readonly**

```typescript
function foo(config: { readonly bar: number; readonly bas: number }) {
  // ..
}
type Foo = {
  readonly bar: number;
  readonly bas: number;
};
class Foo {
  readonly bar = 1; // OK
  readonly baz: string;
  constructor() {
    this.baz = "hello"; // OK
  }
}
```

这有一个 Readonly 的映射类型，它接收一个泛型 T，用来把它的所有属性标记为只读类型：

```typescript
type Foo = {
  bar: number;
  bas: number;
};
type FooReadonly = Readonly<Foo>;
const foo: Foo = { bar: 123, bas: 456 };
const fooReadonly: FooReadonly = { bar: 123, bas: 456 };
foo.bar = 456; // ok
fooReadonly.bar = 456; // Error: bar 属性只读
```

**_设置为绝对的而不可变_**
你甚至可以把索引签名标记为只读：

```typescript
interface Foo {
  readonly [x: number]: number;
}
// 使用
const foo: Foo = { 0: 123, 2: 345 };
console.log(foo[0]); // ok（读取）
foo[0] = 456; // Error: 属性只读
```

**_自动推断_**
在一些情况下，编译器能把一些特定的属性推断为 readonly，例如在一个 class 中，如果你有一个只含有 getter 但是没有 setter 的属性，他能被推断为只读：

```typescript
class Person {
  firstName: string = "John";
  lastName: string = "Doe";

  get fullName() {
    return this.firstName + this.lastName;
  }
}
const person = new Person();
console.log(person.fullName); // John Doe
person.fullName = "Dear Reader"; // Error, fullName 只读
```

_readonly 与 const 的不同点_
const 用于变量；变量不能重新赋值给其他任何事物。
readonly 用于属性；用于别名，可以修改属性；

**10、详解泛型**
**_使用泛型_**
例如当你想创建一个字符串的队列时，你将不得不再次修改相当大的代码。我们真正想要的一种方式是无论什么类型被推入队列，被推出的类型都与推入类型一样。当你使用泛型时，这会很容易：

// 创建一个泛型类

```typescript
// 创建一个泛型类
class Queue<T> {
  private data: T[] = [];
  push = (item: T) => this.data.push(item);
  pop = (): T | undefined => this.data.shift();
}

// 简单的使用
const queue = new Queue<number>();
queue.push(0);
queue.push("1"); // Error：不能推入一个 `string`，只有 number 类型被允许
```

**_tip:_**
你可以随意调用泛型参数，当你使用简单的泛型时，泛型常用 T、U、V 表示。如果在你的参数里，不止拥有一个泛型，你应该使用一个更语义化名称，如 TKey 和 TValue （通常情况下，以 T 做为泛型前缀也在如 C++ 的其他语言里做为模版。）

## koa

### 一、开始

**async/await 使用**

```javascript
function getSyncTime() {
  return new Promise((resolve, reject) => {
    try {
      let startTime = new Date().getTime();
      setTimeout(() => {
        let endTime = new Date().getTime();
        let data = endTime - startTime;
        resolve(data);
      }, 500);
    } catch (err) {
      reject(err);
    }
  });
}

async function getSyncData() {
  let time = await getSyncTime();
  let data = `endTime - startTime = ${time}`;
  return data;
}

async function getData() {
  let data = await getSyncData();
  console.log(data);
}

getData();
```

**中间件的使用（async 中间件只能在 koa v2 中使用）**

```javascript
/* ./middleware/logger-async.js */
function log(ctx) {
  console.log(ctx.method, ctx.header.host + ctx.url);
}

module.exports = function() {
  return async function(ctx, next) {
    log(ctx);
    await next();
  };
};

const Koa = require("koa"); // koa v2
const loggerAsync = require("./middleware/logger-async");
const app = new Koa();

app.use(loggerAsync());

app.use(ctx => {
  ctx.body = "hello world!";
});

app.listen(3000);
console.log("the server is starting at port 3000");
```

### 二、路由

**koa-router 路由的使用**

```javascript
const Koa = require("koa");
const fs = require("fs");
const app = new Koa();
const Router = require("koa-router");

// 子路由1
let home = new Router();
home.get("/", async ctx => {
  let html = `
      <ul>
        <li><a href="/page/helloworld">/page/helloworld</a></li>
        <li><a href="/page/404">/page/404</a></li>
      </ul>
    `;
  ctx.body = html;
});

// 子路由2
let page = new Router();
page
  .get("/404", async ctx => {
    ctx.body = "404 page!";
  })
  .get("/helloworld", async ctx => {
    ctx.body = "helloworld page!";
  });

// 装载所有子路由
let router = new Router();
router.use("/", home.routes(), home.allowedMethods());
router.use("/page", page.routes(), page.allowedMethods());

// 加载路由中间件
app.use(router.routes()).use(router.allowedMethods());
// 监听端口
app.listen(3000, () => {
  console.log("[demo] route-use-middleware is starting at port 3000");
});
```

### 三、请求数据

**请求数据的获取** 1.是从上下文中直接获取
请求对象 ctx.query，返回如 { a:1, b:2 }
请求字符串 ctx.querystring，返回如 a=1&b=2 2.是从上下文的 request 对象中获取
请求对象 ctx.request.query，返回如 { a:1, b:2 }
请求字符串 ctx.request.querystring，返回如 a=1&b=2

**_get_**

```javascript
app.use(async ctx => {
  let url = ctx.url;
  // 从上下文的request对象中获取
  let request = ctx.request;
  let req_query = request.query;
  let req_querystring = request.querystring;
  // 从上下文中直接获取
  let ctx_query = ctx.query;
  let ctx_querystring = ctx.querystring;

  ctx.body = {
    url,
    req_query,
    req_querystring,
    ctx_query,
    ctx_querystring
  };
});
```

**_post_**

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  if (ctx.url === "/" && ctx.method === "GET") {
    // 当GET请求时候返回表单页面
    let html = `
      <h1>koa2 request post demo</h1>
      <form method="POST" action="/">
        <p>userName</p>
        <input name="userName" /><br/>
        <p>nickName</p>
        <input name="nickName" /><br/>
        <p>email</p>
        <input name="email" /><br/>
        <button type="submit">submit</button>
      </form>
    `;
    ctx.body = html;
  } else if (ctx.url === "/" && ctx.method === "POST") {
    // 当POST请求的时候，解析POST表单里的数据，并显示出来
    let postData = await parsePostData(ctx);
    ctx.body = postData;
  } else {
    // 其他请求显示404
    ctx.body = "<h1>404！！！ o(╯□╰)o</h1>";
  }
});

// 解析上下文里node原生请求的POST参数
function parsePostData(ctx) {
  return new Promise((resolve, reject) => {
    try {
      let postdata = "";
      ctx.req.addListener("data", data => {
        postdata += data;
      });
      ctx.req.addListener("end", function() {
        let parseData = parseQueryStr(postdata);
        resolve(parseData);
      });
    } catch (err) {
      reject(err);
    }
  });
}

// 将POST请求参数字符串解析成JSON
function parseQueryStr(queryStr) {
  let queryData = {};
  let queryStrList = queryStr.split("&");
  console.log(queryStrList);
  for (let [index, queryStr] of queryStrList.entries()) {
    let itemList = queryStr.split("=");
    queryData[itemList[0]] = decodeURIComponent(itemList[1]);
  }
  return queryData;
}

app.listen(3000, () => {
  console.log("[demo] request post is starting at port 3000");
});
```

**_koa-bodyparser 中间件_**
对于 POST 请求的处理，koa-bodyparser 中间件可以把 koa2 上下文的 formData 数据解析到 ctx.request.body 中

```javascript
const Koa = require("koa");
const app = new Koa();
const bodyParser = require("koa-bodyparser");

// 使用ctx.body解析中间件
app.use(bodyParser());

app.use(async ctx => {
  if (ctx.url === "/" && ctx.method === "GET") {
    // 当GET请求时候返回表单页面
    let html = `
      <h1>koa2 request post demo</h1>
      <form method="POST" action="/">
        <p>userName</p>
        <input name="userName" /><br/>
        <p>nickName</p>
        <input name="nickName" /><br/>
        <p>email</p>
        <input name="email" /><br/>
        <button type="submit">submit</button>
      </form>
    `;
    ctx.body = html;
  } else if (ctx.url === "/" && ctx.method === "POST") {
    // 当POST请求的时候，解析POST表单里的数据，并显示出来
    let postData = ctx.request.body;
    ctx.body = postData;
  } else {
    // 其他请求显示404
    ctx.body = "<h1>404！！！ o(╯□╰)o</h1>";
  }
});

app.listen(3000, () => {
  console.log("[demo] request post is starting at port 3000");
});
```

### 四、静态资源加载

```javascript
const Koa = require("koa");
const path = require("path");
const static = require("koa-static");

const app = new Koa();

// 静态资源目录对于相对入口文件index.js的路径
const staticPath = "./static";

app.use(static(path.join(__dirname, staticPath)));

app.use(async ctx => {
  ctx.body = "hello world";
});

app.listen(3000, () => {
  console.log("[demo] static-use-middleware is starting at port 3000");
});
```

### 五、cookie 和 session

**cookie**
koa 提供了从上下文直接读取、写入 cookie 的方法
ctx.cookies.get(name, [options]) 读取上下文请求中的 cookie
ctx.cookies.set(name, value, [options]) 在上下文中写入 cookie

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  if (ctx.url === "/index") {
    ctx.cookies.set("cid", "hello world", {
      domain: "localhost", // 写cookie所在的域名
      path: "/index", // 写cookie所在的路径
      maxAge: 10 * 60 * 1000, // cookie有效时长
      expires: new Date("2017-02-15"), // cookie失效时间
      httpOnly: false, // 是否只用于http请求中获取
      overwrite: false // 是否允许重写
    });
    ctx.body = "cookie is ok";
  } else {
    ctx.body = "hello world";
  }
});

app.listen(3000, () => {
  console.log("[demo] cookie is starting at port 3000");
});
```

**session**
koa2 原生功能只提供了 cookie 的操作，但是没有提供 session 操作。session 就只用自己实现或者通过第三方中间件实现。在 koa2 中实现 session 的方案有一下几种

如果 session 数据量很小，可以直接存在内存中
如果 session 数据量很大，则需要存储介质存放 session 数据

将 session 存放在 MySQL 数据库中
需要用到中间件
koa-session-minimal 适用于 koa2 的 session 中间件，提供存储介质的读写接口 。
koa-mysql-session 为 koa-session-minimal 中间件提供 MySQL 数据库的 session 数据读写操作。
将 sessionId 和对于的数据存到数据库
将数据库的存储的 sessionId 存到页面的 cookie 中
根据 cookie 的 sessionId 去获取对于的 session 信息

```javascript
const Koa = require("koa");
const session = require("koa-session-minimal");
const MysqlSession = require("koa-mysql-session");

const app = new Koa();

// 配置存储session信息的mysql
let store = new MysqlSession({
  user: "root",
  password: "abc123",
  database: "koa_demo",
  host: "127.0.0.1"
});

// 存放sessionId的cookie配置
let cookie = {
  maxAge: "", // cookie有效时长
  expires: "", // cookie失效时间
  path: "", // 写cookie所在的路径
  domain: "", // 写cookie所在的域名
  httpOnly: "", // 是否只用于http请求中获取
  overwrite: "", // 是否允许重写
  secure: "",
  sameSite: "",
  signed: ""
};

app.use(
  session({
    key: "SESSION_ID",
    store: store,
    cookie: cookie
  })
);

app.use(async ctx => {
  // 设置session
  if (ctx.url === "/set") {
    ctx.session = {
      user_id: Math.random()
        .toString(36)
        .substr(2),
      count: 0
    };
    ctx.body = ctx.session;
  } else if (ctx.url === "/") {
    // 读取session信息
    ctx.session.count = ctx.session.count + 1;
    ctx.body = ctx.session;
  }
});

app.listen(3000);
console.log("[demo] session is starting at port 3000");
```

### 六、模板引擎

**安装 koa 模板使用中间件**
npm install --save koa-views
**安装 ejs 模板引擎**
npm install --save ejs

```javascript
const Koa = require("koa");
const views = require("koa-views");
const path = require("path");
const app = new Koa();

// 加载模板引擎
app.use(
  views(path.join(__dirname, "./view"), {
    extension: "ejs"
  })
);

app.use(async ctx => {
  let title = "hello koa2";
  await ctx.render("index", {
    title
  });
});

app.listen(3000);
```

### 七、文件上传

**busboy 模块**
npm install --save busboy

```javascript
const inspect = require("util").inspect;
const path = require("path");
const fs = require("fs");
const Busboy = require("busboy");

// req 为node原生请求
const busboy = new Busboy({ headers: req.headers });

// ...

// 监听文件解析事件
busboy.on("file", function(fieldname, file, filename, encoding, mimetype) {
  console.log(`File [${fieldname}]: filename: ${filename}`);

  // 文件保存到特定路径
  file.pipe(fs.createWriteStream("./upload"));

  // 开始解析文件流
  file.on("data", function(data) {
    console.log(`File [${fieldname}] got ${data.length} bytes`);
  });

  // 解析文件结束
  file.on("end", function() {
    console.log(`File [${fieldname}] Finished`);
  });
});

// 监听请求中的字段
busboy.on("field", function(fieldname, val, fieldnameTruncated, valTruncated) {
  console.log(`Field [${fieldname}]: value: ${inspect(val)}`);
});

// 监听结束事件
busboy.on("finish", function() {
  console.log("Done parsing form!");
  res.writeHead(303, { Connection: "close", Location: "/" });
  res.end();
});
req.pipe(busboy);
```

**上传文件简单实现**

```javascript
const inspect = require("util").inspect;
const path = require("path");
const os = require("os");
const fs = require("fs");
const Busboy = require("busboy");

/**
 * 同步创建文件目录
 * @param  {string} dirname 目录绝对地址
 * @return {boolean}        创建目录结果
 */
function mkdirsSync(dirname) {
  if (fs.existsSync(dirname)) {
    return true;
  } else {
    if (mkdirsSync(path.dirname(dirname))) {
      fs.mkdirSync(dirname);
      return true;
    }
  }
}

/**
 * 获取上传文件的后缀名
 * @param  {string} fileName 获取上传文件的后缀名
 * @return {string}          文件后缀名
 */
function getSuffixName(fileName) {
  let nameList = fileName.split(".");
  return nameList[nameList.length - 1];
}

/**
 * 上传文件
 * @param  {object} ctx     koa上下文
 * @param  {object} options 文件上传参数 fileType文件类型， path文件存放路径
 * @return {promise}
 */
function uploadFile(ctx, options) {
  let req = ctx.req;
  let res = ctx.res;
  let busboy = new Busboy({ headers: req.headers });

  // 获取类型
  let fileType = options.fileType || "common";
  let filePath = path.join(options.path, fileType);
  let mkdirResult = mkdirsSync(filePath);

  return new Promise((resolve, reject) => {
    console.log("文件上传中...");
    let result = {
      success: false,
      formData: {}
    };

    // 解析请求文件事件
    busboy.on("file", function(fieldname, file, filename, encoding, mimetype) {
      let fileName =
        Math.random()
          .toString(16)
          .substr(2) +
        "." +
        getSuffixName(filename);
      let _uploadFilePath = path.join(filePath, fileName);
      let saveTo = path.join(_uploadFilePath);

      // 文件保存到制定路径
      file.pipe(fs.createWriteStream(saveTo));

      // 文件写入事件结束
      file.on("end", function() {
        result.success = true;
        result.message = "文件上传成功";

        console.log("文件上传成功！");
        resolve(result);
      });
    });

    // 解析表单中其他字段信息
    busboy.on("field", function(
      fieldname,
      val,
      fieldnameTruncated,
      valTruncated,
      encoding,
      mimetype
    ) {
      console.log("表单字段数据 [" + fieldname + "]: value: " + inspect(val));
      result.formData[fieldname] = inspect(val);
    });

    // 解析结束事件
    busboy.on("finish", function() {
      console.log("文件上结束");
      resolve(result);
    });

    // 解析错误事件
    busboy.on("error", function(err) {
      console.log("文件上出错");
      reject(result);
    });

    req.pipe(busboy);
  });
}
module.exports = {
  uploadFile
};
```

**入口文件**

```javascript
const Koa = require("koa");
const path = require("path");
const app = new Koa();
// const bodyParser = require('koa-bodyparser')

const { uploadFile } = require("./util/upload");

// app.use(bodyParser())

app.use(async ctx => {
  if (ctx.url === "/" && ctx.method === "GET") {
    // 当GET请求时候返回表单页面
    let html = `
      <h1>koa2 upload demo</h1>
      <form method="POST" action="/upload.json" enctype="multipart/form-data">
        <p>file upload</p>
        <span>picName:</span><input name="picName" type="text" /><br/>
        <input name="file" type="file" /><br/><br/>
        <button type="submit">submit</button>
      </form>
    `;
    ctx.body = html;
  } else if (ctx.url === "/upload.json" && ctx.method === "POST") {
    // 上传文件请求处理
    let result = { success: false };
    let serverFilePath = path.join(__dirname, "upload-files");

    // 上传文件事件
    result = await uploadFile(ctx, {
      fileType: "album", // common or album
      path: serverFilePath
    });

    ctx.body = result;
  } else {
    // 其他请求显示404
    ctx.body = "<h1>404！！！ o(╯□╰)o</h1>";
  }
});

app.listen(3000, () => {
  console.log("[demo] upload-simple is starting at port 3000");
});
```

**异步上传图片实现**
参考网址：https://chenshenhai.github.io/koa2-note/note/upload/pic-async.html

### 八、数据库 mysql

npm install --save mysql
**创建数据库会话**

```javascript
const mysql = require("mysql");
const connection = mysql.createConnection({
  host: "127.0.0.1", // 数据库地址
  user: "root", // 数据库用户
  password: "123456", // 数据库密码
  database: "my_database" // 选中数据库
});

// 执行sql脚本对数据库进行读写
connection.query("SELECT * FROM my_table", (error, results, fields) => {
  if (error) throw error;
  connection.release();
});
```

**创建数据连接池**
一般情况下操作数据库是很复杂的读写过程，不只是一个会话，如果直接用会话操作，就需要每次会话都要配置连接参数。所以这时候就需要连接池管理会话。

```javascript
const mysql = require('mysql')

// 创建数据池
const pool  = mysql.createPool({
  host     : '127.0.0.1',   // 数据库地址
  user     : 'root',    // 数据库用户
  password : '123456'   // 数据库密码
  database : 'my_database'  // 选中数据库
})

// 在数据池中进行会话操作
pool.getConnection(function(err, connection) {
  connection.query('SELECT * FROM my_table',  (error, results, fields) => {
    // 结束会话
    connection.release();
    // 如果有错误就抛出
    if (error) throw error;
  })
})
```

**async/await 封装使用 mysql**
由于 mysql 模块的操作都是异步操作，每次操作的结果都是在回调函数中执行，现在有了 async/await，就可以用同步的写法去操作数据库
**_Promise 封装 mysql 模块_**

```javascript
const mysql = require("mysql");
const pool = mysql.createPool({
  host: "127.0.0.1",
  user: "root",
  password: "123456",
  database: "my_database"
});

let query = function(sql, values) {
  return new Promise((resolve, reject) => {
    pool.getConnection(function(err, connection) {
      if (err) {
        reject(err);
      } else {
        connection.query(sql, values, (err, rows) => {
          if (err) {
            reject(err);
          } else {
            resolve(rows);
          }
          connection.release();
        });
      }
    });
  });
};
module.exports = { query };
```

**_async/await 使用_**

```javascript
const { query } = require("./async-db");
async function selectAllData() {
  let sql = "SELECT * FROM my_table";
  let dataList = await query(sql);
  return dataList;
}

async function getData() {
  let dataList = await selectAllData();
  console.log(dataList);
}

getData();
```

### 九、jsonp

在项目复杂的业务场景，有时候需要在前端跨域获取数据，这时候提供数据的服务就需要提供跨域请求的接口，通常是使用 JSONP 的方式提供跨域接口。

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  // 如果jsonp 的请求为GET
  if (ctx.method === "GET" && ctx.url.split("?")[0] === "/getData.jsonp") {
    // 获取jsonp的callback
    let callbackName = ctx.query.callback || "callback";
    let returnData = {
      success: true,
      data: {
        text: "this is a jsonp api",
        time: new Date().getTime()
      }
    };
    // jsonp的script字符串
    let jsonpStr = `;${callbackName}(${JSON.stringify(returnData)})`;
    // 用text/javascript，让请求支持跨域获取
    ctx.type = "text/javascript";
    // 输出jsonp字符串
    ctx.body = jsonpStr;
  } else {
    ctx.body = "hello jsonp";
  }
});
app.listen(3000, () => {
  console.log("[demo] jsonp is starting at port 3000");
});
```

原理：
JSONP 跨域输出的数据是可执行的 JavaScript 代码
ctx 输出的类型应该是'text/javascript'
ctx 输出的内容为可执行的返回数据 JavaScript 代码字符串
需要有回调函数名 callbackName，前端获取后会通过动态执行 JavaScript 代码字符，获取里面的数据

**koa-jsonp 中间件**
npm install --save koa-jsonp

```javascript
const Koa = require("koa");
const jsonp = require("koa-jsonp");
const app = new Koa();
// 使用中间件
app.use(jsonp());
app.use(async ctx => {
  let returnData = {
    success: true,
    data: {
      text: "this is a jsonp api",
      time: new Date().getTime()
    }
  };
  // 直接输出JSON
  ctx.body = returnData;
});
app.listen(3000, () => {
  console.log("[demo] jsonp is starting at port 3000");
});
```

### 十、单元测试

测试是一个项目周期里必不可少的环节，开发者在开发过程中也是无时无刻进行“人工测试”，如果每次修改一点代码，都要牵一发动全身都要手动测试关联接口，这样子是禁锢了生产力。为了解放大部分测试生产力，相关的测试框架应运而生，比较出名的有 mocha，karma，jasmine 等。虽然框架繁多，但是使用起来都是大同小异。

npm install --save-dev mocha chai supertest
**开始写测试用例**

```javascript
const supertest = require("supertest");
const chai = require("chai");
const app = require("./../index");

const expect = chai.expect;
const request = supertest(app.listen());
// 测试套件/组
describe("开始测试demo的GET请求", () => {
  // 测试用例
  it("测试/getString.json请求", done => {
    request
      .get("/getString.json")
      .expect(200)
      .end((err, res) => {
        // 断言判断结果是否为object类型
        expect(res.body).to.be.an("object");
        expect(res.body.success).to.be.an("boolean");
        expect(res.body.data).to.be.an("string");
        done();
      });
  });
});
```

## nuxt

**vue 的服务器渲染**

```javascript
const Vue = require("vue");
const renderer = require("vue-server-renderer").createRenderer();
const app = require("express")();

app.get("*", (req, res) => {
  renderer.renderToString(vm, (err, html) => {
    if (err) {
      console.error(err);
      return;
    }
    console.log(html);
    res.end(html);
  });
});

app.listen(8000);
console.log("server listening at http://localhost:8000");

const vm = new Vue({
  template: "<h1>hello,{{name}}</h1>",
  data: () => {
    return {
      name: "zhang"
    };
  }
});
```

### nuxt 官方文档 https://zh.nuxtjs.org/guide/

**nuxt 安装**
create-nuxt-app <project name>
**nuxt 目录**
**nuxt 路由**
page 下的文件可以直接作为路由
1、基础路由
2、动态路由
3、路由参数校验
4、嵌套路由
5、动态嵌套路由

动态特效
1、全局过渡动效设置
2、页面过渡动效设置

中间件
**nuxt 视图**
**nuxt 异步数据**
asyncData 方法会在组件（限于页面组件）每次加载之前被调用。它可以在服务端或路由更新之前被调用。
**nuxt 资源文件**
**nuxt 插件**
**nuxt 模块**
Nuxt.js 团队提供 官方 模块:
@nuxt/http: 基于 ky-universal 的轻量级和通用的 HTTP 请求
@nuxtjs/axios: 安全和使用简单 Axios 与 Nuxt.js 集成用来请求 HTTP
@nuxtjs/pwa: 使用经过严格测试，更新且稳定的 PWA 解决方案来增强 Nuxt
@nuxtjs/auth: Nuxt.js 的身份验证模块，提供不同的方案和验证策略
Nuxt.js 社区制作的模块列表可在 https://github.com/topics/nuxt-module 中查询
**nuxt 状态树**
**支持 typescript**
**命令和部署**
