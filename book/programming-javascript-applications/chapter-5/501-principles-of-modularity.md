# [译] [PJA] [501] 模块化的原则

> * Original: [Principles of Modularity - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#principles_of_modularity)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Principles of Modularity

## 模块化的原则

You can think of modules as small, independent applications that are fully functional and fully testable in their own right. Keep them as small and simple as possible to do the job that they are designed to do.

我们可以把模块定义为小巧而独立的应用程序，每个模块都独立具备完整的功能性和完整的可测试性。应该让它们尽可能地保持小巧和简单，从而更好地完成它们的本职工作。

Modules should be:

模块应该具备：

* Specialized: Each module should have a very specific function. The module's parts should be integral to solving the single problem that the module exists to solve. The public API should be simple and clean.

* **针对性**：每个模块都应该具备非常专精的功能。模块的各个部分应该紧密团结，来完成模块的份内工作。对外公开的 API 应该干净利落。

* Independent: Modules should know as little as possible about other modules. Instead of calling other modules directly, they should communicate between mediators, such as a central event handling system or command object.

* **独立性**：模块对其它模块的了解应该越少越好。模块不应直接调用其它模块，它们应该通过中介者进行通信，比如一个中央事件处理系统或命令对象。

* Decomposable: It should be fairly simple to test and use modules in isolation from other modules. It's common to compare them to components in an entertainment system. You could have a disc player, radio receiver, TV, amplifier and speakers, all of which can operate independently of each other. If you remove the disc player, the rest of the components continue to function properly.

* **可分解性**：以相互隔绝的方式对模块进行测试和使用应该是十分简单的。模块通常可以比喻为一套组合音响中的各个部件。你家的组合音响中可能包含了影碟机、收音机、电视机、功放和音箱，所有这些部件都可以独立工作。即使你移走了影碟机，剩下的部件也可以继续正常运作。

* Recomposable: It should be possible to fit various modules together in different ways, in order to build a different version of the same software, or an entirely different application.

* **可重组性**：应该可以以不同的方式将各种模块组装到一起，以便构建某个软件的一个新版本，或者构建一个完全不同的应用程序。

* Substitutable: It should be possible to completely substitute one module with another, as long is it supplies the same interface. The rest of the application should not be adversely impacted by the change. The substitute module doesn't have to perform the same function. For example, you might want to substitute a data module that uses REST endpoints as its data source with one that uses a local storage database as its data source.

* **可替换性**：应该可以用另一个模块完全替代眼前的这个，只要它们提供了相同的接口。应用程序中的其余部分不应该因为这种变化而受到负面影响。同时，这个替补模块并不需要以相同的方式运作。举个例子，系统中有一个由 REST 端点作为数据源的数据模块，你可能想把它替换为一个使用本地存储数据库作为数据源的模块。

> `[$]`

> _The Open Closed Principle_ states that a module interface should be open to extension but closed to modification. Changing an interface that a lot of software relies on can be a daunting task. It's best if you can avoid making changes to an existing interface once it has been established. However, software should evolve, and as it does, it should be easy to extend existing interfaces with new functionality.

> **开闭原则** 的意思是，一个模块的接口应该对于扩展是开放的，而对于修改是关闭的（禁止修改）。修改一个很多软件都依赖的接口往往是一项令人望而却步的任务。避免对一个既有接口做修改，是上上策。不过，软件产品应该（也一直在）不断进化，因此，为既有接口扩展新功能不应该成为一件难事。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/31)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
