# [译] [PJA] [505] CommonJS 模块规范

> * Original: [CommonJS Modules - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#commonjs_modules)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## CommonJS Modules

## CommonJS 模块规范

_CommonJS_ is a set of standards for JavaScript environments that attempts to make JavaScript engine implementations more compatible with each other. CommonJS modules specify an API which modules use to declare dependencies. CommonJS module implementations are responsible for reading the modules and resolving those dependencies.

_CommonJS_ 是为 Javascript 环境制定的一系列标准的集合，它试图令 Javascript 引擎相互实现更好的兼容性。CommonJS 模块指定了一个 API，模块用它来声明依赖性。CommonJS 模块的实现对读取模块负责，并解析它们的依赖关系。

Before Node.JS, there were several other attempts to run JavaScript on the server side, as far back as the late 1990's. Both Netscape and Microsoft allowed JavaScript compatible scripting in their server environments. However, few people used those capabilities. The first server side JavaScript solution to gain any real traction was Rhino, but it was too slow and cumbersome to build web scale applications on top of.

在 Node.js 之前，就有一些想把 javascript 运行在服务器端的尝试，这可以追溯到 1990 年。网景和微软都允许 javascript 兼容脚本在他们的服务器环境。不管怎样，确实有一些人曾使用过这些功能。最早的服务器端 javascript 解决方案，获得真正的关注的是 rhino，但它的动作太慢而且笨重，很难基于它来构建网络范围的应用程序。

By the time Node.JS arrived on the scene, there were several different server side environments for JavaScript, that all started out using different conventions for dealing with issues such as module loading. CommonJS was created to solve that problem.

这个时候，Node.js 登场了。但曾有过多种不同的服务器端的 js 环境，它们开始使用不同的约定来处理问题，比如模块的加载。commonjs 就是为了解决这个问题而生的。

The CommonJS module system has a much simpler syntax than either the module pattern or AMD. In CommonJS, the file is the module. There is no need for a function wrapper to contain the scope, because each file is given its own scope. Modules declare dependencies with a synchronous `require()` function. That means that execution is blocked while the required module is being resolved, so it's safe to start using the module immediately after you require it.

commonjs 的模块系统拥有一个比模块模式或 AMD 更简单的语法。在 commonjs 中，文件就是模块。不需要用一个包裹函数来创建作用域，因为每个文件本身就提供它自己的作用域。模块声明依赖关系是使用一个异步的  函数。这意味着执行是被阻塞的，当需要的模块在被解析时，所以在你req它之后可以立即安全地开始使用它。

First, assign to keys on the free variable `exports` to declare your module's public API:

首先，对自由的变量  的键赋值，来声明你的模块的公开的 API：

    'use strict';
    var foo = function () {
      return true;
    };

    exports.foo = foo;

Then use `require()` to import your module and assign it to a local variable. You can specify the name of a module in the list of installed Node modules, or specify a path to the module using relative paths.

然后使用  来导入你的模块，并将其赋值到一个本地变量。你可以指定一个已经安装的 Node 模块的名字，或者通过相对路径来指定一个模块的路径。

For example, if you want to use the Flatiron HTTP Module, you can `require()` by name. (From the [Flatiron.js][12] docs):

举个例子，如果你想使用  模块，你可以通过名字  它。（来自于  的文档）：

    var flatiron = require('flatiron'),
      app = flatiron.app;

    app.use(flatiron.plugins.http, {
      // HTTP options
    });

    //
    // app.router is now available. app[HTTP-VERB] is also available
    // as a shortcut for creating routes
    //
    app.router.get('http://chimera.labs.oreilly.com/version', function () {
      this.res.writeHead(200, { 'Content-Type': 'text/plain' })
      this.res.end('flatiron ' %2B flatiron.version);
    });

    app.start(8080);

Or specify a relative path:

或指定一个相对路径：

    'use strict';
    var mod = require('./ch05-modules.js'),
      result = (mod.foo() === true) ? 'Pass:' : 'Fail:';

    console.log(result, '.foo() should return true.');

You can use `console.log()` to simulate a unit testing framework, but there are several better alternatives for Node. See the Chapter "RESTful APIs with Node" for an introduction to unit testing with NodeUnit.

[12]: http://flatironjs.org/#routing

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
