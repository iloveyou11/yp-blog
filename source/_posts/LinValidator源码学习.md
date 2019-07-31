---
title: LinValidator源码学习
date: 2019-07-25
categories: 源码
author: yangpei
tags:
  - 源码
comments: true
cover_picture: /images/banner.jpg
---

在闲暇时间，学习了一下林间有风团队开发的 LinValidator 插件源码，具体的调用方式如下：

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

---

代码组织结构图如下：
<img src="http://i2.tiimg.com/695012/b68f75f7cdecf40f.png" width="100%"/>

---

具体的源码如下：

```javascript
const validator = require("validator");
const { get, set, cloneDeep } = require("lodash");
// lodash中get函数
// object(Object): 要检索的对象。
// path (Array|string): 要获取属性的路径。
// [defaultValue] (*): 如果解析值是 undefined ，这值会被返回。

// 定义辅助函数,找出obj对象上(包括原型链)所有的以prefix开头|是type的实例|满足filter条件的属性
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

// 定义异常,与后端约定返回字段，msg为提示消息，code为自定义错误码，status为http状态码
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

  // 辅助函数
  _getAllParams(ctx) {
    return {
      body: ctx.request.body,
      query: ctx.request.query,
      path: ctx.params,
      header: ctx.request.body
    };
  }

  // 获取class中所有是Rule实例的属性,方便之后的校验
  _findAllRulesArr(key) {
    // 这里是什么意思,不太明白
    // if (/validate([A-Z])\w+/g.test(key)) {
    //     return true
    // }
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

  // 前端调用的形式是const v = await new PositiveIdValidator().validate(ctx, {id: "uid"});
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

// 对每个字段中一个rule校验规则进行校验的结果类
class RuleResult {
  constructor(pass, msg: "") {
    Object.assign(this, {
      pass,
      msg
    });
  }
}
// 对每个字段全部rule校验规则进行校验的结果类
class RuleFieldResult extends RuleResult {
  constructor(pass, msg, legalValue = null) {
    super(pass, msg);
    this.legalValue = legalValue;
  }
}

// Rule校验类
// 调用形式: this.group_id = new Rule("isInt", "分组id必须是整数，且大于0", {min: 1});
// params参数可以为空
// 针对单条rule进行校验
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

// 针对字段所有的rule进行校验
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

module.exports = {
  Rule,
  MyValidator
};
```
