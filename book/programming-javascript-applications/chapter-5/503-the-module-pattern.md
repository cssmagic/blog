# [译] [PJA] [503] 模块的模式

> * Original: [The Module Pattern - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#the_module_pattern)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## The Module Pattern

Modules in the browser use a wrapping function to encapsulate private data in a closure (for example, with an IIFE, see the section an Immediately Invoked Function Expressions). Without the encapsulated function scope provided by the IIFE, other scripts could try to use the same variable and function names, which could cause a lot of unexpected behavior.

Most libraries, such as jQuery and Underscore, are encapsulated in modules.

The module pattern encapsulates module contents in an immediately invoked function expression (IIFE), and exposes a public interface by assignment. Douglas Crockford gave the module pattern its name, and Eric Miraglia popularized it in a [well-known blog post on the YUI Blog][10].

The original module pattern assigns the result of the IIFE to a predefined namespace variable:

    var myModule = (function () {
      return {
        hello: function hello() {
          return 'Hello, world!';
        }
      };
    }());

    test('Module pattern', function () {
      equal(myModule.hello(),
        'Hello, world!',
        'Module works.');
    });

The problem with this pattern is that you have no choice but to expose at least one global variable for each module. If you're building an application with a lot of modules, that is not a good option. Instead, it's possible to pass in an existing variable to extend with your new module.

Here that variable is called `exports`, for compatibility with CommonJS (see the section on Node Modules for an explanation of CommonJS). If `exports` does not exist, you can fall back on window:

    (function (exports) {
      var api = {
          moduleExists: function test() {
            return true;
          }
        };
      $.extend(exports, api);
    }((typeof exports === 'undefined') ?
        window : exports));

    test('Pass in exports.', function () {
      ok(moduleExists(),
        'The module exists.');
    });

A common mistake is to pass in a specific application namespace inside your module's source file (instead of using a globally defined exports). Normally, that will not harm anything. However, if you wish to reuse the module in another application, you'll have to modify the source of the module in order to attach it to the correct namespace.

Instead, you can pass your application object in as exports. It's common in client side code to have a build step that wraps all of your modules together in a single outer function. If you pass your application object into that wrapper function as a parameter called `exports`, you're in business.

    var app = {};

    (function (exports) {

      (function (exports) {
        var api = {
            moduleExists: function test() {
              return true;
            }
          };
        $.extend(exports, api);
      }((typeof exports === 'undefined') ?
          window : exports));

    }(app));

    test('Pass app as exports.', function () {
      ok(app.moduleExists(),
        'The module exists.');
    });

An upside to this version of the module pattern is that the code you write with it can be easily run and tested in Node. From this point on, when the module pattern gets mentioned, this is the version that should spring to mind. It's older ancestor is obsolete.

[10]: http://yuiblog.com/blog/2007/06/12/module-pattern/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
