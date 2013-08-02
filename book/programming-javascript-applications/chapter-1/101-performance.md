# [译] [PJA] [101] 性能

> * Original: [Performance - Chapter 1. The JavaScript Revolution - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch01.html#performance)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Performance

Just in Time compiling – In modern browsers, most JavaScript is compiled, highly optimized and executed like native code, so run-time performance is close to software written in C or C\+\+. Of course, there is still the overhead of garbage collection and dynamic binding, so it is possible to do certain things faster -- however, the difference is generally not worth sweating over until you’ve optimized everything else. With [Node.js][6] (a high-performance, evented, server-side JavaScript environment built on Google's highly optimized V8 JavaScript engine), JavaScript apps are event-driven and non-blocking, which generally more than makes up the code execution difference between JavaScript and less dynamic languages.

[6]: http://nodejs.com/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
