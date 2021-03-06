---
sidebar: auto
---

# Midway 开发指南


## 介绍

Midway 自 2013 年开始，基本保持着一年一个大版本的迭代速度进行更新，从 express 到 koa1/2，从未缺席。

如今，集团内外的 Node.js 大环境早已脱离原有的前后端分离体系，朝着全栈的方向大步迈进，有着欣喜，有着期待，对此，MidwayJs 团队不仅仅承担着引导，支撑集团 Node.js 应用的责任，也同时通过 `Pandora.js` ，`Sandbox`  等不同形式来让应用变的更加稳定，可靠。

在 2017 年，我们将集团内部的 Midway 5.3 升级到了基于 Koa2 的模型，全面支持了 async/await 的编码风格。
''
同年年中，我们开始计划将监控和数据采集能力进行抽象剥离，形成了全新 Pandora.js 工具，不仅仅服务于 Midway，也服务于全网，乃至外部所有的 Node.js 应用。

2018 年，MidwayJs 团队将基于 Typescript，将 Midway6 在新的语言层面进行升级，让用户在开发体验上更近一步，对外部则是从第一个版本开始。

为什么选择 Typescript ? 相信[ 这篇文章](https://juejin.im/post/59c46bc86fb9a00a4636f939) 会给你一些答案。 


## 关于 Midway

Midway (中途岛) 品牌是淘宝技术部（前淘宝UED）前端部门研发的一款基于 Node.js 的全栈开发解决方案。它将搭配团队的其他产品，pandora 和 sandbox，将 Node.js 的开发体验朝着全新的场景发展，让用户在开发过程中享受到前所未有的愉悦感。


## 快速上手

### 安装 Node 环境

可以访问 Node.js 官网或者使用 nvm 等类似产品，不再赘述。

### 创建新应用

使用 [midway-init](https://www.npmjs.com/package/midway-init) 工具自动创建 midway 应用的目录结构:

```bash
$ npm i midway-init -g
$ midway-init
```


目前只有一个 ts 的脚手架，可以直接使用。

通过生成的 `npm scripts` 来驱动启动命令:

```bash
$ npm install
$ npm run dev
```

### 了解目录结构

midway 的目录和 eggjs 目录非常接近，但也有所区别，不同的地方在于：

* ts 源码存放于 `/src` 目录下，编译后代码存放于 `/dist` 下
* 以往的 app 等都迁移至 `/src/app` 下，作为 web 层
* 传统的业务逻辑等，移动到其他目录，比如 `lib/service`

```plain
➜  midway6-test tree -I node_modules
.
├── README.md
├── README.zh-CN.md
├── dist                                ---- 编译后目录
├── logs                                ---- 本地日志目录
│   └── midway6-test                    ---- 日志应用名开头
│       ├── common-error.log            ---- 错误日志
│       ├── midway-agent.log            ---- agent 输出的日志
│       ├── midway-core.log             ---- 框架输出的日志
│       ├── midway-web.log              ---- koa 输出的日志
│       └── midway6-test-web.log
├── package.json
├── src                                 ---- 源码目录
│   ├── app                             ---- web 层目录
│   │   ├── controller                  ---- web 层 controller 目录
│   │   │   ├── home.ts
│   │   │   └── user.ts
│   │   ├── middleware (可选)            ---- web 层中间件目录
│   │   │   └── trace.ts
│   │   ├── public (可选)                ---- web 层静态文件目录，可以配置
│   │   ├── view (可选)
│   │   |   └── home.tpl                ---- web 层模板
│   ├── config
│   │   ├── config.default.ts
│   │   ├── config.local.ts
│   │   ├── config.prod.ts
│   │   ├── config.unittest.ts
│   │   └── plugin.ts
│   └── lib                             ---- 业务逻辑层目录
│   │   ├── interface.ts                ---- 接口定义文件
│   │   └── service                     ---- 业务逻辑层，目录根据业务自己定义
│   │       └── user.ts   
│   ├── app.ts                          ---- 应用扩展文件，可选
│   └── agent.ts                        ---- agent 扩展文件，可选
├── test
│   └── app
│       └── controller
│           └── home.test.ts
├── tsconfig.json
└── tslint.json
```

如上，由框架约定的目录，Midway 使用 EggJs 作为 Web 层容器，承载请求控制器和传统 MVC 层的工作，这一块由于受到限制，有着一定的目录约定：

* `app/router.ts` 可选，用于配置 URL 路由规则，具体参见 [Router](https://eggjs.org/zh-cn/basics/router.html)。
* `app/controller/**` 用于解析用户的输入，处理后返回相应的结果，具体参见 [Controller](hController)。
* `app/middleware/**` 可选，用于编写中间件，具体参见 [Middleware](https://eggjs.org/zh-cn/basics/middleware.html)。
* `app/extend/**` 可选，用于框架的扩展，具体参见[框架扩展](https://eggjs.org/zh-cn/basics/extend.html)。
* `config/config.{env}.ts` 用于编写配置文件，具体参见[配置](https://eggjs.org/zh-cn/basics/config.html)。
* `config/plugin.ts` 用于配置需要加载的插件，具体参见[插件](https://eggjs.org/zh-cn/basics/plugin.html)。
* `test/**` 用于单元测试，具体参见[单元测试](https://eggjs.org/zh-cn/core/unittest.html)。
* `app.ts` 和 `agent.ts` 用于自定义启动时的初始化工作，可选，具体参见[启动自定义](https://eggjs.org/zh-cn/basics/app-start.html)。关于`agent.js`的作用参见[Agent机制](https://eggjs.org/zh-cn/core/cluster-and-ipc.html#agent-%E6%9C%BA%E5%88%B6)。

而其他由于 Egg 插件的限制，可能有些目录也会有相应的约定，比如：

* `app/public/**` 用于放置静态资源，可选，具体参见内置插件 [egg-static](https://github.com/eggjs/egg-static)。
* `app/view/**` 用于放置模板文件，可选，由模板插件约定，具体参见[模板渲染](https://eggjs.org/zh-cn/core/view.html)。

而除了 app 目录以外的其他目录，在 midway 体系下并没有严格的规定，而是按照逻辑分层，比如按照传统的 `web, biz, manager, dao` 等进行分层进行创建目录就非常不错。

::: tip
由于 Midway 采用了自动扫描装配，依赖注入等特性，无需在特定的目录下受到限制，使得在全栈应用开发的时候，保持了不错的开发体验。
:::

## 路由和控制器

midway 使用 koa-router 作为路由的承载着，同时在 ts 的语法上做了一些简化，我们将路由和控制器放在了一起，使用装饰器来标注路由。

### 路由装饰器

在新的 ts 体系中，我们的控制器目录为 `app/controller` ，我们在其中编写 `*.ts` 文件。例如下面的 `userController.ts` ，我们提供了一个获取用户的接口。

```typescript
@provide()
@controller('/user')
export class UserController {

  @inject('userService')
  service: IUserService;

  @get('/:id')
  async getUser(ctx): Promise<void> {
    const id: number = ctx.params.id;
    const user: IUserResult = await this.service.getUser({id});
    ctx.body = {success: true, message: 'OK', data: user};
  }
}

```

我们创建了 `@controller` 装饰器用来定义这个类为 Controller，同时，提供了方法装饰器用于标注请求的类型。

:::tip 小贴士
便于大家理解，`@controller` 的参数为字符串 pattern，我们会将这个值传入 `router.prefix(prefix)` 的参数中。
:::

midway 针对 web 请求，提供了和 koa-router 对应的方法装饰器，列表如下。

* @get
* @post
* @del
* @put
* @patch
* @options
* @head
* @all

这几个装饰器用于修饰不同的异步方法，同时对应到了 koa-router 的相应的方法。和原有提供的控制器一样，每个控制器都为异步方法，参数为 koa 上下文。

```typescript
@get('/:id')
async getUser(ctx, next): Promise<void> {
    // TODO ctx...
}
```


### 路由绑定

在以往框架的写法中，提供的 `app/router` 文件，虽然可以直接使用，但是由于控制器被 IoC 管控的关系，会有一些区别。

和以往的写法不同的是，**我们需要从容器中拿到对应的控制器实例，并绑定到路由的 pattern 上**。

假如我们有一个控制器，同时没有提供 `@controller` 装饰器，表明他不是一个控制器，在应用初始化时不会自动绑定到某个路由上，但是由于有 `@provide` 装饰器，他会被 IoC 容器所加载。

```typescript
// app/controller/api.ts

@provide()
export class BaseApi {
  async index(ctx) {
    ctx.body = 'index';
  }
}

```

假如我们希望这个控制器可以被外部的路由使用。

```typescript
// app/router.ts

module.exports = function(app) {
  app.get('/api/index', app.generateController('baseApi.index'));
};

```

midway 扩展了一个 `app.generateController` 的方法来简化绑定的这个步骤，参数为 `ClassName.methodName` 的字符串形式。

### 路由优先级

在单页应用下，经常会出现 `/*` 这种路由，在原本的路由文件中，我们可以通过调整代码的顺序，来使的路由的匹配顺序发生变化。而由于使用了装饰器的关系，在新题的体系无法控制文件扫描和加载顺序，这就使得路由匹配的顺序不可控。

midway 提供了 `@priority(priority: number)` 装饰器，用于修饰 class，定义路由的优先级，默认的路由优先级为 `0`，可以设置负数让优先级降低。

```ts
@provide()
@priority(-1)
@controller('/')
export class HomeController {

  @get('/hello')
  async index(ctx) {
    ctx.body = 'hello';
  }

  @get('/*')
  async all(ctx) {
    ctx.body = 'world';
  }
}

```


## 框架增强注入

midway 默认使用 [injection](http://web.npm.alibaba-inc.com/package/injection) 这个包来做依赖注入，虽然 `@inject` 装饰器能满足大多数业务的需求，但是对于框架来说，还有需要需要扩展和使用的地方，比如插件，配置等等。

### 注入插件

midway 除了支持 eggjs 原本的 app.xx 的插件用法，同时，也可以通过 `@plugin` 装饰器来注入插件。

比如我们提供了一个名字叫 `plugin2` 的插件，就可以通过属性注入的方式来修饰插件。

:::warning 注意
由于在 midway 内部插件未放在 applicationContext 中，所以不能使用 @inject 来注入
:::

```typescript
@provide()
export class BaseService {

  @plugin('plugin2')
  plugin;

}

```

这个时候我们就需要拿到插件的名字。

::: tip
插件，在 midway 中为单例，不可配置。
:::

### 查找插件名

这个插件的名字和普通的插件名字，他是根据插件代码中的返回而定的。

midway 会将挂载到 app 上的属性名作为基础 key。

```js
module.exports = (app) => {
  // egg 插件经常这么做
  app.plugin1 = xxxx;
}
```

那么 plugin1 就是插件key，midway 会在给 app 赋值时自动将返回的对象挂载到插件上下文中，供 `@plugin` 装饰器调用。


### 注入配置

在 midway 中不同环境的 config 都会挂载到 app.config 中，但是不是所有的业务逻辑都会依赖 app 对象，所以我们构造了 `@config` 装饰器来获取配置对象。

假如 `config.default.ts` 中有一些代码。

```typescript
export const hello = 1;
```

```typescript
@provide()
export class BaseService {

  @config('hello')
  config;   // 1

}
```

通过这样，我们可以把 config 中的值直接注入到业务逻辑中。

### 注册定时任务

midawy 的定时任务是基于 [egg 定时任务](https://eggjs.org/zh-cn/basics/schedule.html)提供了更多 typescript 以及装饰器方面的支持。将定时任务都统一存放在 lib/schedule 目录下，每一个文件都是一个独立的定时任务，可以配置定时任务的属性和要执行的方法。例如：

```typescript
// src/lib/schedule/hello.ts
'use strict';

import { schedule } from 'midway';

@schedule({
  interval: 2333, // 2.333s 间隔
  type: 'worker', // 指定某一个 worker 执行
})
export default class HelloCron {
  // 定时执行的具体任务
  async exec(ctx) {
    ctx.logger.info(process.pid, 'hello');
  }
}
```

### 注入日志对象

在原有逻辑中，日志对象也都挂载在 app.loggers 中，通过在 config 中配置的 key 来生成不同的日志实例对象，比如插件的日志，链路的日志等。

比如自定义一个日志，这个时候，日志的 key 则为 `customLogger` 。

```typescript
module.exports = appInfo => {
  return {
    customLogger: {
      xxLogger: {
        file: path.join(appInfo.root, 'logs/xx.log'),
      },
    },
  };
};
```

这个时候可以用 `@logger` 来获取日志实例。

```typescript
@provide()
export class BaseService {

  @logger('customLogger')
  logger;

}

```

### 请求作用域中的日志

midway 在新版本中默认对所有对象开启了请求作用域，处于该作用域下的对象，都会包含一个默认的日志对象。

::: tip
该 logger 对象是在请求链路开始就埋入到 IoC 容器中，所以可以通过 @inject 可以获取该对象，  key 就为 logger，如果和属性同名则可以不填。
:::


```typescript
@provide()
export class BaseService {

  @inject()
  logger;

  // 也可以直接传入 key
  // @inject('logger')
  // logger;
  

}
```

## 应用测试

经过大量的实践，我们沉淀出了一套标准的测试工具集。

* 测试工具包 [midway-bin](https://www.npmjs.com/package/midway-bin)
* 测试框架 mocha
* 测试断言库 assert/chai
* 测试模拟 [midway-mock](https://www.npmjs.com/package/midway-mock)


### 测试目录结构

我们约定 `test` 目录为存放所有测试脚本的目录，测试所使用到的 `fixtures` 和相关辅助脚本都应该放在此目录下。

测试脚本文件统一按 `${filename}.test.ts` 命名，必须以 `.test.ts` 作为文件后缀。

一个应用的测试目录示例：

```plain
test
├── controller
│   └── home.test.ts
├── hello.test.ts
└── service
    └── user.test.ts

```


### 测试命令

在脚手架中，我们已经将常见的命令进行内置，可能略微有些不同，大致代码如下。

```json
{
  "scripts": {
    "test": "midway-bin test --ts",
    "cov": "midway-bin cov --ts",
  }
}
```

<span data-type="color" style="color:rgb(88, 88, 88)"><span data-type="background" style="background-color:rgb(255, 255, 255)">然后就可以按标准的 </span></span>`npm test`<span data-type="color" style="color:rgb(88, 88, 88)"><span data-type="background" style="background-color:rgb(255, 255, 255)"> </span></span>来运行测试了。

```bash
npm test

> unittest-example@ test /Users/harry/midwayj/examples/unittest
> midway-bin test --ts

  test/hello.test.ts
    ✓ should work

  1 passing (10ms)

```


### 开始测试

在测试运行之前，我们首先要创建应用的一个 app 实例， 通过它来访问需要被测试的 Controller、Middleware、Service 等应用层代码。

通过 `midway-mock`，结合 Mocha 的 `before` 钩子就可以便捷地创建出一个 app 实例。

```typescript
// test/controller/home.test.js
const assert = require('assert');
import { mm } from 'midway-mock';

describe('test/controller/home.test.ts', () => {
  let app;
  before(() => {
    // 创建当前应用的 app 实例
    app = mm.app();
    // 等待 app 启动成功，才能执行测试用例
    return app.ready();
  });
});
```


这样我们就拿到了一个 app 的引用，接下来所有测试用例都会基于这个 app 进行。 更多关于创建 app 的信息请查看 [mm.app(options)](https://github.com/eggjs/egg-mock#options) 文档。

每一个测试文件都需要这样创建一个 app 实例非常冗余，因此 `midway-mock` 提供了一个 bootstrap 文件，可以直接从它上面拿到我们所常用的实例：

```typescript
// test/controller/home.test.ts
import { app, mock, assert } from 'midway-mock/bootstrap';

describe('test/controller/home.test.ts', () => {
  // test cases
});

```

### Controller 测试

我们可以通过 app.httpRequest() [SuperTest](https://github.com/visionmedia/supertest) 发起一个真实的 HTTP 请求来进行测试。app.httpRequest() 是 midway-mock 封装的 SuperTest 请求实例。

例如我们要给 `app/controller/home.ts`：

```typescript
import { controller, get, provide } from 'midway';

@provide()
@controller('/')
export class HomeController {
  @get('/')
  async index(ctx) {
    ctx.body = `Welcome to midwayjs!`;
  }
}
```

写一个完整的单元测试，它的测试代码 `test/controller/home.test.ts` 如下：

```typescript
import { app, assert } from 'midway-mock/bootstrap';

describe('test/controller/home.test.ts', () => {
  it('should GET /', () => {
    // 对 app 发起 `GET /` 请求
    return app
      .httpRequest()
      .get('/')
      .expect('Welcome to midwayjs!') // 期望 body 是 hello world
      .expect(200); // 期望返回 status 200
  });
});
```

通过基于 SuperTest 的 app.httpRequest() 可以轻松发起 GET、POST、PUT 等 HTTP 请求，并且它有非常丰富的请求数据构造接口，请查看 [SuperTest](https://github.com/visionmedia/supertest#getting-started) 文档。

### Service 测试

由于 midway 提倡使用 IoC 的方式来定义 service，所以编码与测试都与 eggjs 有明显的区别。

例如 `src/lib/service/user.ts`:

```typescript
import { provide } from 'midway';
import { IUserService, IUserOptions, IUserResult } from '../../interface';

// 装载 service 到 IoC 容器
@provide('userService')
export class UserService implements IUserService {
  async getUser(options: IUserOptions): Promise<IUserResult> {
    return new Promise<IUserResult>((resolve) => {
      // 10ms 之后返回用户数据
      setTimeout(() => {
        resolve({
          id: options.id,
          username: 'mockedName',
          phone: '12345678901',
          email: 'xxx.xxx@xxx.com',
        });
      }, 10);
    });
  }
}

```

编写单元测试 `test/service/user.test.ts`：

```typescript
import { app, assert } from 'midway-mock/bootstrap';
import { IUserService } from '../../src/interface';

describe('test/service/user.test.ts', () => {
  it('#getUser', async () => {
    // 取出 userService
    const user = await app.applicationContext.getAsync<IUserService>('userService');
    const data = await user.getUser({ id: 1 });
    assert(data.id === 1);
    assert(data.username === 'mockedName');
  });
});

```

app.applicationContext 是 IoC 容器的应用上下文, 通过它可以异步取出注入的 service，并使用 service 进行测试。完整 demo 可以参见 [midway-test-demo](https://github.com/Lellansin/midway-test-demo)。
