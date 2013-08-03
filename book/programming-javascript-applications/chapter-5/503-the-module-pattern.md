# [译] [PJA] [503] 模块模式

> * Original: [The Module Pattern - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#the_module_pattern)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## The Module Pattern

## 模块模式

Modules in the browser use a wrapping function to encapsulate private data in a closure (for example, with an IIFE, see the section an Immediately Invoked Function Expressions). Without the encapsulated function scope provided by the IIFE, other scripts could try to use the same variable and function names, which could cause a lot of unexpected behavior.

在浏览器中，模块使用一个包裹函数来把私有数据封装到一个闭包中（比如说，通过一个 IIFE，立即调用的函数表达式）。如果没有这个由 IIFE 提供的封闭函数作用域，那当如果其它脚本可能会尝试同样的变量或函数名，这将可能导致很多意想不到的问题。

Most libraries, such as jQuery and Underscore, are encapsulated in modules.

大多数类库，比如 jQuery 或 Underscore，都封装在模块中。

The module pattern encapsulates module contents in an immediately invoked function expression (IIFE), and exposes a public interface by assignment. Douglas Crockford gave the module pattern its name, and Eric Miraglia popularized it in a [well-known blog post on the YUI Blog][10].

这种模块模式将模块内容封闭在一个 IIFE 中，然后通过赋值向外暴露接口。Douglas Crockford 提出了“模块模式”这个命名，而 Eric Miraglia 通过 [YUI 博客上的一篇著名文章][10] 将它推广开来。

The original module pattern assigns the result of the IIFE to a predefined namespace variable:

最初的模块模式是将 IIFE 运行的结果赋值给一个预定义的命名空间变量：

```js
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
```

The problem with this pattern is that you have no choice but to expose at least one global variable for each module. If you're building an application with a lot of modules, that is not a good option. Instead, it's possible to pass in an existing variable to extend with your new module.

这种模式的问题是，你除了为每个模块暴露至少一个全局变量以外别无选择。如果你在构建一个拥有大量模块的应用程序，这并不是一个很好的选择。与此相对地，可以传递一个已经存在的、以供扩展的变量，用你的新模块来扩展它。

Here that variable is called `exports`, for compatibility with CommonJS (see the section on Node Modules for an explanation of CommonJS). If `exports` does not exist, you can fall back on window:

为了与 CommonJS 保持兼容（参见后续的 Node 模块章节，作为 CommonJS 的一个解释），我们在这里将这个变量命名为 `exports`。如果 `exports` 不存在，那么你可以回退至 window：

```js
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
```

A common mistake is to pass in a specific application namespace inside your module's source file (instead of using a globally defined exports). Normally, that will not harm anything. However, if you wish to reuse the module in another application, you'll have to modify the source of the module in order to attach it to the correct namespace.

一个常见的错误是把一个特定的应用程序命名空间传进了你的模块的源文件中（而不是使用一个全局定义的 exports）。一般来说，这不会有什么坏处。但是，如果你希望在其它应用程序中重用这个模块，你就不得不修改模块的源码，以便将它附着到正确的命名空间上。

Instead, you can pass your application object in as exports. It's common in client side code to have a build step that wraps all of your modules together in a single outer function. If you pass your application object into that wrapper function as a parameter called `exports`, you're in business.

取而代之，你可以把你的应用程序对象传进去作为 exports。在客户端采用一个构建步骤是很常见的，这个步骤可以把你的所有模块包裹到一个外层函数中。如果你把你的应用程序对象作为一个名为 `exports` 的参数传进这个包裹函数中，你就算走对路了。

```js
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
```

An upside to this version of the module pattern is that the code you write with it can be easily run and tested in Node. From this point on, when the module pattern gets mentioned, this is the version that should spring to mind. It's older ancestor is obsolete.

这个版本的模块模式有一个好处是，你用这种模式写的代码可以很容易地在 Node 中运行和测试。从此开始，每当提到模块模式时，你就应该想到这个版本。比它更早的版本已经过时了。

[10]: http://yuiblog.com/blog/2007/06/12/module-pattern/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
