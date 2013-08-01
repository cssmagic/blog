# [译] [PJA] [507] Harmony 的模块机制

> * Original: [Harmony Modules](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#ch9phz70i00005ig1kqp3ed7t)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Harmony Modules

None of the module systems discussed so far are included in the official ECMAScript specifications. In the future, a module system will be built-in to all ECMA-compliant JavaScript engines.

The Harmony module specification hasn't stabilized yet, but when it does, it should be possible to write harmony modules and compile using build tools to fall back on the module pattern, AMD, and CommonJS where required.

Harmony module build tools are already beginning to appear, but since the specification is likely to change, you should probably wait a bit longer before you start to rely on them for your production applications.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
