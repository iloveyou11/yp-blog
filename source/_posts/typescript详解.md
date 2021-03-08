---
title: typescript详解
date: 2019-07-29
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---
<!-- more -->

##### 基本配置（自动编译）

**vscode 配置自动编译 ts 代码**
1. 运行 tsc --init，生成配置文件，改 outDir
2. 运行任务，选择监视 tsconfig

<!-- more -->

##### 一、typescript 的数据类型(存在类型校验)

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

**对象**

```typescript
declare function create(o: object | null): void;
create({ prop: 0 });
```

**【特殊类型】**

**任意类型any**

```typescript
let random: any = 123;
//null和undefined 其他类型的子类型
let num1: number | undefined; //定义未赋值就是undefined
let num2: number | null | undefined;
```

**null和undefined**

```typescript
let num: number;
let str: string;

// 这些类型能被赋予
num = null;
str = undefined;
```


**空类型void(方法没有返回值)**

```typescript
function run1(): void {
  console.log("void");
}
function run2(): number {
  return 1;
}
```

**never 类型(表示从来不会出现的值，基本用不到)**

一个从来不会有返回值的函数（如：如果函数内含有 while(true) {}）；
一个总是会抛出错误的函数（如：function foo() { throw new Error('Not Implemented') }，foo 的返回类型是 never）；

```typescript
function error(message: string): never {
  throw new Error(message);
}


//使用场景（详细检查）
function foo(x: string | number): boolean {
  if (typeof x === 'string') {
    return true;
  } else if (typeof x === 'number') {
    return false;
  }

  // 如果不是一个 never 类型，这会报错：
  // - 不是所有条件都有返回值 （严格模式下）
  // - 或者检查到无法访问的代码
  // 但是由于 TypeScript 理解 `fail` 函数返回为 `never` 类型
  // 它可以让你调用它，因为你可能会在运行时用它来做安全或者详细的检查。
  return fail('Unexhaustive');
}

function fail(message: string): never {
  throw new Error(message);
}
```
never 表示一个从来不会优雅的返回的函数时，你可能马上就会想到与此类似的 void，然而实际上，void 表示没有任何类型，never 表示永远不存在的值的类型。

**[其他类型]**

**泛型**（在计算机科学中，许多算法和数据结构并不会依赖于对象的实际类型。然而，你仍然会想在每个变量里强制提供约束）

```typescript
function reverse<T>(items: T[]): T[] {
  const toreturn = [];
  for (let i = items.length - 1; i >= 0; i--) {
    toreturn.push(items[i]);
  }
  return toreturn;
}
```

**联合类型**（在 JavaScript 中，你希望属性为多种类型之一，如字符串或者数组。这就是联合类型所能派上用场的地方）

```typescript
function formatCommandline(command: string[] | string) {
  let line = '';
  if (typeof command === 'string') {
    line = command.trim();
  } else {
    line = command.join(' ').trim();
  }
  // Do stuff with line: string
}
```

**交叉类型**（在 JavaScript 中， extend 是一种非常常见的模式，在这种模式中，你可以从两个对象中创建一个新对象，新对象会拥有着两个对象所有的功能。）

```typescript（在 JavaScript 中， extend 是一种非常常见的模式，在这种模式中，你可以从两个对象中创建一个新对象，新对象会拥有着两个对象所有的功能。）
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

const x = extend({ a: 'hello' }, { b: 42 });

// 现在 x 拥有了 a 属性与 b 属性
const a = x.a;
const b = x.b;
```


【tips】
>- 如果你需要使用类型注解的层次结构，请使用接口。
>- 它能使用 implements 和 extends
为一个简单的对象类型（像例子中的Coordinates）使用类型别名，仅仅有一个语义化的作用。与此相似，当你想给一个联合类型和交叉类型使用一个语意化的名称时，一个类型别名将会是一个好的选择。

#### 二、typescript 函数

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


```typescript
// 重载
function padding(all: number);
function padding(topAndBottom: number, leftAndRight: number);
function padding(top: number, right: number, bottom: number, left: number);
// Actual implementation that is a true representation of all the cases the function body needs to handle
function padding(a: number, b?: number, c?: number, d?: number) {
  if (b === undefined && c === undefined && d === undefined) {
    b = c = d = a;
  } else if (c === undefined && d === undefined) {
    c = a;
    d = b;
  }
  return {
    top: a,
    right: b,
    bottom: c,
    left: d
  };
}
```

```typescript
interface Overloaded {
  (foo: string): string;
  (foo: number): number;
}

// 实现接口的一个例子：
function stringOrNumber(foo: number): number;
function stringOrNumber(foo: string): string;
function stringOrNumber(foo: any): any {
  if (typeof foo === 'number') {
    return foo * foo;
  } else if (typeof foo === 'string') {
    return `hello ${foo}`;
  }
}

const overloaded: Overloaded = stringOrNumber;

// 使用
const str = overloaded(''); // str 被推断为 'string'
const num = overloaded(123); // num 被推断为 'number'
```


TypeScript 中的函数重载没有任何运行时开销。它只允许你记录希望调用函数的方式，并且编译器会检查其余代码。


**7、箭头函数(this 指向上下文)**

```typescript
setTimeout(() => {
  alert("run");
}, 1000);
```

##### 三、typescript 中类

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

##### 四、typescript 接口（定义标准）

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

##### 五 typescript 泛型

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

##### 六、其他补充

**1、枚举类型**
```typescript
enum CardSuit {
  Clubs,
  Diamonds,
  Hearts,
  Spades
}
// 简单的使用枚举类型
let Card = CardSuit.Clubs;
// 类型安全
Card = 'not a member of card suit'; // Error: string 不能赋值给 `CardSuit` 类型
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

**使用用例**

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

**设置为绝对的而不可变**

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

**自动推断**

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

**使用泛型**

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

**tip:**
你可以随意调用泛型参数，当你使用简单的泛型时，泛型常用 T、U、V 表示。如果在你的参数里，不止拥有一个泛型，你应该使用一个更语义化名称，如 TKey 和 TValue （通常情况下，以 T 做为泛型前缀也在如 C++ 的其他语言里做为模版。）

###### 七、高级特性
**1、类型断言**
TypeScript 允许你覆盖它的推断，并且能以你任何你想要的方式分析它，这种机制被称为「类型断言」。TypeScript 类型断言用来告诉编译器你比它更了解这个类型，并且它不应该再发出错误。

```typescript
interface Foo {
  bar: number;
  bas: string;
}

const foo = {} as Foo;
foo.bar = 123;
foo.bas = 'hello';
```

**双重断言**
类型断言，尽管我们已经证明了它并不是那么安全，但它也还是有用武之地。如下一个非常实用的例子所示，当使用者了解传入参数更具体的类型时，类型断言能按预期工作：

```
function handler(event: Event) {
  const element = event as HTMLElement; // Error: 'Event' 和 'HTMLElement' 中的任何一个都不能赋值给另外一个
}
function handler(event: Event) {
  const element = (event as any) as HTMLElement; // ok
}
```
TypeScript 是怎么确定单个断言是否足够:

当 S 类型是 T 类型的子集，或者 T 类型是 S 类型的子集时，S 能被成功断言成 T。这是为了在进行类型断言时提供额外的安全性，完全毫无根据的断言是危险的，如果你想这么做，你可以使用 any。


**2、Freshness**

```
function logIfHasName(something: { name?: string }) {
  if (something.name) {
    console.log(something.name);
  }
}

const person = { name: 'matt', job: 'being awesome' };
const animal = { name: 'cow', diet: 'vegan, but has milk of own species' };

logIfHasName(person); // okay
logIfHasName(animal); // okay

logIfHasName({ neme: 'I just misspelled name to neme' }); // Error: 对象字面量只能指定已知属性，`neme` 属性不存在。

//允许额外的属性——一个类型能够包含索引签名，以明确表明可以使用额外的属性
let x: { foo: number, [x: string]: any };
x = { foo: 1, baz: 2 }; // ok, 'baz' 属性匹配于索引签名
```


```
//案例：Facebook ReactJS 为对象的 Freshness 提供了一个很好的用例，通常在组件中，你只使用少量属性，而不是传入所有，来调用 setState

// 假设
interface State {
  foo: string;
  bar: string;
}
// 你可能想做：
this.setState({ foo: 'Hello' }); // Error: 没有属性 'bar'
// 因为 state 包含 'foo' 与 'bar'，TypeScript 会强制你这么做：
this.setState({ foo: 'Hello', bar: this.state.bar });

```

**3、类型保护**

**typeof**

```
function doSome(x: number | string) {
  if (typeof x === 'string') {
    // 在这个块中，TypeScript 知道 `x` 的类型必须是 `string`
    console.log(x.subtr(1)); // Error: 'subtr' 方法并没有存在于 `string` 上
    console.log(x.substr(1)); // ok
  }

  x.substr(1); // Error: 无法保证 `x` 是 `string` 类型
}
```
**instanceof**

```
class Foo {
  foo = 123;
  common = '123';
}

class Bar {
  bar = 123;
  common = '123';
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

doStuff(new Foo());
doStuff(new Bar());
```
**in**（in 操作符可以安全的检查一个对象上是否存在一个属性）

```
interface A {
  x: number;
}

interface B {
  y: string;
}

function doStuff(q: A | B) {
  if ('x' in q) {
    // q: A
  } else {
    // q: B
  }
}
```
**字面量类型保护**

```
type Foo = {
  kind: 'foo'; // 字面量类型
  foo: number;
};

type Bar = {
  kind: 'bar'; // 字面量类型
  bar: number;
};

function doStuff(arg: Foo | Bar) {
  if (arg.kind === 'foo') {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 一定是 Bar
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
```
**使用定义的类型保护**

JavaScript 并没有内置非常丰富的、运行时的自我检查机制。当你在使用普通的 JavaScript 对象时（使用结构类型，更有益处），你甚至无法访问 instacneof 和 typeof。在这种情景下，你可以创建用户自定义的类型保护函数，这仅仅是一个返回值为类似于someArgumentName is SomeType 的函数

```
// 仅仅是一个 interface
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

// 用户自己定义的类型保护！
function isFoo(arg: Foo | Bar): arg is Foo {
  return (arg as Foo).foo !== undefined;
}

// 用户自己定义的类型保护使用用例：
function doStuff(arg: Foo | Bar) {
  if (isFoo(arg)) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```

**4、字面量类型**

```
type CardinalDirection = 'North' | 'East' | 'South' | 'West';

function move(distance: number, direction: CardinalDirection) {
  // ...
}

move(1, 'North'); // ok
move(1, 'Nurth'); // Error

//其他字面量类型
type OneToFive = 1 | 2 | 3 | 4 | 5;
type Bools = true | false;

//使用类型注解
function iTakeFoo(foo: 'foo') {}
type Test = {
  someProp: 'foo';
};
const test: Test = {
  // 推断 `someProp` 永远是 'foo'
  someProp: 'foo'
};
iTakeFoo(test.someProp); // ok

```
**使用用例**
TypeScript 枚举类型是基于数字的，你可以使用带字符串字面量的联合类型，来模拟一个基于字符串的枚举类型，然后，你就可以使用 keyof、typeof 来生成字符串的联合类型

```
// 用于创建字符串列表映射至 `K: V` 的函数
function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}

// 创建 K: V
const Direction = strEnum(['North', 'South', 'East', 'West']);

// 创建一个类型
type Direction = keyof typeof Direction;

// 简单的使用
let sample: Direction;

sample = Direction.North; // Okay
sample = 'North'; // Okay
sample = 'AnythingElse'; // ERROR!
```

**5、控制只读**

```
//函数参数只读
function foo(config: { readonly bar: number, readonly bas: number }) {
  // ..
}
const config = { bar: 123, bas: 123 };
foo(config);// 现在你能够确保 'config' 不能够被改变了

//interface或type只读
type Foo = {
  readonly bar: number;
  readonly bas: number;
};
const foo: Foo = { bar: 123, bas: 456 };// 不能被改变
foo.bar = 456; // Error: foo.bar 为仅读属性

//class类只读
class Foo {
  readonly bar = 1; // OK
  readonly baz: string;
  constructor() {
    this.baz = 'hello'; // OK
  }
}
```


```
//这有一个 Readonly 的映射类型，它接收一个泛型 T，用来把它的所有属性标记为只读类型：
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

```
//绝对的不可变
interface Foo {
  readonly [x: number]: number;
}

// 使用
const foo: Foo = { 0: 123, 2: 345 };
console.log(foo[0]); // ok（读取）
foo[0] = 456; // Error: 属性只读
```


```
//自动推断为只读——在一些情况下，编译器能把一些特定的属性推断为 readonly，例如在一个 class 中，如果你有一个只含有 getter 但是没有 setter 的属性，他能被推断为只读
class Person {
  firstName: string = 'John';
  lastName: string = 'Doe';

  get fullName() {
    return this.firstName + this.lastName;
  }
}

const person = new Person();

console.log(person.fullName); // John Doe
person.fullName = 'Dear Reader'; // Error, fullName 只读
```

**对比：**

const
- 用于变量；
- 变量不能重新赋值给其他任何事物。

readonly
- 用于属性；
- 用于别名，可以修改属性；


**6、泛型**

设计泛型的关键目的是在成员之间提供有意义的约束，这些成员可以是：
- 类的实例成员
- 类的方法
- 函数参数
- 函数返回值

```
// 创建一个泛型类
class Queue<T> {
  private data :T[] = [];
  push = (item: T) => this.data.push(item);
  pop = (): T | undefined => this.data.shift();
}
// 简单的使用
const queue = new Queue<number>();
queue.push(0);
queue.push('1'); // Error：不能推入一个 `string`，只有 number 类型被允许
```


```
function reverse<T>(items: T[]): T[] {
  const toreturn = [];
  for (let i = items.length - 1; i >= 0; i--) {
    toreturn.push(items[i]);
  }
  return toreturn;
}

const sample = [1, 2, 3];
let reversed = reverse(sample);

reversed[0] = '1'; // Error
reversed = ['1', '2']; // Error

reversed[0] = 1; // ok
reversed = [1, 2]; // ok
```

```
class Utility {
  reverse<T>(items: T[]): T[] {
    const toreturn = [];
    for (let i = items.length; i >= 0; i--) {
      toreturn.push(items[i]);
    }
    return toreturn;
  }
}
```

你可以随意调用泛型参数，当你使用简单的泛型时，泛型常用 T、U、V 表示。如果在你的参数里，不止拥有一个泛型，你应该使用一个更语义化名称，如 TKey 和 TValue （通常情况下，以 T 做为泛型前缀也在如 C++ 的其他语言里做为模版。）


```
//另一个明显的例子是，一个用于加载 json 返回值函数，它返回你任何传入类型的 Promise
const getJSON = <T>(config: { url: string; headers?: { [key: string]: string } }): Promise<T> => {
  const fetchConfig = {
    method: 'GET',
    Accept: 'application/json',
    'Content-Type': 'application/json',
    ...(config.headers || {})
  };
  return fetch(config.url, fetchConfig).then<T>(response => response.json());
};

//请注意，你仍然需要明显的注解任何你需要的类型，但是 getJSON<T> 的签名 config => Promise<T> 能够减少你一些关键的步骤（你不需要注解 loadUsers 的返回类型，因为它能够被推出来）
type LoadUserResponse = {
  user: {
    name: string;
    email: string;
  }[];
};
function loaderUser() {
  return getJSON<LoadUserResponse>({ url: 'https://example.com/users' });
}
```

**7、辨析联合类型**

```
//原始辨析（根据属性值）
interface Square {
  kind: 'square';
  size: number;
}

interface Rectangle {
  kind: 'rectangle';
  width: number;
  height: number;
}

// 有人仅仅是添加了 `Circle` 类型
// 我们可能希望 TypeScript 能在任何被需要的地方抛出错误
interface Circle {
  kind: 'circle';
  radius: number;
}

type Shape = Square | Rectangle | Circle;

function area(s: Shape) {
  switch (s.kind) {
    case 'square':
      return s.size * s.size;
    case 'rectangle':
      return s.width * s.height;
    case 'circle':
      return Math.PI * s.radius ** 2;
    default:
    //如果你使用 strictNullChecks 选项来做详细的检查，你应该返回 _exhaustiveCheck 变量（类型是 never），否则 TypeScript 可能会推断返回值为 undefined
      const _exhaustiveCheck: never = s;
      return _exhaustiveCheck;
  }
}
```


**8、索引签名**

```
const foo: {
  [index: string]: { message: string };
} = {};

// 储存的东西必须符合结构
// ok
foo['a'] = { message: 'some message' };

// Error, 必须包含 `message`
foo['a'] = { messages: 'some message' };

// 读取时，也会有类型检查
// ok
foo['a'].message;

// Error: messages 不存在
foo['a'].messages;
```
索引签名的名称（如：{ [index: string]: { message: string } } 里的 index ）除了可读性外，并没有任何意义。例如：如果有一个用户名，你可以使用 { username: string}: { message: string }，这有利于下一个开发者理解你的代码。

**所有成员都必须符合字符串的索引签名**

```
// ok
interface Foo {
  [key: string]: number;
  x: number;
  y: number;
}

// Error
interface Bar {
  [key: string]: number;
  x: number;
  y: string; // Error: y 属性必须为 number 类型
}
```
**使用一组有限的字符串字面量**

```
type Index = 'a' | 'b' | 'c';
type FromIndex = { [k in Index]?: number };

const good: FromIndex = { b: 1, c: 2 };

// Error:
// `{ b: 1, c: 2, d: 3 }` 不能分配给 'FromIndex'
// 对象字面量只能指定已知类型，'d' 不存在 'FromIndex' 类型上
const bad: FromIndex = { b: 1, c: 2, d: 3 };
```
**同时拥有 string 和 number 类型的索引签名**

```
interface ArrStr {
  [key: string]: string | number; // 必须包括所用成员类型
  [index: number]: string; // 字符串索引类型的子级

  // example
  length: number;
}
```



**9、混合**

```
// 所有 mixins 都需要
type Constructor<T = {}> = new (...args: any[]) => T;

/////////////
// mixins 例子
////////////

// 添加属性的混合例子
function TimesTamped<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    timestamp = Date.now();
  };
}

// 添加属性和方法的混合例子
function Activatable<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    isActivated = false;

    activate() {
      this.isActivated = true;
    }

    deactivate() {
      this.isActivated = false;
    }
  };
}

///////////
// 组合类
///////////

// 简答的类
class User {
  name = '';
}

// 添加 TimesTamped 的 User
const TimestampedUser = TimesTamped(User);

// Tina TimesTamped 和 Activatable 的类
const TimestampedActivatableUser = TimesTamped(Activatable(User));

//////////
// 使用组合类
//////////

const timestampedUserExample = new TimestampedUser();
console.log(timestampedUserExample.timestamp);

const timestampedActivatableUserExample = new TimestampedActivatableUser();
console.log(timestampedActivatableUserExample.timestamp);
console.log(timestampedActivatableUserExample.isActivated);
```

**TIP：**
「混合」是一个函数：
传入一个构造函数；
创建一个带有新功能，并且扩展构造函数的新类；
返回这个新类。
