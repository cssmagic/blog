# [译] [PJA] [507] Harmony 的模块机制

> * Original: [Harmony Modules - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#ch9phz70i00005ig1kqp3ed7t)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Harmony Modules

## Harmony 的模块机制

None of the module systems discussed so far are included in the official ECMAScript specifications. In the future, a module system will be built-in to all ECMA-compliant JavaScript engines.

目前为止我们所讨论到的模块机制都并没有收录进官方的 ECMAScript 规则中。但在未来，将会有一个模块系统内建到所有符合 ECMA 规范的 js 引擎。

The Harmony module specification hasn't stabilized yet, but when it does, it should be possible to write harmony modules and compile using build tools to fall back on the module pattern, AMD, and CommonJS where required.

Harmony 的模块规范现在还没有稳定，但一旦稳定下来，我们将有可能编写 Harmony 模块，并使用构建工具来进行编译，在必要的场合下还可以降级到模块模式、AMD 和 CommonJS 的模块机制。

Harmony module build tools are already beginning to appear, but since the specification is likely to change, you should probably wait a bit longer before you start to rely on them for your production applications.

Harmony 模块构建工具已经开始出现了，但由于规则还有可能变动，在开始依赖它们投入生产之前，你可能还需要再等一等。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
