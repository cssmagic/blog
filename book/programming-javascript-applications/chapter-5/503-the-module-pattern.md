# [译] [PJA] [503] 模块模式

> * Original: [The Module Pattern - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#the_module_pattern)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## The Module Pattern

## 模块模式

Modules in the browser use a wrapping function to encapsulate private data in a closure (for example, with an IIFE, see the section an Immediately Invoked Function Expressions). Without the encapsulated function scope provided by the IIFE, other scripts could try to use the same variable and function names, which could cause a lot of unexpected behavior.

浏览器中的模块通常使用一个包裹函数来把私有数据封装到一个闭包中（比如我们可以通过 IIFE 来实现，参见本书的《立即调用的函数表达式》章节）。如果没有 IIFE 提供的这个封闭的函数级作用域，一旦其它脚本尝试使用同名的变量或函数，将会导致很多意想不到的情况。

Most libraries, such as jQuery and Underscore, are encapsulated in modules.

大多数类库，比如 jQuery 或 Underscore，都封装在这样的模块中。

The module pattern encapsulates module contents in an immediately invoked function expression (IIFE), and exposes a public interface by assignment. Douglas Crockford gave the module pattern its name, and Eric Miraglia popularized it in a [well-known blog post on the YUI Blog][10].

这种“模块模式”将模块内容封闭在一个 IIFE 中，然后通过赋值向外暴露接口。Douglas Crockford 提出了“模块模式”这个名词，而 Eric Miraglia 通过 [YUI 博客上的一篇著名文章][10] 将它推广开来。

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

这种模式的问题是，你不得不为每个模块暴露至少一个全局变量，别无它法。如果你正在构建一个拥有大量模块的应用程序，这显然不是一个好办法。实际上，我们可以传一个已经存在的、以供扩展的变量（命名空间）进去，用你的新模块来扩展它。

Here that variable is called `exports`, for compatibility with CommonJS (see the section on Node Modules for an explanation of CommonJS). If `exports` does not exist, you can fall back on window:

为了与 CommonJS 保持兼容（后面的[《CommonJS 模块规范》](https://github.com/cssmagic/blog/issues/36)章节会详细解释 CommonJS），我们将这个变量命名为 `exports`。如果 `exports` 不存在，那么至少你还可以回退至 `window` 对象：（译注：扩展 `window` 对象即相当于暴露新的全局变量。不过不用担心，这只是以防万一的退路。）

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

一个常见的错误是把一个特定的应用程序命名空间传进了模块内部的源码中（而不是使用一个全局定义的 `exports`）。一般来说，这不会导致什么严重后果。但是，如果你希望在其它应用程序中重用这个模块，你就不得不修改模块的源码，以便将它绑定到正确的命名空间上。

Instead, you can pass your application object in as exports. It's common in client side code to have a build step that wraps all of your modules together in a single outer function. If you pass your application object into that wrapper function as a parameter called `exports`, you're in business.

正确的方案是这样的，你可以把你的应用程序对象（命名空间）传进去作为 `exports`。在客户端采用一个构建步骤已经很常见了，这个步骤可以把你的所有模块包裹到一个外层函数中。如果你把你的应用程序对象（命名空间）作为一个名为 `exports` 的参数传进这个外层包裹函数中，你就算是走上正轨了。

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

最后这个版本的模块模式有一个好处是，你用它编写的代码可以很容易地在 Node 中运行和测试。从现在开始，每当提到“模块模式”时，你脑袋里面就应该立马蹦出这个版本。比它更早的那些版本已经过时了。

[10]: http://yuiblog.com/blog/2007/06/12/module-pattern/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/34)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
