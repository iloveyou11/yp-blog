---
title: ts设计模式详解
date: 2019-08-10
categories: JavaScript
tags:
  - typescript
comments: true
cover_picture: /images/banner.jpg
---

由于设计模式和软件开发的语言，平台都没有关系，因此，前端工程师对设计模式也是有需求的。
<!-- more -->

>**创建型模式**<br/>
创建型模式一共有4个，分别为工厂（工厂，工厂方法，抽象工厂合并），建造者，原型，单例<br/>
**结构型模式**<br/>
结构型模式一共有7种：适配器，桥接，组合，装饰，外观，享元，代理<br/>
**行为型模式**<br/>
行为型模式一共有5种：命令，中介者，观察者，状态，策略<br/>


#### 一、创建型模式
##### 工厂模式

```typescript
namespace FactoryMethodPattern {

    export interface AbstractProduct {
        method(param?: any) : void;
    }

    export class ConcreteProductA implements AbstractProduct {
        method = (param?: any) => {
            return "Method of ConcreteProductA";
        }
    }

    export class ConcreteProductB implements AbstractProduct {
        method = (param?: any) => {
            return "Method of ConcreteProductB";
        }
    }


    export namespace ProductFactory {
        export function createProduct(type: string) : AbstractProduct {
            if (type === "A") {
                return new ConcreteProductA();
            } else if (type === "B") {
                return new ConcreteProductB();
            }

            return null;
        }
    }
}

```
使用：

```typescript
namespace FactoryMethodPattern {
	export namespace Demo {
		export function show() : void {
		    var a: FactoryMethodPattern.AbstractProduct = FactoryMethodPattern.ProductFactory.createProduct("A");
		    var b: FactoryMethodPattern.AbstractProduct = FactoryMethodPattern.ProductFactory.createProduct("B");

		    console.log(a.method());
		    console.log(b.method());
		};
	}
}


```


##### 抽象工场模式
```typescript
namespace AbstractFactoryPattern {
    export interface AbstractProductA {
        methodA(): string;
    }
    export interface AbstractProductB {
        methodB(): number;
    }

    export interface AbstractFactory {
        createProductA(param?: any) : AbstractProductA;
        createProductB() : AbstractProductB;
    }


    export class ProductA1 implements AbstractProductA {
        methodA = () => {
            return "This is methodA of ProductA1";
        }
    }
    export class ProductB1 implements AbstractProductB {
        methodB = () => {
            return 1;
        }
    }

    export class ProductA2 implements AbstractProductA {
        methodA = () => {
            return "This is methodA of ProductA2";
        }
    }
    export class ProductB2 implements AbstractProductB {
        methodB = () => {
            return 2;
        }
    }


    export class ConcreteFactory1 implements AbstractFactory {
        createProductA(param?: any) : AbstractProductA {
            return new ProductA1();
        }

        createProductB(param?: any) : AbstractProductB {
            return new ProductB1();
        }
    }
    export class ConcreteFactory2 implements AbstractFactory {
        createProductA(param?: any) : AbstractProductA {
            return new ProductA2();
        }

        createProductB(param?: any) : AbstractProductB {
            return new ProductB2();
        }
    }


    export class Tester {
        private abstractProductA: AbstractProductA;
        private abstractProductB: AbstractProductB;

        constructor(factory: AbstractFactory) {
            this.abstractProductA = factory.createProductA();
            this.abstractProductB = factory.createProductB();
        }

        public test(): void {
            console.log(this.abstractProductA.methodA());
            console.log(this.abstractProductB.methodB());
        }
    }

 }
```

使用：

```typescript
namespace AbstractFactoryPattern {
	export namespace Demo {
		export function show() {
    		// Abstract factory1
		    var factory1: AbstractFactoryPattern.AbstractFactory = new AbstractFactoryPattern.ConcreteFactory1();
    		var tester1: AbstractFactoryPattern.Tester = new AbstractFactoryPattern.Tester(factory1);
		    tester1.test();

		    // Abstract factory2
		    var factory2: AbstractFactoryPattern.AbstractFactory = new AbstractFactoryPattern.ConcreteFactory2();
			var tester2: AbstractFactoryPattern.Tester = new AbstractFactoryPattern.Tester(factory2);
		    tester2.test();
		}
	}
}
```

##### 建造者模式

```typescript
namespace BuilderPattern {
    export class UserBuilder {
        private name: string;
        private age: number;
        private phone: string;
        private address: string;

        constructor(name: string) {
            this.name = name;
        }

        get Name() {
            return this.name;
        }
        setAge(value: number): UserBuilder {
            this.age = value;
            return this;
        }
        get Age() {
            return this.age;
        }
        setPhone(value: string): UserBuilder {
            this.phone = value;
            return this;
        }
        get Phone() {
            return this.phone;
        }
        setAddress(value: string): UserBuilder {
            this.address = value;
            return this;
        }
        get Address() {
            return this.address;
        }

        build(): User {
            return new User(this);
        }
    }

    export class User {
        private name: string;
        private age: number;
        private phone: string;
        private address: string;

        constructor(builder: UserBuilder) {
            this.name = builder.Name;
            this.age = builder.Age;
            this.phone = builder.Phone;
            this.address = builder.Address
        }

        get Name() {
            return this.name;
        }
        get Age() {
            return this.age;
        }
        get Phone() {
            return this.phone;
        }
        get Address() {
            return this.address;
        }
    }

}
```
使用：

```typescript
namespace BuilderPattern {
	export namespace Demo { 
		export function show() : void {
			var u: BuilderPattern.User = new BuilderPattern.UserBuilder("Jancsi")
                        .setAge(12)
                        .setPhone("0123456789")
                        .setAddress("asdf")
                        .build();
    		console.log(u.Name + " " + u.Age + " " + u.Phone + " " + u.Address);
		}
	}
}


```


##### 原型模式

```typescript
namespace PrototypePattern {
    export interface Prototype {
        clone(): Prototype;
        toString(): string;
    }

    export class Concrete1 implements Prototype {

        clone() : Prototype {
            return new Concrete1();
        }

        toString(): string {
            return "This is Concrete1";
        }
    }

    export class Concrete2 implements Prototype {

        clone() : Prototype {
            return new Concrete2();
        }

        toString(): string {
            return "This is Concrete2";
        }
    }

    export class Concrete3 implements Prototype {

        clone() : Prototype {
            return new Concrete3();
        }

        toString(): string {
            return "This is Concrete3";
        }
    }


    export class Builder {
        private prototypeMap: { [s: string]: Prototype; } = {};

        constructor() {
            this.prototypeMap['c1'] = new Concrete1();
            this.prototypeMap['c2'] = new Concrete2();
            this.prototypeMap['c3'] = new Concrete3();
        }

        createOne(s: string): Prototype {
            console.log(s);
            return this.prototypeMap[s].clone();
        }
    }
}

```
使用：

```typescript
namespace PrototypePattern {
	export namespace Demo {
		export function show() : void {
			var builder : PrototypePattern.Builder = new PrototypePattern.Builder();
	    	var i = 0;
    		for (i = 1; i <= 3; i += 1) {
	        	console.log(builder.createOne("c" + i).toString());
    		}

		}
	}
}

```

##### 单例模式

```typescript
namespace SingletonPattern {
    export class Singleton {
        private static singleton: Singleton;
        private constructor() {
        }

        public static getInstance(): Singleton {
            if (!Singleton.singleton) {
                Singleton.singleton = new Singleton();
            }
            return Singleton.singleton;
        }
    }
}

```
使用：

```typescript
namespace SingletonPattern {
	export namespace Demo {
		export function show() : void {
			const singleton1 = SingletonPattern.Singleton.getInstance();
			const singleton2 = SingletonPattern.Singleton.getInstance();

			if (singleton1 === singleton2) {
				console.log("two singletons are equivalent");
			} else {
				console.log("two singletons are not equivalent");
			}
		}
	}
}


```


#### 二、结构型模式
##### 适配器模式

```typescript
namespace AdapterPattern {

    export class Adaptee {
        public method(): void {
            console.log("`method` of Adaptee is being called");
        }
    }

    export interface Target {
        call(): void;
    }

    export class Adapter implements Target {
        public call(): void {
            console.log("Adapter's `call` method is being called");
            var adaptee: Adaptee = new Adaptee();
            adaptee.method();
        }
    }
}

```
使用：
```typescript
namespace AdapterPattern {
	export namespace Demo {

		export function show() : void {
			var adapter: AdapterPattern.Adapter = new AdapterPattern.Adapter();
			adapter.call();
		}
	}
}

```
##### 职责链模式

```typescript
namespace ChainOfResponsibilityPattern {

    export class Handler {
        private handler: Handler;
        private req: number;

        constructor(req: number) {
            this.req = req;
        }

        public setHandler(handler: Handler): void {
            this.handler = handler;
        }

        public operation(msg: string, req: number): void {
            if (req <= this.req) {
                this.handlerRequest(msg)
            } else if (this.handler !== null && this.handler !== undefined) {
                this.handler.operation(msg, req);
            }
        }

        public handlerRequest(msg: string): void {
            throw new Error("Abstract method!");
        }
    }

    export class ConcreteHandler1 extends Handler {
        constructor(req: number) {
            super(req);
        }
        public handlerRequest(msg: string) {
            console.log("Message (ConcreteHandler1) :: ", msg);
        }
    }


    export class ConcreteHandler2 extends Handler {
        constructor(req: number) {
            super(req);
        }
        public handlerRequest(msg: string) {
            console.log("Message :: (ConcreteHandler2) ", msg);
        }
    }

    export class ConcreteHandler3 extends Handler {
        constructor(req: number) {
            super(req);
        }
        public handlerRequest(msg: string) {
            console.log("Message :: (ConcreteHandler3) ", msg);
        }
    }
}

```
使用：

```typescript
namespace ChainOfResponsibilityPattern {
	export namespace Demo {

		export function show() : void {
		    var h1: ChainOfResponsibilityPattern.Handler,
				h2: ChainOfResponsibilityPattern.Handler,
				h3: ChainOfResponsibilityPattern.Handler,
				reqs: number[],
				i: number,
				max: number;

			reqs = [2, 7, 23, 34, 4, 5, 8, 3];

			h1 = new ChainOfResponsibilityPattern.ConcreteHandler1(3);
			h2 = new ChainOfResponsibilityPattern.ConcreteHandler2(7);
			h3 = new ChainOfResponsibilityPattern.ConcreteHandler3(20);

			h1.setHandler(h2);
			h2.setHandler(h3);

			for (i = 0, max = reqs.length; i < max; i += 1) {
				h1.operation("operation is fired!", reqs[i]);
			}

		}
	}
}


```

##### 桥接模式

```typescript
namespace BridgePattern {

    export class Abstraction {
        implementor: Implementor;
        constructor(imp: Implementor) {
            this.implementor = imp;
        }

        public callIt(s: String): void {
            throw new Error("This method is abstract!");
        }
    }

    export class RefinedAbstractionA extends Abstraction {
        constructor(imp: Implementor) {
            super(imp);
        }

        public callIt(s: String): void {
            console.log("This is RefinedAbstractionA");
            this.implementor.callee(s);
        }
    }

    export class RefinedAbstractionB extends Abstraction {
        constructor(imp: Implementor) {
            super(imp);
        }

        public callIt(s: String): void {
            console.log("This is RefinedAbstractionB");
            this.implementor.callee(s);
        }
    }

    export interface Implementor {
        callee(s: any): void;
    }

    export class ConcreteImplementorA implements Implementor {
        public callee(s: any) : void {
            console.log("`callee` of ConcreteImplementorA is being called.");
            console.log(s);
        }
    }

    export class ConcreteImplementorB implements Implementor {
        public callee(s: any) : void {
            console.log("`callee` of ConcreteImplementorB is being called.");
            console.log(s);
        }
    }
}

```
使用：

```typescript
namespace BridgePattern {
	export namespace Demo {

		export function show() : void {
		    var abstractionA: BridgePattern.Abstraction = new BridgePattern.RefinedAbstractionA(new BridgePattern.ConcreteImplementorA());
		    var abstractionB: BridgePattern.Abstraction = new BridgePattern.RefinedAbstractionB(new BridgePattern.ConcreteImplementorB());

			abstractionA.callIt("abstractionA");
			abstractionB.callIt("abstractionB");
		}
	}
}


```

##### 组合模式

```typescript
namespace CompositePattern {
    export interface Component {
        operation(): void;
    }

    export class Composite implements Component {

        private list: Component[];
        private s: String;

        constructor(s: String) {
            this.list = [];
            this.s = s;
        }

        public operation(): void {
            console.log("`operation of `", this.s)
            for (var i = 0; i < this.list.length; i += 1) {
                this.list[i].operation();
            }
        }

        public add(c: Component): void {
            this.list.push(c);
        }

        public remove(i: number): void {
            if (this.list.length <= i) {
                throw new Error("index out of bound!");
            }
            this.list.splice(i, 1);
        }
    }

    export class Leaf implements Component {
        private s: String;
        constructor(s: String) {
            this.s = s;
        }
        public operation(): void {
            console.log("`operation` of Leaf", this.s, " is called.");
        }
    }
}


```
使用：

```typescript
namespace CompositePattern {
	export namespace Demo {
		export function show() : void {
		    var leaf1 = new CompositePattern.Leaf("1"),
				leaf2 = new CompositePattern.Leaf("2"),
				leaf3 = new CompositePattern.Leaf("3"),

			composite1 = new CompositePattern.Composite("Comp1"),
			composite2 = new CompositePattern.Composite("Comp2");

			composite1.add(leaf1);
			composite1.add(leaf2);
			composite1.add(leaf3);

			composite1.remove(2);

			composite2.add(leaf1);
			composite2.add(leaf3);

			composite1.operation();
			composite2.operation();
		}
	}
}

```


##### 装饰器模式

```typescript
namespace DecoratorPattern {

    export interface Component {
        operation(): void;
    }

    export class ConcreteComponent implements Component {
        private s: String;

        constructor(s: String) {
            this.s = s;
        }

        public operation(): void {
            console.log("`operation` of ConcreteComponent", this.s, " is being called!");
        }
    }

    export class Decorator implements Component {
        private component: Component;
        private id: Number;

        constructor(id: Number, component: Component) {
            this.id = id;
            this.component = component;
        }

        public get Id(): Number {
            return this.id;
        }

        public operation(): void {
            console.log("`operation` of Decorator", this.id, " is being called!");
            this.component.operation();
        }
    }

    export class ConcreteDecorator extends Decorator {
        constructor(id: Number, component: Component) {
            super(id, component);
        }

        public operation(): void {
            super.operation();
            console.log("`operation` of ConcreteDecorator", this.Id, " is being called!");
        }
    }
}
```
使用：

```typescript
namespace DecoratorPattern {
	export namespace Demo {
		export function show() : void {
			var decorator1: DecoratorPattern.Decorator = new DecoratorPattern.ConcreteDecorator(1, new DecoratorPattern.ConcreteComponent("Comp1"));
			decorator1.operation();
		}
	}
}


```


##### 外观模式

```typescript
namespace FacadePattern {

    export class Part1 {
        public method1(): void {
            console.log("`method1` of Part1");
        }
    }

    export class Part2 {
        public method2(): void {
            console.log("`method2` of Part2");
        }
    }

    export class Part3 {
        public method3(): void {
            console.log("`method3` of Part3");
        }
    }

    export class Facade {
        private part1: Part1 = new Part1();
        private part2: Part2 = new Part2();
        private part3: Part3 = new Part3();

        public operation1(): void {
            console.log("`operation1` is called ===");
            this.part1.method1();
            this.part2.method2();
            console.log("==========================");
        }

        public operation2(): void {
            console.log("`operation2` is called ===");
            this.part1.method1();
            this.part3.method3();
            console.log("==========================");
        }
    }
}

```
使用：

```typescript
namespace FacadePattern {
	export namespace Demo {
		export function show() : void {
		    var facade: FacadePattern.Facade = new FacadePattern.Facade();
			facade.operation1();
			facade.operation2();
		}
	}
}


```


##### 享元模式

```typescript
namespace FlyweightPattern {

    export interface Flyweight {
        operation(s: String): void;
    }

    export class ConcreteFlyweight implements Flyweight {
        private instrinsicState: String;

        constructor(instrinsicState: String) {
            this.instrinsicState = instrinsicState;
        }

        public operation(s: String): void {
            console.log("`operation` of ConcreteFlyweight", s, " is being called!");
        }
    }

    export class UnsharedConcreteFlyweight implements Flyweight {
        private allState: number;

        constructor(allState: number) {
            this.allState = allState;
        }

        public operation(s: String): void {
            console.log("`operation` of UnsharedConcreteFlyweight", s, " is being called!");
        }
    }

    export class FlyweightFactory {

        private fliesMap: { [s: string]: Flyweight; } = <any>{};

        constructor() { }

        public getFlyweight(key: string): Flyweight {

            if (this.fliesMap[key] === undefined || null) {
                this.fliesMap[key] = new ConcreteFlyweight(key);
            }
            return this.fliesMap[key];
        }
    }
}

```
使用：

```typescript
namespace FlyweightPattern {
	export namespace Demo {
		export function show() : void {
		    var factory: FlyweightPattern.FlyweightFactory   = new FlyweightPattern.FlyweightFactory(),

			conc1: FlyweightPattern.ConcreteFlyweight    = <FlyweightPattern.ConcreteFlyweight>factory.getFlyweight("conc1"),
			conc2: FlyweightPattern.ConcreteFlyweight    = <FlyweightPattern.ConcreteFlyweight>factory.getFlyweight("conc2");

			conc1.operation("1");
			conc2.operation("2");
		}
	}
}


```

##### 代理模式

```typescript
namespace ProxyPattern {
    export interface Subject {
        doAction(): void;
    }

    export class Proxy implements Subject {
        private realSubject: RealSubject;
        private s: string;

        constructor(s: string) {
            this.s = s;
        }

        public doAction(): void {
            console.log("`doAction` of Proxy(", this.s, ")");
            if (this.realSubject === null || this.realSubject === undefined) {
                console.log("creating a new RealSubject.");
                this.realSubject = new RealSubject(this.s);
            }
            this.realSubject.doAction();
        }
    }

    export class RealSubject implements Subject {
        private s: string;

        constructor(s: string) {
            this.s = s;
        }
        public doAction(): void {
            console.log("`doAction` of RealSubject", this.s, "is being called!");
        }
    }
}

```
使用：

```typescript
namespace ProxyPattern {
	export namespace Demo {
		export function show() : void {
		    var proxy1: ProxyPattern.Proxy = new ProxyPattern.Proxy("proxy1"),

			proxy2: ProxyPattern.Proxy = new ProxyPattern.Proxy("proxy2");

			proxy1.doAction();
			proxy1.doAction();
			proxy2.doAction();
			proxy2.doAction();
			proxy1.doAction();

		}
	}
}


```

##### 模板方法模式

```typescript
namespace TemplateMethodPattern {
    export class AbstractClass {
        public method1(): void {
            throw new Error("Abstract Method");
        }

        public method2(): void {
            throw new Error("Abstract Method");
        }

        public method3(): void {
            throw new Error("Abstract Method");
        }

        public templateMethod(): void {
            console.log("templateMethod is being called");
            this.method1();
            this.method2();
            this.method3();
        }
    }

    export class ConcreteClass1 extends AbstractClass {
        public method1(): void {
            console.log("method1 of ConcreteClass1");
        }

        public method2(): void {
            console.log("method2 of ConcreteClass1");
        }

        public method3(): void {
            console.log("method3 of ConcreteClass1");
        }
    }

    export class ConcreteClass2 extends AbstractClass {
        public method1(): void {
            console.log("method1 of ConcreteClass2");
        }

        public method2(): void {
            console.log("method2 of ConcreteClass2");
        }

        public method3(): void {
            console.log("method3 of ConcreteClass2");
        }
    }
}

```
使用：

```typescript
namespace TemplateMethodPattern {
	export namespace Demo {

		export function show() : void {
			var c1: TemplateMethodPattern.ConcreteClass1 = new TemplateMethodPattern.ConcreteClass1(),
				c2: TemplateMethodPattern.ConcreteClass2 = new TemplateMethodPattern.ConcreteClass2();

			c1.templateMethod();
			c2.templateMethod();

		}
	}
}

```


#### 三、行为型模式
##### 解释器模式

```typescript
namespace InterpreterPattern {
    export class Context {
    }

    export interface AbstractExpression {
        interpret(context: Context): void;
    }

    export class TerminalExpression implements AbstractExpression {
        public interpret(context: Context): void {
            console.log("`interpret` method of TerminalExpression is being called!");
        }
    }

    export class NonterminalExpression implements AbstractExpression {

        public interpret(context: Context): void {
            console.log("`interpret` method of NonterminalExpression is being called!");
        }
    }
}
```
使用：

```typescript
namespace InterpreterPattern {
	export namespace Demo {

		export function show() : void {
			var context: InterpreterPattern.Context = new InterpreterPattern.Context(),
				list = [],
				i = 0,
				max;

			list.push(new InterpreterPattern.NonterminalExpression());
			list.push(new InterpreterPattern.NonterminalExpression());
			list.push(new InterpreterPattern.NonterminalExpression());
			list.push(new InterpreterPattern.TerminalExpression());
			list.push(new InterpreterPattern.NonterminalExpression());
			list.push(new InterpreterPattern.NonterminalExpression());
			list.push(new InterpreterPattern.TerminalExpression());
			list.push(new InterpreterPattern.TerminalExpression());

			for (i = 0, max = list.length; i < max; i += 1) {
				list[i].interpret(context);
			}


		}
	}
}

```

##### 命令模式

```typescript
namespace CommandPattern {
    export class Command {
        public execute(): void {
            throw new Error("Abstract method!");
        }
    }

    export class ConcreteCommand1 extends Command {
        private receiver: Receiver;

        constructor(receiver: Receiver) {
            super();
            this.receiver = receiver;
        }

        public execute(): void {
            console.log("`execute` method of ConcreteCommand1 is being called!");
            this.receiver.action();
        }
    }

    export class ConcreteCommand2 extends Command {
        private receiver: Receiver;

        constructor(receiver: Receiver) {
            super();
            this.receiver = receiver;
        }

        public execute(): void {
            console.log("`execute` method of ConcreteCommand2 is being called!");
            this.receiver.action();
        }
    }

    export class Invoker {
        private commands: Command[];

        constructor() {
            this.commands = [];
        }

        public storeAndExecute(cmd: Command) {
            this.commands.push(cmd);
            cmd.execute();
        }
    }

    export class Receiver {
        public action(): void {
            console.log("action is being called!");
        }
    }
}

(function main() {
    var receiver: CommandPattern.Receiver = new CommandPattern.Receiver(),
        command1: CommandPattern.Command  = new CommandPattern.ConcreteCommand1(receiver),
        command2: CommandPattern.Command  = new CommandPattern.ConcreteCommand2(receiver),
        invoker : CommandPattern.Invoker  = new CommandPattern.Invoker();

    invoker.storeAndExecute(command1);
    invoker.storeAndExecute(command2);

}());


```
使用：

```typescript
namespace CommandPattern {
	export namespace Demo {

		export function show() : void {
		    var receiver: CommandPattern.Receiver = new CommandPattern.Receiver(),
				command1: CommandPattern.Command  = new CommandPattern.ConcreteCommand1(receiver),
				command2: CommandPattern.Command  = new CommandPattern.ConcreteCommand2(receiver),
				invoker : CommandPattern.Invoker  = new CommandPattern.Invoker();

			invoker.storeAndExecute(command1);
			invoker.storeAndExecute(command2);


		}
	}
}

```


##### 中介者模式
```typescript
namespace MediatorPattern {
    export interface Mediator {
        send(msg: string, colleague: Colleague): void;
    }

    export class Colleague {
        public mediator: Mediator;

        constructor(mediator: Mediator) {
            this.mediator = mediator;
        }

        public send(msg: string): void {
            throw new Error("Abstract Method!");
        }

        public receive(msg: string): void {
            throw new Error("Abstract Method!");
        }
    }

    export class ConcreteColleagueA extends Colleague {
        constructor(mediator: Mediator) {
            super(mediator);
        }

        public send(msg: string): void {
            this.mediator.send(msg, this);
        }

        public receive(msg: string): void {
            console.log(msg, "`receive` of ConcreteColleagueA is being called!");
        }
    }

    export class ConcreteColleagueB extends Colleague {
        constructor(mediator: Mediator) {
            super(mediator);
        }

        public send(msg: string): void {
            this.mediator.send(msg, this);
        }

        public receive(msg: string): void {
            console.log(msg, "`receive` of ConcreteColleagueB is being called!");
        }
    }

    export class ConcreteMediator implements Mediator {
        public concreteColleagueA: ConcreteColleagueA;
        public concreteColleagueB: ConcreteColleagueB;

        public send(msg: string, colleague: Colleague): void {
            if (this.concreteColleagueA === colleague) {
                this.concreteColleagueB.receive(msg);
            } else {
                this.concreteColleagueA.receive(msg);
            }
        }
    }
}
```
使用：

```typescript
namespace MediatorPattern {
	export namespace Demo {

		export function show() : void {
			var cm: MediatorPattern.ConcreteMediator = new MediatorPattern.ConcreteMediator(),
				c1: MediatorPattern.ConcreteColleagueA = new MediatorPattern.ConcreteColleagueA(cm),
				c2: MediatorPattern.ConcreteColleagueB = new MediatorPattern.ConcreteColleagueB(cm);

			cm.concreteColleagueA = c1;
			cm.concreteColleagueB = c2;

			c1.send("`send` of ConcreteColleagueA is being called!");
			c2.send("`send` of ConcreteColleagueB is being called!");

		}
	}
}

```

##### 观察者模式

```typescript
namespace ObserverPattern {
    export class Subject {
        private observers: Observer[] = [];

        public register(observer: Observer): void {
            console.log(observer, "is pushed!");
            this.observers.push(observer);
        }

        public unregister(observer: Observer): void {
            var n: number = this.observers.indexOf(observer);
            console.log(observer, "is removed");
            this.observers.splice(n, 1);
        }

        public notify(): void {
            console.log("notify all the observers", this.observers);
            var i: number
              , max: number;

            for (i = 0, max = this.observers.length; i < max; i += 1) {
                this.observers[i].notify();
            }
        }
    }

    export class ConcreteSubject extends Subject {
        private subjectState: number;

        get SubjectState(): number {
            return this.subjectState;
        }

        set SubjectState(subjectState: number) {
            this.subjectState = subjectState;
        }
    }

    export class Observer {
        public notify(): void {
            throw new Error("Abstract Method!");
        }
    }

    export class ConcreteObserver extends Observer {
        private name: string;
        private state: number;
        private subject: ConcreteSubject;

        constructor (subject: ConcreteSubject, name: string) {
            super();
            console.log("ConcreteObserver", name, "is created!");
            this.subject = subject;
            this.name = name;
        }

        public notify(): void {
            console.log("ConcreteObserver's notify method");
            console.log(this.name, this.state);
            this.state = this.subject.SubjectState;
        }

        get Subject(): ConcreteSubject {
            return this.subject;
        }

        set Subject(subject: ConcreteSubject) {
            this.subject = subject;
        }
    }
}

```
使用：

```typescript
namespace ObserverPattern {
	export namespace Demo {
		export function show() : void {
			var sub: ObserverPattern.ConcreteSubject = new ObserverPattern.ConcreteSubject();

			sub.register(new ObserverPattern.ConcreteObserver(sub, "Jancsi"));
			sub.register(new ObserverPattern.ConcreteObserver(sub, "Julcsa"));
			sub.register(new ObserverPattern.ConcreteObserver(sub, "Marcsa"));

			sub.SubjectState = 123;
			sub.notify();

		}
	}
}

```


##### 状态模式

```typescript
namespace StatePattern {
    export interface State {
        handle(context: Context): void;
    }

    export class ConcreteStateA implements State {
        public handle(context: Context): void {
            console.log("`handle` method of ConcreteStateA is being called!");
            context.State = new ConcreteStateB();
        }
    }

    export class ConcreteStateB implements State {
        public handle(context: Context): void {
            console.log("`handle` method of ConcreteStateB is being called!");
            context.State = new ConcreteStateA();
        }
    }

    export class Context {
        private state: State;

        constructor(state: State) {
            this.state = state;
        }

        get State(): State {
            return this.state;
        }

        set State(state: State) {
            this.state = state;
        }

        public request(): void {
            console.log("request is being called!");
            this.state.handle(this);
        }
    }
}


```
使用：

```typescript
namespace StatePattern {
	export namespace Demo {

		export function show() : void {
			var context: StatePattern.Context = new StatePattern.Context(new StatePattern.ConcreteStateA());
			context.request();
			context.request();
			context.request();
			context.request();
			context.request();
			context.request();
			context.request();
			context.request();

		}
	}
}

```


##### 策略模式

```typescript
namespace StrategyPattern {
    export interface Strategy {
        execute(): void;
    }

    export class ConcreteStrategy1 implements Strategy {
        public execute(): void {
            console.log("`execute` method of ConcreteStrategy1 is being called");
        }
    }

    export class ConcreteStrategy2 implements Strategy {
        public execute(): void {
            console.log("`execute` method of ConcreteStrategy2 is being called");
        }
    }

    export class ConcreteStrategy3 implements Strategy {
        public execute(): void {
            console.log("`execute` method of ConcreteStrategy3 is being called");
        }
    }

    export class Context {
        private strategy: Strategy;

        constructor(strategy: Strategy) {
            this.strategy = strategy;
        }

        public executeStrategy(): void {
            this.strategy.execute();
        }
    }
}


```
使用：

```typescript
namespace StrategyPattern {
	export namespace Demo {
		export function show() : void {
		    var context: StrategyPattern.Context = new StrategyPattern.Context(new StrategyPattern.ConcreteStrategy1());

			context.executeStrategy();

			context = new StrategyPattern.Context(new StrategyPattern.ConcreteStrategy2());
			context.executeStrategy();

			context = new StrategyPattern.Context(new StrategyPattern.ConcreteStrategy3());
			context.executeStrategy();
		}
	}
}

```


##### 迭代器模式

```typescript
namespace IteratorPattern {
    export interface Iterator {

        next(): any;
        hasNext(): boolean;
    }

    export interface Aggregator {
        createIterator(): Iterator;
    }

    export class ConcreteIterator implements Iterator {
        private collection: any[] = [];
        private position: number = 0;

        constructor(collection: any[]) {
            this.collection = collection;
        }

        public next(): any {
            // Error handling is left out
            var result = this.collection[this.position];
            this.position += 1;
            return result;
        }

        public hasNext(): boolean {
            return this.position < this.collection.length;
        }
    }

    export class Numbers implements Aggregator {
        private collection: number[] = [];

        constructor(collection: number[]) {
            this.collection = collection;
        }
        public createIterator(): Iterator {
            return new ConcreteIterator(this.collection);
        }
    }
}


```
使用：

```typescript
namespace IteratorPattern {
	export namespace Demo {

		export function show() : void {
		    var nArray = [1, 7, 21, 657, 3, 2, 765, 13, 65],
				numbers: IteratorPattern.Numbers = new IteratorPattern.Numbers(nArray),
				it: IteratorPattern.ConcreteIterator = <IteratorPattern.ConcreteIterator>numbers.createIterator();

			while (it.hasNext()) {
				console.log(it.next());
			}

		}
	}
}

```
##### 备忘录模式

```typescript
namespace MementoPattern {
    export class State {
        private str: string;

        constructor(str: string) {
            this.str = str;
        }

        get Str() : string {
            return this.str;
        }

        set Str(str: string) {
            this.str = str;
        }
    }

    export class Originator {
        private state: State;

        constructor(state: State) {
            this.state = state;
        }

        get State(): State {
            return this.state;
        }

        set State(state: State) {
            console.log("State :: ", state);
            this.state = state;
        }

        public createMemento(): Memento {
            console.log("creates a memento with a given state!");
            return new Memento(this.state);
        }

        public setMemento(memento: Memento) {
            console.log("sets the state back");
            this.State = memento.State;
        }
    }

    export class Memento {
        private state: State;

        constructor (state: State) {
            this.state = state;
        }

        get State(): State {
            console.log("get memento's state");
            return this.state;
        }
    }

    export class CareTaker {
        private memento: Memento;

        get Memento(): Memento {
            return this.memento;
        }

        set Memento(memento: Memento) {
            this.memento = memento;
        }
    }
}

```
使用：

```typescript
namespace MementoPattern {
	export namespace Demo {

		export function show() : void {
			var state: MementoPattern.State = new MementoPattern.State("... State "),
				originator: MementoPattern.Originator = new MementoPattern.Originator(state),
				careTaker: MementoPattern.CareTaker = new MementoPattern.CareTaker();

			careTaker.Memento = originator.createMemento();
			originator.State = new MementoPattern.State("something else...");

			originator.setMemento(careTaker.Memento);
		}
	}
}

```

##### 访问者模式

```typescript
namespace VisitorPattern {
    export interface Visitor {
        visitConcreteElement1(concreteElement1: ConcreteElement1): void;
        visitConcreteElement2(concreteElement2: ConcreteElement2): void;
    }

    export class ConcreteVisitor1 implements Visitor {
        public visitConcreteElement1(concreteElement1: ConcreteElement1): void {
            console.log("`visitConcreteElement1` of ConcreteVisitor1 is being called!");
        }

        public visitConcreteElement2(concreteElement2: ConcreteElement2): void {
            console.log("`visitConcreteElement2` of ConcreteVisitor1 is being called!");
        }
    }

    export class ConcreteVisitor2 implements Visitor {
        public visitConcreteElement1(concreteElement1: ConcreteElement1): void {
            console.log("`visitConcreteElement1` of ConcreteVisitor2 is being called!");
        }

        public visitConcreteElement2(concreteElement2: ConcreteElement2): void {
            console.log("`visitConcreteElement2` of ConcreteVisitor2 is being called!");
        }
    }


    export interface Element {
        operate(visitor: Visitor): void;
    }

    export class ConcreteElement1 implements Element {
        public operate(visitor: Visitor): void {
            console.log("`operate` of ConcreteElement1 is being called!");
            visitor.visitConcreteElement1(this);
        }
    }

    export class ConcreteElement2 implements Element {
        public operate(visitor: Visitor): void {
            console.log("`operate` of ConcreteElement2 is being called!");
            visitor.visitConcreteElement2(this);
        }
    }

    export class Objs {
        private elements: Element[] = [];

        public attach(e: Element): void {
            this.elements.push(e);
        }

        public detach(e: Element): void {
            var index = this.elements.indexOf(e);
            this.elements.splice(index, 1);
        }

        public operate(visitor: Visitor): void {
            var i = 0,
                max = this.elements.length;

            for(; i < max; i += 1) {
                this.elements[i].operate(visitor);
            }
        }
    }

}


```

使用：

```typescript
namespace VisitorPattern {
	export namespace Demo {

		export function show() : void {
		    var objs: VisitorPattern.Objs = new VisitorPattern.Objs();

			objs.attach(new VisitorPattern.ConcreteElement1());
			objs.attach(new VisitorPattern.ConcreteElement2());

			var v1: VisitorPattern.ConcreteVisitor1 = new VisitorPattern.ConcreteVisitor1(),
				v2: VisitorPattern.ConcreteVisitor2 = new VisitorPattern.ConcreteVisitor2();

			objs.operate(v1);
			objs.operate(v2);

		}
	}
}

```




