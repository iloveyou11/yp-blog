---
title: 前端自动化测试——jest必会
date: 2019-09-16
categories: 前端
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

<!-- more -->



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

##### 常用匹配器

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

##### 模式切换

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
##### 异步处理

**1. 回调函数**

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
2. promise
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
##### 钩子函数
```
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
##### 分组

这样可以使结构看起来更为清晰
```
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

##### mock文件

使用jest对axios做一个模拟，这样就不会去请求真正的数据，因为每次调用接口很耗费时间，假若要做大量的ajax测试，则会耗费很长的时间才能完成测试，而前端不需要在意接口返回的内容（此部分由后端完成接口测试），只在意接口是否成功请求，因此采用mock函数可以解决此问题。
```
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


**实现mock文件和非mock文件**

如果不采用`jest.mock('axios')`来模拟ajax请求，而是直接模拟函数（用其他函数来代替），jest也提供了此类方法
```
// jest会自动找__mocks__文件夹，将此文件夹下地demo.js代替原目录下的demo.js，这样可以模拟异步请求
jest.mock('./demo')

// 不采用以上语句也是可以的，可以直接修改jest.config.js配置文件，将automock设置为true
automock: true
```

```
jest.mock('./demo')
import {fetchData} from './demo' //这里使用的__mocks__下的demo.js文件
const {getNumber} = jest.requireActual('./demo') //这里使用的项目中实际的demo.js文件
```

一般在项目中，需要模拟异步请求，而不模拟同步函数，因此可以使用jest.requireActual来加载真正的同步函数
fetchData是模拟demo.js中的函数，而getNumber是真正demo.js中的函数

##### mock定时器

```
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
```
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

```
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

##### snapshot

```
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

##### 测试ES6中的类

所谓单元测试，是针对此单元模块做测试，而不关心引入的外部模块是否正常，如果引入的外部模块中含有大量的复杂逻辑，则会拖慢测试的性能，因此可以采用模拟类的方式解决
```
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


```
// class.js  待测试的方法
import Util from "./util";
export const class1 = (a, b) => {
    const util = new Util()
    util.a(a)
    util.b(b)
}
```

```
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

##### DOM节点操作

node环境下是不具备dom的，但是jest在node环境下自己模拟了一套dom api，需要安装jquery模块
```
import $ from 'jquery'

const addDiv = () => {
    $('body').append('<div>aaa</div>')
}

export default addDiv
```

```
import addDiv from './dom'
import $ from 'jquery'

// node环境下是不具备dom的
// 但是jest在node环境下自己模拟了一套dom api
test('测试dom', () => {
    addDiv()
    expect($('body').find('div').length).toBe(1)
})
```

#### 自动化测试介绍
##### TDD vs BDD
**（1）TDD**
测试驱动开发的模式，写代码之前先会去想测试怎么写，这样有助于先思考代码怎么实现，可以有效地提升写代码的质量
**开发流程**
1. 编写测试用例
2. 运行测试，测试用例无法通过测试
3. 编写代码，使测试用例通过测试
4. 优化代码，完成开发
5. 重复上述步骤
**优势：**
1. 长期减少回归bug
2. 代码质量更好（组织、可维护性）
3. 测试覆盖率高
4. 错误测试代码不容易出现
**特点:**
1. 先写测试再写代码
2. 一般结合单元测试使用，是白盒测试
3. 测试重点是代码
4. 安全感低
5. 速度快

**（2）BDD**
行为驱动开发的模式，先写完所有业务代码，之后再根据用户的行为去写测试代码，这样能够保证测试的行为在项目运行时能够正确地执行
**开发流程**
1. 编写完代码
2. 根据用户行为编写测试用例
3. 运行测试，验证测试用例是否通过
4. 优化代码，保证测试用例全部通过
5. 重复上述步骤
**特点:**
1. 先写代码再写测试
2. 一般结合集成测试使用，是黑盒测试
3. 测试重点在UI（DOM）
4. 安全感高
5. 速度慢

##### 单元测试 vs 集成测试
**（1）单元测试**：针对一个模块（单元）进行测试，当我们对一个模块或一个组件进行单元测试的时候，测试覆盖率会非常高，但是单元测试也存在一些缺点，比如业务耦合度高，当我们要修改组件逻辑或数据的时候，要重新修改单元测试的代码，会使代码量增大。单元测试也会增加工作量，因为要对组件或模块进行全覆盖地测试，有时候测试代码会比业务本身的代码还要多。

单元测试只能确保各模块或组件能正常运行，但是不能确保它们组件起来也能正常运行，单元测试过于独立，不能保证整个项目能够正常运行。结合它的一些优点和缺点，单元测试在某些场景下是适用的，某些场景下是不适用的。

例如我们要开发一个函数库，这个时候使用TDD+单元测试就很合适，因为函数库是一个个函数入参和结果生成，因此写单元测试不会和函数耦合得很紧密。当写业务代码的时候，业务耦合度会很高，单元测试实际上并不是很适用。

**（2）集成测试**：测的是一个用户功能模块，比如用户注册功能，集成测试完全是用测试脚本去模拟用户操作，比如打开浏览器、点击注册链接、输入用户名密码、点击注册。集成测试不用关注代码自身的实现，而是根据一系列的用户操作编写测试用例，保证其功能正常即可。

集成测试能确保它们组件起来能正常运行，但是其测试覆盖率没有单元测试高。

在项目业务开发过程中，使用BDD+集成测试就很合适，因为我们可以先写完代码，再根据用户行为去测试功能是否正常，开发过程比单元测试更为快捷，并且能够有效保证功能逻辑正常。

##### 前端自动化测试的优势
1. 更好的代码组织，项目的可维护性增强
2. 更小的bug出现频率，尤其是回归测试中的bug
3. 修改工程质量差的项目时，更加安全
4. 项目具备潜在的文档特性
5. 扩展前端的知识面（深入框架底层、node知识）

#### vue项目自动化测试

##### vue环境下配置jest
1. 使用vue脚手架创建项目，`vue create [name]`,手动配置，选择jest，会自动生成jest.config.js配置文件
2. 如果是自己手动搭建vue项目环境，需要支持jest的话，可以创建一个jest.config.js文件，内容如下：（记得安装第三方依赖）

```
module.exports = {
    moduleFileExtensions: [
        'js',
        'jsx',
        'json',
        'vue'
    ],
    transform: {
        '^.+\\.vue$': 'vue-jest',
        '.+\\.(css|styl|less|sass|scss|svg|png|jpg|ttf|woff|woff2)$': 'jest-transform-stub',
        '^.+\\.jsx?$': 'babel-jest'
    },
    transformIgnorePatterns: [
        '/node_modules/'
    ],
    moduleNameMapper: {
        '^@/(.*)$': '<rootDir>/src/$1'
    },
    snapshotSerializers: [
        'jest-serializer-vue'
    ],
    testMatch: [
        '**/tests/unit/**/*.spec.(js|jsx|ts|tsx)|**/__tests__/*.(js|jsx|ts|tsx)'
    ],
    testURL: 'http://localhost/',
    watchPlugins: [
        'jest-watch-typeahead/filename',
        'jest-watch-typeahead/testname'
    ]
}
```

##### vue-test-utils的配置和使用

[官方文档](https://vue-test-utils.vuejs.org/)
1. shallowMount适合单元测试时使用，只做浅渲染，不渲染内部引入的其他组件，知识用占位符代表其引入，从而提高性能
2. mount是全部渲染，做集成测试时，mount是合理的选择，但是会降低性能
`import {shallowMount,mount} from '@vue/test-utils'`

使用快照测试，可以帮助我们及时捕获UI的变化，判断组件是否正常渲染。
```
import Vue from 'vue'
import {shallowMount} from '@vue/test-utils'
import Hello from '@/components/Hello.vue'
import {wrap} from 'module';

describe('测试Hello.vue', () => {
    it('不适用vue-test-utils，原始方法进行测试，较为繁琐', () => {
        const root = document.createElement('div')
        root.className = 'root'
        document.body.append(root)
        new Vue({
            render: h => h(Hello, {
                props: {
                    msg: 'hello'
                }
            })
        }).$mount('.root')
        expect(document.getElementsByClassName('hello-container').length).toBe(1)
    })

    // shallowMount适合单元测试时使用，只做浅渲染，不渲染内部引入的其他组件，知识用占位符代表其引入，从而提高性能
    // mount是全部渲染，做集成测试时，mount是合理的选择，但是会降低性能
    it('使用vue-test-utils进行测试', () => {
        const msg = 'hello world'
        const wrapper = shallowMount(Hello, {
            propsData: {
                msg
            }
        })
        expect(wrapper.text()).toMatch(msg)
        expect(wrapper.props('msg')).toEqual(msg)
        expect(wrapper.findAll('.hello-container').length).toBe(1)
    })

    // 快照测试，可以帮助我们及时捕获UI的变化
    it('单纯测试组件渲染是否正常', () => {
        const msg = 'hello world'
        const wrapper = shallowMount(Hello, {
            propsData: {
                msg
            }
        })
        expect(wrapper).toMatchSnapshot()
    })
})
```

##### 实战

TDD开发Header组件

```
import {
    shallowMount
} from '@vue/test-utils'
import Header from '../../components/header.vue'

// 建议看vue-test-utils英文文档，看wrapper具有哪些方法
it('header 包含 input框', () => {
    const wrapper = shallowMount(Header)
    const input = wrapper.find('[data-test="input"]')
    expect(input.exists()).toBe(true)
})
it('input 框初始值为空', () => {
    const wrapper = shallowMount(Header)
    const inputValue = wrapper.vm.$data.inputValue
    expect(inputValue).toBe('')
})
it('input 发生变化，数据跟着变化', () => {
    const wrapper = shallowMount(Header)
    const input = wrapper.find('[data-test="input"]')
    input.setValue('hello world')
    const inputValue = wrapper.vm.$data.inputValue
    expect(inputValue).toBe('hello world')
})
it('input 中无内容时，无反应', () => {
    const wrapper = shallowMount(Header)
    const input = wrapper.find('[data-test="input"]')
    input.setValue('')
    input.trigger('keyup.enter')
        // 不应该向外触发add事件
    expect(wrapper.emitted().add).toBeFalsy()
})
it('input 中有内容时，向外触发add事件，同时清空input', () => {
    const wrapper = shallowMount(Header)
    const input = wrapper.find('[data-test="input"]')
    input.setValue('aaaaaaaaa')
    input.trigger('keyup.enter')
    expect(wrapper.emitted().add).toBeTruthy()
    expect(wrapper.vm.$data.inputValue).toBe('')
})
```

TDD开发Todo页面

```
import {
    shallowMount
} from '@vue/test-utils'
import TodoList from '../../TodoList.vue'
import Header from '../../components/header.vue'

it('TodoList初始化时，undoList为空', () => {
    const wrapper = shallowMount(TodoList)
    const undoList = wrapper.vm.$data.undoList
    expect(undoList).toEqual([])
})
it('TodoList监听到Header的add事件时，会增加一个内容', () => {
    const content = 'learning IT'
    const wrapper = shallowMount(TodoList)
    const header = wrapper.find(Header)
    header.vm.$emit('add', content)
    const undoList = wrapper.vm.$data.undoList
    expect(undoList).toEqual([content])
})
```
修复eslint报错：`npm run lint --fix`

增加快照测试，能够保证页面UI样式不发生变化，如果变化会及时通知，输入w/u进行手动确认

```
it('header样式发生改变作提示', () => {
    const wrapper = shallowMount(Header)
    expect(wrapper).toMatchSnapshot()
})
```
#### react项目自动化测试
##### react环境下配置jest
create-react-app中自动集成了jest，具体配置在package.json文件中。

如果是自己搭建react项目环境，则可以配置jest.config.js文件

```
module.exports = {
    "roots": [
        "<rootDir>/src"
    ],
    "collectCoverageFrom": [
        "src/**/*.{js,jsx,ts,tsx}",
        "!src/**/*.d.ts"
    ],
    "setupFiles": [
        "react-app-polyfill/jsdom"
    ],
    "setupFilesAfterEnv": [],
    "testMatch": [
        "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",
        "<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}"
    ],
    "testEnvironment": "jest-environment-jsdom-fourteen",
    "transform": {
        "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest",
        "^.+\\.css$": "<rootDir>/config/jest/cssTransform.js",
        "^(?!.*\\.(js|jsx|ts|tsx|css|json)$)": "<rootDir>/config/jest/fileTransform.js"
    },
    "transformIgnorePatterns": [
        "[/\\\\]node_modules[/\\\\].+\\.(js|jsx|ts|tsx)$",
        "^.+\\.module\\.(css|sass|scss)$"
    ],
    "modulePaths": [],
    "moduleNameMapper": {
        "^react-native$": "react-native-web",
        "^.+\\.module\\.(css|sass|scss)$": "identity-obj-proxy"
    },
    "moduleFileExtensions": [
        "web.js",
        "js",
        "web.ts",
        "ts",
        "web.tsx",
        "tsx",
        "json",
        "web.jsx",
        "jsx",
        "node"
    ],
    "watchPlugins": [
        "jest-watch-typeahead/filename",
        "jest-watch-typeahead/testname"
    ]
}
```
##### 安装enzyme

[github平台](https://github.com/airbnb/enzyme)、[官方文档](https://airbnb.io/enzyme/)

安装：`npm i --save-dev enzyme enzyme-adapter-react-16`
```
// 在test文件中引入
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

Enzyme.configure({ adapter: new Adapter() });
```

在测试时，建议给DOM添加data-test属性，这样能降低与原代码class、id等选择器的耦合度，改动class、id依然能选择出正确的DOM节点。

单元测试适合用shallow、集成测试适合用mount；shallow是浅渲染（不渲染内部子组件）、mount是深渲染。

`import Enzyme, {shallow,mount} from 'enzyme'`

`wrapper.debug()`可以打印出完整的DOM信息（对应wrapper），更方便调试。

快照`expect(wrapper).toMatchSnapshot()`的使用：如果我们对页面的展示内容比较敏感，一般不会去改动，改动时需要反复校验，遇到这种页面或组件时，可以采用snapshot对其进行约束。

安装`jest-enzyme`可以采用更多的匹配器，方便测试的书写。
具体匹配器写法详见 [jest-enzyme](https://github.com/FormidableLabs/enzyme-matchers/tree/master/packages/jest-enzyme)

```
import React from 'react';
import App from './App';
import Enzyme, {
    shallow,
    mount
} from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'

Enzyme.configure({
    adapter: new Adapter()
})

it('renders without crashing', () => {
    const wrapper = shallow( < App / > )
    expect(wrapper).toMatchSnapshot()
    const container = wrapper.find('[data-test="container"]')
    expect(container).toExist()
    expect(container).toHaveProp('title', 'app')
});
```
分别编写Header和TodoList组件的测试代码：

##### 实战
```
// Header组件
import React from 'react';
import Enzyme, {
    shallow
} from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'
import Header from '../../components/Header.jsx'
Enzyme.configure({
    adapter: new Adapter()
})

describe('Header组件', () => {
    it('包含一个input框', () => {
        const wrapper = shallow(< Header />)
        const inputEle = wrapper.find('[data-test="input"]')
        expect(inputEle.length).toBe(1)
    });

    it('input初始化应该为空', () => {
        const wrapper = shallow(< Header />)
        const inputEle = wrapper.find('[data-test="input"]')
        expect(inputEle.prop('value')).toEqual('')
    });

    it('当用户输入时，input内容应相应变化', () => {
        const wrapper = shallow(< Header />)
        const inputEle = wrapper.find('[data-test="input"]')
        const userInput = 'learning'
        inputEle.simulate('change', {
            target: {
                value: userInput
            }
        })

        {/* 或者测试数据 */}
        expect(wrapper.state('value')).toEqual(userInput)
        {/* 或者测试DOM */}
        const newInputEle = wrapper.find('[data-test="input"]')
        expect(newInputEle.prop('value')).toBe(userInput)
    });

    it('input框输入回车时，如果无内容则无操作', () => {
        const fn = jest.fn()
        const wrapper = shallow(< Header addUndoItem={fn} />)
        const inputEle = wrapper.find('[data-test="input"]')
        wrapper.setState({
            value: ''
        })
        inputEle.simulate('keyup', {
            keyCode: 13
        })
        expect(fn).not().toHaveBeenCalled()
    });

    it('input框输入回车时，如果有内容则调用添加函数', () => {
        const fn = jest.fn()
        const wrapper = shallow(< Header addUndoItem={fn} />)
        const inputEle = wrapper.find('[data-test="input"]')
        const userInput = 'hhhhhh'
        wrapper.setState({
            value: userInput
        })
        inputEle.simulate('keyup', {
            keyCode: 13
        })
        expect(fn).toHaveBeenCalled()
        expect(fn).toHaveBeenLastCalledWith(userInput)
        const newInputEle = wrapper.find('[data-test="input"]')
        expect(newInputEle.prop('value')).toBe('')
    });
})
```

```
// TodoList组件
import React from 'react';
import Enzyme, {
    shallow
} from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'
import Todolist from '../../index.jsx'
Enzyme.configure({
    adapter: new Adapter()
})

describe('Todolistr组件', () => {
    it('初始化列表为空', () => {
        const wrapper = shallow(<Todolist />)
        expect(wrapper.state('undoList')).toEqual([])
    })
    it('应该给header传递增加undoList的方法', () => {
        const wrapper = shallow(<Todolist />)
        const header = wrapper.find('Header')
        expect(header.prop('addUndoItem')).toBe(wrapper.instance.addUndoItem)
    })
    it('header回车时，undoList应该新增内容', () => {
        const wrapper = shallow(<Todolist />)
        const header = wrapper.find('Header')
        const addFunc = header.prop('addUndoItem')
        const userInput = 'learning makes me happy'
        addFunc(userInput)
        expect(wrapper.state('undoList').length).toBe(1)
        expect(wrapper.state('undoList')[0]).toBe(userInput)
        addFunc(userInput)
        expect(wrapper.state('undoList').length).toBe(2)
    })
})
```

提取enzyme通用配置，需要在jest.config.js中进行配置

```
// test.setup.js
import Enzyme from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'
Enzyme.configure({
    adapter: new Adapter()
})

// jest.config.js
module.exports = {
  "setupFilesAfterEnv": [
     './node_modules/jest-enzyme/lib/index.js', 
     //这里是新增的js文件
     '<rootDir>/src/utils/test.setup.js'
  ]
}
```
