# PHP 和 JSON 都说 `_.isEmpty()` 是个好方法

## 背景

我们在通过后端 API 获取 JSON 数据时，有时会遇到这个问题：

在需要输出为**对象**的地方，如果没有数据，以前端的习惯来说，我们会期待收到一个**空对象** `{}`；但实际上，此时我们往往会收到一个**空数组** `[]`。

前端在检查和处理此类数据时，会感到稍许不便。如果考虑不周，还会引发错误。

## 原因

在 PHP 中，与 JavaScript **数组**和**对象**对应的概念都是数组；而且 PHP 实际上并不区分**索引数组**和**关联数组**，只是允许开发者通过数组键名的约定来产生这两种数组的效果。也就是说，对于空数组，由于它不包含键名，无法判断它是“索引数组”还是“关联数组”。因此，PHP 的 `json_encode()` 方法在处理空数组时，一律将其编码为 `[]`。

由此可见，要求后端来改变这种情况恐怕不太容易，还是需要前端在处理类似数据的时候做个容错判断（我们常说“发送时要严格、接收时要宽松”嘛）。那么，怎样判断既简单又方便呢？

## 解决方案

这里就要隆重推出 `_.isEmpty()` 啦！请看以下代码：

```js
_.isEmpty()  //true
_.isEmpty(null)  //true
_.isEmpty(undefined)  //true
_.isEmpty('')  //true
_.isEmpty({})  //true
_.isEmpty([])  //true

_.isEmpty('foo')  //false
_.isEmpty([foo, bar])  //false
_.isEmpty({foo: bar})  //false
```

同时我们注意到，即使后端将空数据输出为 `null`，仅用这一个方法也可以一并判断。

## 注意事项

需要注意的是，不能把这货当成 `toBoolean()` 来用。这样用是有坑的：

```js
_.isEmpty(1)  //true
_.isEmpty(0)  //true
_.isEmpty(NaN)  //true
_.isEmpty(true)  //true
_.isEmpty(false)  //true
_.isEmpty(_.isEmpty)  //true
```

所以，只应该用它来判断一个数据集合是否为空。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](https://github.com/cssmagic/blog/issues/8) &nbsp; | &nbsp; [我要捐助](https://github.com/cssmagic/blog/issues/9)

&nbsp;
> * [更多文章](https://github.com/cssmagic/blog/issues)
> * [查看原文与评论](https://github.com/cssmagic/blog/issues/5)
