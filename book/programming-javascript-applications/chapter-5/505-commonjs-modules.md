# [译] [PJA] [505] CommonJS 模块规范

> * Original: [CommonJS Modules - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#CommonJS_modules)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## CommonJS Modules

## CommonJS 模块规范

_CommonJS_ is a set of standards for JavaScript environments that attempts to make JavaScript engine implementations more compatible with each other. CommonJS modules specify an API which modules use to declare dependencies. CommonJS module implementations are responsible for reading the modules and resolving those dependencies.

**CommonJS** 是为 JavaScript 环境制定的一系列标准的集合，它试图令各种 JavaScript 引擎之间达到更好的兼容性。CommonJS 模块指定了一个 API，模块用它来声明依赖性。CommonJS 的模块机制主要负责读取模块，并解析它们的依赖关系。

Before Node.JS, there were several other attempts to run JavaScript on the server side, as far back as the late 1990's. Both Netscape and Microsoft allowed JavaScript compatible scripting in their server environments. However, few people used those capabilities. The first server side JavaScript solution to gain any real traction was Rhino, but it was too slow and cumbersome to build web scale applications on top of.

在 Node.js 之前，就有一些想把 JavaScript 运行在服务器端的尝试，这些尝试可以追溯到上世纪 90 年代。当年网景和微软都允许在他们的服务器环境中运行 JavaScript 或兼容脚本（译注：因为微软的 JavaScript 实现被命名为“JScript”）。不过很可惜，几乎没有人这样用过。第一个受到广泛关注的服务器端 JavaScript 解决方案是 Rhino，但它太慢而且笨重，很难基于它来构建网站级别的应用程序。

By the time Node.JS arrived on the scene, there were several different server side environments for JavaScript, that all started out using different conventions for dealing with issues such as module loading. CommonJS was created to solve that problem.

等到 Node.js 登场的时候，服务器端的 JavaScript 环境就已经有好几种了，而且它们在模块加载问题上又分别使用了不同的约定。CommonJS 就是为了统一这个乱世而生的。

The CommonJS module system has a much simpler syntax than either the module pattern or AMD. In CommonJS, the file is the module. There is no need for a function wrapper to contain the scope, because each file is given its own scope. Modules declare dependencies with a synchronous `require()` function. That means that execution is blocked while the required module is being resolved, so it's safe to start using the module immediately after you require it.

CommonJS 的模块系统拥有一个比模块模式或 AMD 更简单的语法。在 CommonJS 中，文件就是模块。不需要用一个包裹函数来创建作用域，因为每个文件本身就已经划清了它自己的作用域。模块使用一个同步的 `require()` 函数来声明依赖关系。这意味着，当那个被 require 的模块正在解析时，代码执行是处于阻塞状态的，所以你可以放心地在 require 它之后就立即开始使用它。

First, assign to keys on the free variable `exports` to declare your module's public API:

首先，通过对自由变量 `exports` 的键进行赋值，可以声明模块的公开 API：

```js
'use strict';
var foo = function () {
  return true;
};

exports.foo = foo;
```

Then use `require()` to import your module and assign it to a local variable. You can specify the name of a module in the list of installed Node modules, or specify a path to the module using relative paths.

此外，使用 `require()` 可以引入其它模块，将其赋值给一个本地变量。你可以指定一个已经安装的 Node 模块的名字，也可以通过相对路径来指定一个模块的路径。

For example, if you want to use the Flatiron HTTP Module, you can `require()` by name. (From the [Flatiron.js][12] docs):

举个例子，如果你想使用 Flatiron 的 HTTP 模块，你可以通过名字 `require()` 它。（以下摘自 [Flatiron.js][12] 的文档）：

```js
var flatiron = require('flatiron'),
  app = flatiron.app;

app.use(flatiron.plugins.http, {
  // HTTP options
  // HTTP 选项写在这里
});

//
// app.router is now available. app[HTTP-VERB] is also available
// as a shortcut for creating routes
// app.router 现在可用了。
// app[HTTP-VERB] 作为一个创建路由的快捷方式也可用了。
//
app.router.get('http://chimera.labs.oreilly.com/version', function () {
  this.res.writeHead(200, { 'Content-Type': 'text/plain' })
  this.res.end('flatiron ' %2B flatiron.version);
});

app.start(8080);
```

Or specify a relative path:

或指定一个相对路径：

```js
'use strict';
var mod = require('./ch05-modules.js'),
  result = (mod.foo() === true) ? 'Pass:' : 'Fail:';

console.log(result, '.foo() should return true.');
```

You can use `console.log()` to simulate a unit testing framework, but there are several better alternatives for Node. See the Chapter "RESTful APIs with Node" for an introduction to unit testing with NodeUnit.

你可以使用 `console.log()` 来模拟一个单元测试框架，但对 Node 来说还有一些更好的替代方案。参见《基于 Node 实现 REST 风格的 API》章节来了解如何使用 NodeUnit 来进行单元测试。

[12]: http://flatironjs.org/#routing

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
