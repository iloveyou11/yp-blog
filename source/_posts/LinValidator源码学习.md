---
title: LinValidator源码学习
date: 2019-07-25
categories: 源码
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

在闲暇时间，学习了一下林间有风团队开发的 LinValidator 插件源码，具体的调用方式如下，使用方法很简单，只需要在校验器类中针对待检验的属性各创建自己的Rule的实例，里面写具体的校验规则即可，当然也可以自定义规则函数。同时，校验器之间可以实现继承。

<!-- more -->

[官方网站](http://doc.cms.7yue.pro/lin/server/koa/validator.html#%E4%BD%BF%E7%94%A8)


```javascript
// 规则

// 首先任何一个规则函数，满足以validate开头的类方法，除validate()这个函数外。
// 都会被带入一个重要的参数data。 data是前端传入参数的容器， 它的整体结构如下：
// this.data = {
//     body: ctx.request.body, // body -> body
//     query: ctx.request.query, // query -> query
//     path: ctx.params, // params -> path
//     header: ctx.request.header // header -> header
//   };

//会对 ctx.request.body(上下文请求体)、ctx.request.query(上下文请求query参数) 、ctx.request.header(上下文请求头)、ctx.param(路由参数)
class RegisterValidator extends LinValidator {
  constructor() {
    super();
    this.nickname = [
      new Rule("isNotEmpty", "昵称不可为空"),
      new Rule("isLength", "昵称长度必须在2~10之间", 2, 10)
    ];
    this.group_id = new Rule("isInt", "分组id必须是整数，且大于0", {
      min: 1
    });
    this.email = [
      new Rule("isOptional"),
      new Rule("isEmail", "电子邮箱不符合规范，请输入正确的邮箱")
    ];
    this.password = [
      new Rule(
        "matches",
        "密码长度必须在6~22位之间，包含字符、数字和 _ ",
        /^[A-Za-z0-9_*&$#@]{6,22}$/
      )
    ];
    this.confirm_password = new Rule("isNotEmpty", "确认密码不可为空");
  }

  //   自定义规则函数
  validateConfirmPassword(data) {
    if (!data.body.password || !data.body.confirm_password) {
      return [false, "两次输入的密码不一致，请重新输入"];
    }
    let ok = data.body.password === data.body.confirm_password;
    if (ok) {
      return ok;
    } else {
      return [false, "两次输入的密码不一致，请重新输入"];
    }
  }
}

// 实现继承
class PositiveIdValidator extends LinValidator {
  constructor() {
    super();
    this.id = new Rule("isInt", "id必须为正整数", {
      min: 1
    });
  }
}
class UpdateUserInfoValidator extends PositiveIdValidator {
  constructor() {
    super();
    this.group_id = new Rule("isInt", "分组id必须是正整数", {
      min: 1
    });
    this.email = new Rule("isEmail", "电子邮箱不符合规范，请输入正确的邮箱");
  }
}

// 别名，会对uid参数值做校验
const v = await new PositiveIdValidator().validate(ctx, {
  id: "uid"
});
```


代码组织结构图如下：
<img style="width:100%" src="https://i.loli.net/2019/09/02/83DoHCrNsM4fBTJ.jpg" />


具体的源码如下，并作详细的源码讲解：

```javascript
const validator = require("validator");
const { get, set, cloneDeep } = require("lodash");

```
lodash中get函数使用时需要的参数：
object(Object): 要检索的对象。
path (Array|string): 要获取属性的路径。
[defaultValue] (*): 如果解析值是 undefined ，这值会被返回

cloneDeep是lodash提供的深拷贝方法，如果想具体了解深拷贝，可以跳转这里：
- [cloneDeep](#cloneDeep)


以下的_findMember函数，是找出obj对象上(包括原型链)所有的以prefix开头|是type的实例|满足filter条件的属性。
举个栗子：
如果这样调用_findMembers(obj,{prefix="class",type="Student",filter="name"),则代表要保留obj实例自身和原型链上以"class"开头、或是Student实例、或是name的全部属性。


_getAllParams是一次获取ctx上的参数和属性，包括body、query、params、header

_findAllRulesArr是获取class中所有是Rule实例的属性,方便之后的校验
```javascript
const _findMembers = (obj, { prefix, type, filter }) => {
  function _find(obj) {
    if (obj.__proto__ === null) {
      return [];
    }
    let names = Reflect.ownKeys(obj);
    names = names.filter(name => {
      return _needKeep(name);
    });
    return [...names, ..._find(obj.__proto__)];
  }

  function _needKeep(name) {
    if (prefix) {
      if (name.startsWith(prefix)) {
        return true;
      }
    }
    if (type) {
      if (obj[name] instanceof type) {
        return true;
      }
    }
    if (filter) {
      if (filter(name)) {
        return true;
      }
    }
  }

  return _find(obj);
};

_getAllParams(ctx) {
  return {
    body: ctx.request.body,
    query: ctx.request.query,
    path: ctx.params,
    header: ctx.request.header
  };
}

// 获取class中所有是Rule实例的属性,方便之后的校验
_findAllRulesArr(key) {
  // 这里是什么意思,不太明白
  if (/validate([A-Z])\w+/g.test(key)) {
      return true
  }
  if (this[key] instanceof Array) {
    this[key].forEach(value => {
      if (!(value instanceof Rule)) {
        throw new Error("验证数组必须全部为Rule类型");
      }
    });
    return true;
  }
  return false;
}

```

定义自定义异常,与后端约定返回字段，msg为提示消息，code为自定义错误码，status为http状态码
```javascript
class HttpError extends Error {
  constructor(msg = "服务器异常", code = 10000, status = 500) {
    super();
    Object.assign(this, {
      msg,
      code,
      status
    });
  }
}
class ParamsError extends HttpError {
  constructor(msg, code) {
    this.msg = msg || "参数错误";
    this.code = code || 10001;
    this.status = 400;
  }
}

class MyValidator {
  constructor() {
    this.data = {};
    this.parsed = {};
  }
}
```


前端调用的形式是const v = await new PositiveIdValidator().validate(ctx, {id: "uid"});
```javascript
  async validate(ctx, alias = {}) {
    this.alias = alias;
    let params = this._getAllParams(ctx);
    this.data = cloneDeep(params);
    this.parsed = cloneDeep(params);

    const members = _findMembers(this, {
      filter: this._findAllRulesArr.bind(this)
    });

    let errorMsg = [];
    for (let key of members) {
      const result = await this._check(key, alias);
      if (!result.success) {
        errorMsg.push(result.msg);
      }
    }
    if (errorMsg.length !== 0) {
      throw new ParamsError(errorMsg);
    }
    ctx.v = this;
    return this;
  }

  // 验证每个字段是否通过rules数组
  async _check(key, alias = {}) {
    let isFunc = typeof this[key] === "function" ? true : false;
    // 如果是函数,则是采用自定义校验形式
    let result;
    if (isFunc) {
      try {
        await this[key](this.data);
        result = new RuleResult(true);
      } catch (error) {
        result = new RuleResult(
          false,
          error.msg || error.message || "参数错误"
        );
      }
    } else {
      //如果不是函数,则使用rules数组中的rule分别校验字段是否满足条件
      const rules = this[key];
      const ruleField = new RuleField(rules);
      // 如果存在别名,则进行替换
      key = alias[key] ? alias[key] : key;
      const param = this._findParams(key);
      result = ruleField.validate(param.value);
      if (result.pass) {
        // 如果参数路径不存在，往往是因为用户传了空值，而又设置了默认值
        if (param.path.length === 0) {
          set(this.parsed, ["default", key], result.legalValue);
        } else {
          set(this.parsed, param.path, result.legalValue);
        }
      }
    }
    if (!result.pass) {
      const msg = `${isCustomFunc ? "" : key}${result.msg}`;
      return {
        msg: msg,
        success: false
      };
    }
    return {
      msg: "ok",
      success: true
    };
  }
  // 使用lodash中的get函数,第二个参数是路径,可以采用a.b.c的形式,也可以采用['a','b','c']的形式
  _findParams(key) {
    let value;
    value = get(this.data, ["query", key]);
    if (value) {
      return {
        value,
        path: ["query", key]
      };
    }
    value = get(this.data, ["body", key]);
    if (value) {
      return {
        value,
        path: ["body", key]
      };
    }
    value = get(this.data, ["path", key]);
    if (value) {
      return {
        value,
        path: ["path", key]
      };
    }
    value = get(this.data, ["header", key]);
    if (value) {
      return {
        value,
        path: ["header", key]
      };
    }
    return {
      value: null,
      path: []
    };
  }
}
```

对每个字段中一个rule校验规则进行校验的结果类
```javascript
class RuleResult {
  constructor(pass, msg: "") {
    Object.assign(this, {
      pass,
      msg
    });
  }
}
```

对每个字段全部rule校验规则进行校验的结果类
```javascript
class RuleFieldResult extends RuleResult {
  constructor(pass, msg, legalValue = null) {
    super(pass, msg);
    this.legalValue = legalValue;
  }
}
```

Rule校验类
调用形式: this.group_id = new Rule("isInt", "分组id必须是整数，且大于0", {min: 1});
params参数可以为空
针对单条rule进行校验

```javascript
class Rule {
  constructor(name, msg, ...params) {
    Object.assign(this, {
      name,
      msg,
      params
    });
  }
  validate(field) {
    if (this.name === "isOptional") {
      return new RuleResult(true); //pass置为true,表示此条规则验证通过
    }
    // validate库的使用,内置了大量的校验函数
    if (!validator[this.name](field + "", ...this.params)) {
      return new RuleResult(false, this.msg || this.message || "参数错误");
    }
    return new RuleResult(true, "");
  }
}
```

针对字段所有的rule进行校验
```javascript
class RuleField {
  constructor(rules) {
    this.rules = rules;
  }
  validate(field) {
    // 如果待校验的字段为空,则检查是否设置了可以为空,或有默认值
    if (field == null) {
      const allowEmpty = this._allowEmpty();
      const defaultValue = this._hasDefault();
      if (allowEmpty) {
        return new RuleFieldResult(true, "", defaultValue);
      } else {
        return new RuleFieldResult(false, "字段是必填参数");
      }
    }
    const fieldResult = new RuleFieldResult(false); //先设置初始状态为false,此字段验证没通过
    for (let rule of this.rules) {
      let result = rule.validate(field);
      if (!result.pass) {
        fieldResult.msg = result.msg;
        fieldResult.legalValue = null;
        // 一旦一条校验规则不通过，则立即终止这个字段的验证
        return filedResult;
      }
    }
    return new RuleFieldResult(true, "", this._convert(field));
  }
  // 如果字段设置形式为 isInt\isFloat\isBoolean 的形式,则要进行字段格式转化
  // 如 this.id = new Rule("isInt", "必须为整数");
  _convert(value) {
    for (let rule of this.rules) {
      if (rule.name == "isInt") {
        return parseInt(value);
      }
      if (rule.name == "isFloat") {
        return parseFloat(value);
      }
      if (rule.name == "isBoolean") {
        return value ? true : false;
      }
    }
    return value;
  }
  _allowEmpty() {
    for (let rule of this.rules) {
      if (rule.name == "isOptional") {
        return true;
      }
    }
    return false;
  }
  _hasDefault() {
    for (let rule of this.rules) {
      const defaultValue = rule.params[0];
      if (rule.name == "isOptional") {
        return defaultValue;
      }
    }
  }
}
```

最后将Rule、MyValidator分别导出，引入项目中即可使用
```javascript
module.exports = {
  Rule,
  MyValidator
};
```



### <a id="cloneDeep">cloneDeep</a>
在了解深拷贝之前，首先需要了解一下深拷贝与浅拷贝的区别：

1.浅拷贝： 创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。
2.深拷贝： 将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象

当然，要想实现深拷贝，则需要将对象中的每个属性完全拷贝到新的对象中。而往往项目中我们常使用Object.assign()实现的复制，要注意，这里的Object.assign()只是一个浅复制的过程，因为对象只会被克隆最外部的一层,至于更深层的对象,依然是通过引用指向同一块堆内存。可见，对于结构层次很深的对象，这种方法并不是好的解决方案。

下面由浅入深，依次来完善这个深拷贝函数：

- **JSON.parse方法**
```JavaScript
const newObj = JSON.parse(JSON.stringify(oldObj));
```
这种方法其实是存在很多坑的，比如：

1. 无法实现对函数 、RegExp等特殊对象的克隆
2. 抛弃对象的constructor,所有的构造函数会指向Object
3. 对象有循环引用,会报错


实现一个完整的深克隆是由许多坑要踩的,npm上一些库的实现也不够完整,在生产环境中最好用**lodash**的深克隆实现.

- **递归调用，完成各层次属性的拷贝**

```JavaScript
function clone(target) {
    if (typeof target === 'object') {
        let cloneTarget = {}; //这里有个问题，如何是拷贝数组呢？
        for (const key in target) {
            cloneTarget[key] = clone(target[key]);
        }
        return cloneTarget;
    } else {
        return target;
    }
};
```
- **修复数组问题**
```JavaScript
function clone(target) {
    if (typeof target === 'object') {
        let cloneTarget = Array.isArray(target) ? [] : {};
        for (const key in target) {
            cloneTarget[key] = clone(target[key]);
        }
        return cloneTarget;
    } else {
        return target;
    }
};
```
- **解决循环引用**
```
检查map中有无克隆过的对象
有 - 直接返回
没有 - 将当前对象作为key，克隆对象作为value进行存储
继续克隆
```

```JavaScript
function clone(target, map = new Map()) {
    if (typeof target === 'object') {
        let cloneTarget = Array.isArray(target) ? [] : {};
        if (map.get(target)) {
            return map.get(target);
        }
        map.set(target, cloneTarget);
        for (const key in target) {
            cloneTarget[key] = clone(target[key], map);
        }
        return cloneTarget;
    } else {
        return target;
    }
};
```

- **性能优化**

使用weakMap代替map

我们默认创建一个对象：const obj = {}，就默认创建了一个强引用的对象，我们只有手动将obj = null，它才会被垃圾回收机制进行回收，如果是弱引用对象，垃圾回收机制会自动帮我们回收。如果我们要拷贝的对象非常庞大时，使用Map会对内存造成非常大的额外消耗，而且我们需要手动清除Map的属性才能释放这块内存，而WeakMap会帮我们巧妙化解这个问题。

- **考虑其他数据类型**

……
