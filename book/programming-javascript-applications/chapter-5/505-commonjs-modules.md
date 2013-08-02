# [译] [PJA] [505] CommonJS 模块规范

> * Original: [CommonJS Modules - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#commonjs_modules)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## CommonJS Modules

_CommonJS_ is a set of standards for JavaScript environments that attempts to make JavaScript engine implementations more compatible with each other. CommonJS modules specify an API which modules use to declare dependencies. CommonJS module implementations are responsible for reading the modules and resolving those dependencies.

Before Node.JS, there were several other attempts to run JavaScript on the server side, as far back as the late 1990's. Both Netscape and Microsoft allowed JavaScript compatible scripting in their server environments. However, few people used those capabilities. The first server side JavaScript solution to gain any real traction was Rhino, but it was too slow and cumbersome to build web scale applications on top of.

By the time Node.JS arrived on the scene, there were several different server side environments for JavaScript, that all started out using different conventions for dealing with issues such as module loading. CommonJS was created to solve that problem.

The CommonJS module system has a much simpler syntax than either the module pattern or AMD. In CommonJS, the file is the module. There is no need for a function wrapper to contain the scope, because each file is given its own scope. Modules declare dependencies with a synchronous `require()` function. That means that execution is blocked while the required module is being resolved, so it's safe to start using the module immediately after you require it.

First, assign to keys on the free variable `exports` to declare your module's public API:

    'use strict';
    var foo = function () {
      return true;
    };

    exports.foo = foo;

Then use `require()` to import your module and assign it to a local variable. You can specify the name of a module in the list of installed Node modules, or specify a path to the module using relative paths.

For example, if you want to use the Flatiron HTTP Module, you can `require()` by name. (From the [Flatiron.js][12] docs):

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
