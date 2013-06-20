# Underscore 模板引擎 API 更新

## 语法

Underscore 的模板引擎 `_.template()` 脱胎于 jQuery 作者的作品 [Micro-Templating](http://ejohn.org/blog/javascript-micro-templating/)。但从 Underscore 1.3.3 开始，这个方法做了较大的调整，在保留旧语法的基础上，还新增支持了一个 `{variable: 'foo'}` 对象作为第三个参数。

一旦传入了这个参数，则模板（第一个参数）中的变量将不再指向待渲染数据（第二个参数）的**属性**，模板中的 `foo` 变量将直接指向待渲染数据（第二个参数）**自身**。

比如，原来的调用方法：

```js
_.template('I love <%= person %>.', {person: 'you'});
```

在新语法下可以写为：

```js
_.template('I love <%= foo.person %>.', {person: 'you'}, {variable: 'foo'});
```

## 比较

看起来似乎变复杂了，但这样做有两个好处：

### 内部实现

旧语法在实现上，需要使用 `with` 声明来实现对数据对象属性的查找。`with` 有哪些问题，这里就不多说了。而新语法由于已经在模板中指定了数据对象自身，则不需要用 `with` 来搜索其属性。据官方文档称，“模板渲染性能得到极大提升”。

### 外部接口

新语法所带来的一个隐性的改良——接口灵活性更高，即待渲染数据（第二个参数）可以不仅是对象，也可以是数组等其它数据类型。仍然以上面的代码为例，用新语法还可以写成这样：

```js
_.template('I love <%= foo %>.', 'you', {variable: 'foo'});
```

这样传入的数据更自由，数组等数据不需要被包装为对象再传入了。


## 使用

在实际应用中，往往存在某个常用的模板需要被多次渲染的情况。此时，为优化性能，我们通常会采用“两步渲染法”——先把模板编译成模板函数备用；按需执行已经编译好的模板函数，把不同的数据渲染为不同的结果——以避免同一模板的重复编译。如下所示：

```js
var fnRender = _.template('I love <%= person %>.');
fnRender({person: 'you'});  //'I love you.'
fnRender({person: 'her'});  //'I love her.'
```

在这种情况下，我们无法使用 `{variable: 'foo'}` 参数。那怎么办呢？

幸好有 `_.templateSettings` 可以进行 `_.template()` 的全局设置：

```js
_.extend(_.templateSettings, {variable: 'foo'});
```

在此之后编译的所有模板函数即工作在新语法之下。需要注意的是，这个设置是全局的，也就是说，当前页面的所有模板和相关功能都需要以新语法来写，并将 `foo` 统一命名。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](https://github.com/cssmagic/blog/issues/8) &nbsp; | &nbsp; [我要捐助](https://github.com/cssmagic/blog/issues/9)

&nbsp;
> * [更多文章](https://github.com/cssmagic/blog/issues)
> * [查看原文与评论](https://github.com/cssmagic/blog/issues/4)
