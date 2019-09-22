---
title: koa+nuxt基础
date: 2019-06-10
categories: 技能
author: yangpei
tags:
  - koa
  - nuxt
comments: true
cover_picture: /images/banner.jpg
---

#### koa

##### 一、开始

<!-- more -->

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

##### 二、路由

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

##### 三、请求数据

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

##### 四、静态资源加载

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

##### 五、cookie 和 session

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

##### 六、模板引擎

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

##### 七、文件上传

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

##### 八、数据库 mysql

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

##### 九、jsonp

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

##### 十、单元测试

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

#### nuxt

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

##### nuxt 官方文档 https://zh.nuxtjs.org/guide/

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
