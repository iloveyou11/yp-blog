---
title: jest详解
date: 2019-09-16
categories: JavaScript
author: yangpei
tags:
  - JavaScript
comments: true
cover_picture: /images/banner.jpg
---
#### 简介

**常见前端自动化测试框架：**

1. Jasmine
2. Mocha+Chai
3. Jest

引入jest测试，则一定要针对模块进行测试

**jest作用：**
1. 单元测试（模块测试）
2. 集成测试（多个模块测试）

`npx jest --init`
会生成jest.config.js配置文件

`npx jest --coverage`
会自动生成测试覆盖率说明

**package.json**
1. jest --watch 修改了哪个文件，重新运行那个文件的测试用例
2. jest --watchAll 修改了任意文件，重新运行所有文件的测试用例

#### jest语法基础

**常用匹配器：**

**1. boolean**
```
    toBe 相当于 === ，要完全相等才匹配（ 包括引用地址）
    toEqual 只匹配内容
    toBeNull 匹配null
    toBeUndefined 匹配undefined
    toBeDefined 匹配已定义
    toBeTruthy 转化为boolean是否为true
    toBeFalsy 转化为boolean是否为false
    not 非
```
**2. number**
```
    toBeGreaterThan 大于
    toBeLessThan 小于
    toBeGreaterThanOrEqual 大于等于
    toBeLessThanOrEqual 小于等于
    toBeCloseTo 计算浮点数可使用，因为浮点数会出现小数溢出
```
**3. string**
```
    toMatch 正则表达式或字符串
```
**4. array、set**
```
    toContain 数组包含某个值
```
**5. 异常**
```
    toThrow 函数抛出异常
```

**模式切换**

合理地使用这些模式，能够让测试变得更加地灵活、好用
```
Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.  
 // 需要使用git来管理代码
 // 以检测变化的文件
 // jest --watch表示直接进入o模式
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.
```
**异步**

1. 回调函数形式

处理异步函数测试，需要使用done，在异步回调中加入done函数
```JavaScript
export const fetchData = (fn) => {
    axios.get('http://www.dell-lee.com/react/api/demo.json').then(res => {
        fn(res.data)
    })
}

test('fetchData', (done) => {
    fetchData((data) => {
        expect(data).toEqual({
            success: true
        })
        done()
    })
})
```
2. promise形式
```JavaScript
export const fetchData = () => {
    return axios.get('http://www.dell-lee.com/react/api/demo1.json')
}

// 写法1
// 测试内容
test('fetchData', () => {
    return fetchData().then((res) => {
        expect(res.data).toEqual({
            success: true
        })
    })
})
// 测试404，记得一定要加expect.assertions(1)保证下面一定执行1个expect
test('fetchData', () => {
    expect.assertions(1)
    return fetchData().catch((e) => {
        expect(e.toString().indexOf('404') > -1).toBe(true)
    })
})

// 写法2
// 测试内容
test('fetchData', () => {
    return expect(fetchData()).resolves.toMatchObject({
        data: {
            success: true
        }
    })
})
// 测试404
test('fetchData', () => {
    return expect(fetchData()).rejects.toThrow()
})

// 写法3
// 测试内容
test('fetchData', async() => {
    await expect(fetchData()).resolves.toMatchObject({
        data: {
            success: true
        }
    })
})
// 测试404
test('fetchData', async() => {
    await expect(fetchData()).rejects.toThrow()
})

// 写法4
// 测试内容
test('fetchData', async() => {
    const res = await fetchData()
    expect(res.data).toEqual({
        success: true
    })
})
// 测试404
test('fetchData', async() => {
    expect.assertions(1)
    try {
        await fetchData()
    } catch (e) {
        expect(e.toString().indexOf('404') > -1).toBe(true)
    }
})
```
**钩子函数**
```JavaScript
beforeAll：在所有测试用例之前执行
beforeEach：在每个测试用例之前执行
afterAll：在所有测试用例之后执行
afterEach：在每个测试用例之后执行

beforeEach(() => {
  initializeCityDatabase();
});

afterEach(() => {
  clearCityDatabase();
});

beforeAll(() => {
  return initializeCityDatabase();
});

afterAll(() => {
  return clearCityDatabase();
});
```
**分组**

这样可以使结构看起来更为清晰
```JavaScript
describe('outer', () => {
  describe('describe inner 1', () => {
    console.log('describe inner 1');
    test('test 1', () => {
      console.log('test for describe inner 1');
      expect(true).toEqual(true);
    });
    test('test 2', () => {
      console.log('test for describe inner 2);
      expect(true).toEqual(true);
    });
  });

  describe('describe inner 2', () => {
    test('test1 for describe inner 2', () => {
      console.log('test for describe inner 2');
      expect(false).toEqual(false);
    });
    test('test2 for describe inner 2', () => {
      console.log('test for describe inner 2');
      expect(false).toEqual(false);
    });
  });
});
```
test.only是指只执行那一个测试用例

**mock**

使用jest对axios做一个模拟，这样就不会去请求真正的数据，因为每次调用接口很耗费时间，假若要做大量的ajax测试，则会耗费很长的时间才能完成测试，而前端不需要在意接口返回的内容（此部分由后端完成接口测试），只在意接口是否成功请求，因此采用mock函数可以解决此问题。
```JavaScript
// demo.js

import axios from "axios";
export const runCb = (cb) => {
    cb()
}
export const getData = () => {
    return axios.get('/api').then(res => res.data)
}


// demo.test.js

import {
    runCb,
    getData
} from './demo'
import axios from 'axios';
jest.mock('axios') //使用jest对axios做一个模拟，这样就不会去请求真正的数据了

//   mock函数，
//   1、捕获函数的调用；
//   2、自由地设置返回结果；
//   3、改变内部函数的实现

test('测试runCb函数', () => {

    const func = jest.fn()

    //mock函数有返回值
    // const func = jest.fn(()=>{
    //     return 'aa'
    // }) 

    // 模拟返回结果各一次
    func.mockReturnValueOnce('aaa').mockReturnValueOnce('bbb')
        // 模拟每次返回结果
    func.mockReturnValue('aaa')
        // 模拟函数内部具体实现
    func.mockImplementation(() => {
        // ……
        // 函数具体实现
    })
    func.mockImplementationOnce(() => {
            // ……
            // 函数具体实现
        })
    // 模拟返回this
    func.mockReturnThis()
    runCb(func)
    runCb(func)
    expect(func).toBeCalled() //验证func被调用过

    // console.log(func.mock); 
    // {
    //     calls: [[],[]],
    //     instances: [undefined, undefined],
    //     invocationCallOrder: [1, 2],
    //     results: [{
    //             type: 'return',
    //             value: 'aaa'
    //         },{
    //             type: 'return',
    //             value: 'bbb'
    //         }
    //     ]
    // }

    expect(func.mock.calls.length).toBe(2) // 验证调用了两次
    expect(func.mock.calls[0]).toEqual(['abc']) // 验证接收的参数
    expect(func).toBeCalledWith('aaa') // 验证函数每次接收的参数都是aaa
    expect(func.mock.results[0].value).toBe('aaa') // 验证函数返回结果

})

test('测试ajax请求', async() => {
    axios.get.mockResolvedValue({
        data: 'hello'
    })
    await getData().then((data) => {
        expect(data).toBe('hello')
    })
})
```
推荐使用vscode提供的`jest插件`，安装后不需要执行`npm test`命令，保存文件即可执行测试，绿色代表测试用例通过


**深入mock**

如果不采用`jest.mock('axios')`来模拟ajax请求，而是直接模拟函数（用其他函数来代替），jest也提供了此类方法
```JavaScript
// jest会自动找__mocks__文件夹，将此文件夹下地demo.js代替原目录下的demo.js，这样可以模拟异步请求
jest.mock('./demo')

// 不采用以上语句也是可以的，可以直接修改jest.config.js配置文件，将automock设置为true
automock: true
```
一般在项目中，需要模拟异步请求，而不模拟同步函数，因此可以使用jest.requireActual来加载真正的同步函数
fetchData是模拟demo.js中的函数，而getNumber是真正demo.js中的函数

**snapshot**

```JavaScript
import {
    config1,
    config2
} from './aa'

test('测试静态配置', () => {
    expect(config1()).toMatchSnapshot()
})
test('测试动态配置', () => {
    expect(config2()).toMatchSnapshot({
        time: expect.any(Date)
    })
})

test('测试行内snapshot', () => {
    expect(config2()).toMatchInlineSnapshot({
        time: expect.any(Date)
    })
})
```
如果要使用行内snapshot（就是在test文件间生成快照，而不是单独地生成快照文件），则需要安装第三方模块prettier

**mock timers**

```JavaScript
// timer.js
export default (cb) => {
    setTimeout(() => {
        cb()
        setTimeout(() => {
            cb()
        }, 2000)
    }, 1000)
}
```
使用done可以测试setTimeout定时器，但是当定时时间非常大时，则需要等待很长的时间执行，因此可以使用jest提供的useFakeTimers函数，模拟定时器立即执行
```JavaScript
// time.test.js

import timer from './timer'

test('测试定时器', (done) => {
    timer(() => {
        expect(2).toBe(2)
        done()
    })
})
```

使用jest提供的useFakeTimers函数，模拟定时器立即执行

```JavaScript
import timer from './timer'
jest.useFakeTimers()

// jest.useFakeTimers()和jest.runAllTimers()配对使用
// 能够有效避免运行异步函数的等待时间
test('测试定时器', () => {
    const fn = jest.fn()
    timer(fn)

    // 运行所有的定时器
    jest.runAllTimers()
    expect(fn).toHaveBeenCalledTimes(2)
    // 只运行处于队列中的定时器
    // jest.runOnlyPendingTimers()
    // expect(fn).toHaveBeenCalledTimes(1)
    // 任意地快进时间
    // jest.advanceTimersByTime(3000)
    // expect(fn).toHaveBeenCalledTimes(2)
})
```
解释一下，`jest.useFakeTimers()`和`jest.runAllTimers()`必须配对使用。`runAllTimers()`是运行所有的定时器，如例子中的两个`setTimeout`都会被执行，而`runOnlyPendingTimers()`只运行处于队列中的定时器，即只执行最外层的`setTimeout`，还有`advanceTimersByTime()`函数是指定任意的快进时间

**测试ES6中的类**

所谓单元测试，是针对此单元模块做测试，而不关心引入的外部模块是否正常，如果引入的外部模块中含有大量的复杂逻辑，则会拖慢测试的性能，因此可以采用模拟类的方式解决
```JavaScript
// util.js
class Util {
    init() {
        // 异常复杂
    }
    a() {
        // 异常复杂
    }
    b() {
        // 异常复杂
    }
}

export default Util

```


```JavaScript
// class.js  待测试的方法
import Util from "./util";
export const class1 = (a, b) => {
    const util = new Util()
    util.a(a)
    util.b(b)
}
```

```JavaScript
// class.test.js 测试文件

// jest.mock发现util是一个类, 会自动把类中的构造函数和方法替换为jest.fn()
jest.mock('./util.js') 

// 转化如下:
// const Util = jest.fn()
// Util.prototype.inti=jest.fn()
// Util.prototype.a=jest.fn()
// Util.prototype.b=jest.fn()

import {class1} from './class'
import Util from './util'

// 如果a b方法内部逻辑异常复杂,而我们只关心class1是否成功创建了实例,并执行了a b方法,不关注其内部实现,如果简化测试呢?
test('测试class1是否成功创建了实例,并执行了a b方法', () => {
    class1()
    expect(Util).toHaveBeenCalled()
    console.log(Util.mock);
    expect(Util.mock.instances[0].a).toHaveBeenCalled()
    expect(Util.mock.instances[0].b).toHaveBeenCalled()
})
```

集成测试（对此单元以及单元内所包含的其他单元模块统一地去做测试）

在单元测试中我们之所以使用mock，是因为mock能够让我们引入的外部模块变得简单，让单元测试运行起来会更加地快速


如果需要对Util类的mock做进一步的自定义，则可以使用之前提到的__mocks__文件夹进行手动模拟

**DOM节点操作**

node环境下是不具备dom的，但是jest在node环境下自己模拟了一套dom api，需要安装jquery模块
```JavaScript
import $ from 'jquery'

const addDiv = () => {
    $('body').append('<div>aaa</div>')
}

export default addDiv
```

```JavaScript
import addDiv from './dom'
import $ from 'jquery'

// node环境下是不具备dom的
// 但是jest在node环境下自己模拟了一套dom api
test('测试dom', () => {
    addDiv()
    expect($('body').find('div').length).toBe(1)
})
```

