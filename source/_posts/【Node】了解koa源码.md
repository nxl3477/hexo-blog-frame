---
title: 【Node】了解koa源码
date: 2019-06-16 22:29:06
categories: NodeJs
tags: [ NodeJs, Koa, 源码 ]
---

想要查看源码， 首先我们
```shell
npm i Koa -S
```

打开`node_modules`， 找到koa目录下的application 文件，

我们可以看到平时使用的 app 及其相关方法就在这里, 删除了不必要的代码后， 我们看看简易版

```js
module.exports = class Application extends Emitter {

  constructor() {
    super();

    this.proxy = false;
    this.middleware = [];
    this.subdomainOffset = 2;
    this.env = process.env.NODE_ENV || 'development';
    this.context = Object.create(context);
    this.request = Object.create(request);
    this.response = Object.create(response);
    if (util.inspect.custom) {
      this[util.inspect.custom] = this.inspect;
    }
  }

  listen(...args) {
    debug('listen');
    const server = http.createServer(this.callback());
    return server.listen(...args);
  }

  toJSON() {
    return only(this, [
      'subdomainOffset',
      'proxy',
      'env'
    ]);
  }

  inspect() {
    return this.toJSON();
  }

  use(fn) {
    this.middleware.push(fn);
    return this;
  }

  callback() {
    const fn = compose(this.middleware);

    const handleRequest = (req, res) => {
      const ctx = this.createContext(req, res);
      return this.handleRequest(ctx, fn);
    };

    return handleRequest;
  }

  handleRequest(ctx, fnMiddleware) {
    const res = ctx.res;
    res.statusCode = 404;
    const onerror = err => ctx.onerror(err);
    const handleResponse = () => respond(ctx);
    onFinished(res, onerror);
    return fnMiddleware(ctx).then(handleResponse).catch(onerror);
  }

  createContext(req, res) {
    const context = Object.create(this.context);
    const request = context.request = Object.create(this.request);
    const response = context.response = Object.create(this.response);
    context.app = request.app = response.app = this;
    context.req = request.req = response.req = req;
    context.res = request.res = response.res = res;
    request.ctx = response.ctx = context;
    request.response = response;
    response.request = request;
    context.originalUrl = request.originalUrl = req.url;
    context.state = {};
    return context;
  }

  /**
   * Default error handler.
   *
   * @param {Error} err
   * @api private
   */

  onerror(err) {
    const msg = err.stack || err.toString();
    console.error(msg.replace(/^/gm, '  '));
  }
};

function respond(ctx) {

  const res = ctx.res;
  let body = ctx.body;
  const code = ctx.status;

  // body: json
  body = JSON.stringify(body);
  res.end(body);
}
```




## 灵魂部分
```js
function compose (middleware) {
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  // 检测中间件类型
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }



  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0) // 执行整这个函数
    function dispatch (i) {
      index = i
      // 取出当前中间件
      let fn = middleware[i]
      // 如果索引跟中间件长度一样了,实际上就等于访问完了, fn是空,  next 也是空, 这里应该是保证一下next 确实不存在
      if (i === middleware.length) fn = next
      // 最后一个中间件没有 next 就熄火了
      if (!fn) return Promise.resolve()
      try {
        // fn 就是中间件,  中间件接收两个参数, context 和 Next
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        // 出错
        return Promise.reject(err)
      }
    }
  }
}
```







