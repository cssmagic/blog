# [译] [PJA] [501] 模块化的原则

> * Original: [Principles of Modularity - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#principles_of_modularity)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Principles of Modularity

You can think of modules as small, independent applications that are fully functional and fully testable in their own right. Keep them as small and simple as possible to do the job that they are designed to do.

Modules should be:

  * Specialized: Each module should have a very specific function. The module's parts should be integral to solving the single problem that the module exists to solve. The public API should be simple and clean.

  * Independent: Modules should know as little as possible about other modules. Instead of calling other modules directly, they should communicate between mediators, such as a central event handling system or command object.

  * Decomposable: It should be fairly simple to test and use modules in isolation from other modules. It's common to compare them to components in an entertainment system. You could have a disc player, radio receiver, TV, amplifier and speakers, all of which can operate independently of each other. If you remove the disc player, the rest of the components continue to function properly.

  * Recomposable: It should be possible to fit various modules together in different ways, in order to build a different version of the same software, or an entirely different application.

  * Substitutable: It should be possible to completely substitute one module with another, as long is it supplies the same interface. The rest of the application should not be adversely impacted by the change. The substitute module doesn't have to perform the same function. For example, you might want to substitute a data module that uses REST endpoints as its data source with one that uses a local storage database as its data source.

_The Open Closed Principle_ states that a module interface should be open to extension but closed to modification. Changing an interface that a lot of software relies on can be a daunting task. It's best if you can avoid making changes to an existing interface once it has been established. However, software should evolve, and as it does, it should be easy to extend existing interfaces with new functionality.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
