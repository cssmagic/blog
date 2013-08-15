# [译] JavaScript 开发者经常忽略或误用的七个基础知识点

> * Original: [7 JavaScript Basics Many Developers Aren't Using (Properly)](http://tech.pro/tutorial/1453/7-javascript-basics-many-developers-aren-t-using-properly)
> * Translated by: [cssmagic](https://github.com/cssmagic)

![JavaScript](https://f.cloud.github.com/assets/1231359/967278/4822777c-0571-11e3-8241-19d3e4468795.png)

JavaScript, at its base, is a simple language that we continue to evolve with intelligent, flexible patterns. We've used those patterns in JavaScript frameworks which fuel our web applications today. Lost in JavaScript framework usage, which many new developers are thrust right into, are some of the very useful JavaScript techniques that make basic tasks possible. Here are seven of those basics:

JavaScript 本身可以算是一门简单的语言，但我们也不断用智慧和灵活的模式来改进它。昨天我们将这些模式应用到了 JavaScript 框架中，今天这些框架又驱动了我们的 Web 应用程序。很多新手开发者被各种强大的 JavaScript 框架吸引进来，但他们却忽略了框架身后浩如星海的 JavaScript 实用技巧。本文将为你呈献其中七个基础知识点：

## 1. String.prototype.replace: `/g` and `/i` Flags

## 1. 在 `String.prototype.replace` 方法中使用 `/g` 和 `/i` 标志位

One surprise to many JavaScript newbies is that String's `replace` method doesn't [replace all occurrences of the needle](http://davidwalsh.name/javascript-replace) -- just the first occurrence. Of course seasoned JavaScript vets know that a regular expression and the global flag (`/g`) need to be used:

令很多 JavaScript 初学者意外的是，字符串的 `replace` 方法并不会 [替换所有匹配的子串](http://davidwalsh.name/javascript-replace)——而仅仅替换第一次匹配。当然 JavaScript 老手们都知道这里可以使用正则表达式，并且需要加上一个全局标志位（`/g`）：

```js
// Mistake
// 踩到坑了
var str = "David is an Arsenal fan, which means David is great";
str.replace("David", "Darren"); // "Darren is an Arsenal fan, which means David is great"

// Desired
// 符合预期
str.replace(/David/g, "Darren"); // "Darren is an Arsenal fan, which means Darren is great"
```

Another basic logical mistake is not ignoring case when case is not critical to the validation (letters may be uppercase or lowercase), so the `/i` flag is also useful:

另一个基本的逻辑错误就是在大小写不敏感的校验场合（字母可大写可小写）没有忽略大小写，此时 `/i` 标志位就很实用：

```js
str.replace(/david/gi, "Darren"); // "Darren will always be an Arsenal fan, which means Darren will always be great"
```

（译注：上面这段例程我没有看懂用意，可能是注释有误吧……）

Every JavaScript developer has been bitten by each of the flags in the past -- so be sure to use them when when appropriate!

每个 JavaScript 开发者都曾踩过这两个标志位的坑——因此别忘了在适当的时候用上它们！

## 2. Array-Like Objects and Array.prototype.slice

## 2. 类数组对象和 `Array.prototype.slice` 方法

Array's slice method is principally for grabbing segments of an array. What many developers don't know is that slice can be used to covert Array-like objects like arguments, NodeLists, and attributes into true arrays of data:

数组的 `slice` 方法通常用来从一个数组中抽取片断。但很多开发者不了解的是，这个方法还可以用来将“类数组”元素（比如 `arguments` 参数列表、节点列表和属性列表）转换成真正的数组：（译注：DOM 元素的属性列表通过 `attributes` 属性获取，比如 `document.body.attributes`。）

```js
var nodesArr = Array.prototype.slice.call(document.querySelectorAll("div"));
// "true" array of DIVs
// 得到一个由 div 元素组成的“真正的”数组

var argsArr = Array.prototype.slice.call(arguments);
// changes arguments to "true" array
// 把 arguments 转换成一个“真正的”数组
```

You can even clone an array using a simple `slice` call:

你还可以使用一次简单的 `slice` 调用来克隆一个数组：

```js
var clone = myArray.slice(0); // naive clone
                              // 浅克隆
```

（译注：这里的参数 `0` 也可以省略，我估计 `undefined` 被 `slice` 方法自动转换为 `0` 了吧。）

`Array.prototype.slice` is an absolute gem in the world of JavaScript, one that even novice JavaScript developers don't know the full potential of.

`Array.prototype.slice` 绝对是 JavaScript 世界中的一玫珍宝，但 JavaScript 初学者们显然还没有意识到它的全部潜力。

## 3. Array.prototype.sort

## 3. `Array.prototype.sort` 方法

The [Array `sort` method](http://davidwalsh.name/array-sort) is vastly underused and probably a bit more powerful than most developers believe. Many developers would assume sort would do something like this:

[数组的 `sort` 方法](http://davidwalsh.name/array-sort) 远远没有被充分利用，而且可能比开发者们想像的更加强大。很多开发者可能觉得 `sort` 方法可以用来做这种事情：

```js
[1, 3, 9, 2].sort();
    // Returns: [1, 2, 3, 9]
    // 返回 [1, 2, 3, 9]
```

...which is true, but sort has more powerful uses, like this:

……这没错，但它还有更强大的用法，比如这样：

```js
[
    { name: "Robin Van PurseStrings", age: 30 },
    { name: "Theo Walcott", age: 24 },
    { name: "Bacary Sagna", age: 28  }
].sort(function(obj1, obj2) {
    // Ascending: first age less than the previous
    // 实现增序排列：前者的 age 小于后者
    return obj1.age - obj2.age;
});
    // Returns:  
    // [
    //    { name: "Theo Walcott", age: 24 },
    //    { name: "Bacary Sagna", age: 28  },
    //    { name: "Robin Van PurseStrings", age: 30 }
    // ]
```

You can sort objects by property, not just simple basic items. In the event that JSON is sent down from the server and objects need to be sorted, keep this in mind!

你不仅可以对简单类型的数组项进行排序，可以通过属性来排序对象。如果哪天服务器端发来一段 JSON 数据，而且其中的对象需要排序，你可别忘了这一招！

## 4. Array Length for Truncation

## 4. 用 `length` 属性来截断数组

There's not a developer out there that hasn't been bitten by JavaScript's pass-objects-by-reference nature. Oftentimes developers will attempt to [empty an array](http://davidwalsh.name/empty-array) but mistakenly create a new one instead:

几乎所有开发者都踩过 JavaScript 的这个坑——“传对象只是传引用”。开发者们经常会试图 [把一个数组清空](http://davidwalsh.name/empty-array)，但实际上却错误地创建了一个新数组。

```js
var myArray = yourArray = [1, 2, 3];

// :(
// 囧
myArray = []; // `yourArray` is still [1, 2, 3]
              // `yourArray` 仍然是 [1, 2, 3]

// The right way, keeping reference
// 正确的方法是保持引用
myArray.length = 0; // `yourArray` and `myArray` both []
                    // `yourArray` 和 `myArray`（以及其它所有对这个数组的引用）都变成 [] 了
```

What these developers probably realize is that objects are passed by reference, so while setting myArray to `[]` does create a new array, other references stay the same! Big mistake! Use array truncation instead.

坑里的人们终于明白，原来传对象只是在传引用。因此当我把 myArray 重新赋值为 `[]` 时，确实会创建出一个新的空数组，但其它对老数组的引用仍然没变！大坑啊！还是换用截断的方法吧，少年。

## 5. Array Merging with push

## 5. 使用 `push` 来合并数组

I showed in point 2 that Array's slice and apply can do some cool stuff, so it shouldn't surprise you that other Array methods can do the same trickery. This time we can merge arrays with the `push` method:

在上面的第 2 节里，我展示了数组的 `slice` 和 `apply` 方法所能组合出的几个小妙招，所以对于数组方法的其它技巧，你应该已经做好心理准备了吧。这次我们使用 `push` 方法来合并数组：

```js
var mergeTo = [4,5,6];
var mergeFrom = [7,8,9];

Array.prototype.push.apply(mergeTo, mergeFrom);

mergeTo; // is: [4, 5, 6, 7, 8, 9]
```

A wonderful example of a lessor-known, simple native method for completing the basic task of array merging.

这是一项不为人知的小技巧，简单的原生方法就可以实现数组合并这样的常见任务。

（译注：这个方法的巧妙之外不仅在于 `push` 方法可以接收多个参数，还涉及到 `apply` 方法的第二个参数的用法。）

## 6. Efficient Feature/Object Property Detection

## 6. 高效探测功能特性和对象属性

Oftentimes developers will use the following technique to detect a browser feature:

很多时候开发者们会像下面这样来探测浏览器的某个特性：

```js
if(navigator.geolocation) {
    // Do some stuff
    // 在这里干点事情
}
```

While that works correctly, it isn't always efficient, as that method of object detection can initialize resources in the browser. In the past, the snippet above caused memory leaks in some browsers. The better and more efficient route is checking for a key within an object:

当然这可以正常工作，但它并不一定有很好的效率。因为这个对象探测方法会在浏览器中初始化资源。在过去，上面的代码片断可能会在某些浏览器下导致内存泄露。更好、更快的方法是检查对象是否包含某个键名：

```js
if("geolocation" in navigator) {
    // Do some stuff
    // 在这里干点事情
}
```

This key check is as simple as it gets and may avoid memory problems. Also note that if the value of a property is falsy, your check will fail despite the key being present.

键名检查十分简单，而且可以避免内存泄露。另外请注意，如果这个属性的值是假值，那么前一种探测方式将会得到“否”的结果，并不能真正探测出这个键名是否存在。

## 7. Event preventDefault and stopPropagation

## 7. 事件对象的 `preventDefault` 和 `stopPropagation` 方法

Oftentimes we trigger functionality when action elements like links are clicked. Obviously we don't want the browser to follow the link upon click, so we use our handy JavaScript library's `Event.stop` method:

很多时候，当一个动作元素（比如链接）被点击时，会触发某个功能。显然我们并不希望点击链接时浏览器顺着这个链接跳转，于是我们会习惯性地使用 JavaScript 类库的 `Event.stop` 方法：

```js
$("a.trigger").on("click", function(e) {
    e.stop();

    // Do more stuff
    // 在这里干点事情
});
```

（译注：不知道哪个类库有这个方法，估计其作用相当于 `return false;` 吧。语法看起来像 jQuery，但 jQuery 并没有这个方法，而且 jQuery 是支持 `e.preventDefault` 和 `e.stopPropagation` 方法的。）

The problem with this lazy method of stopping the event is that not only does it prevent the default action, but it stops propagation of the event, meaning other event listeners for the elements wont fire because they don't know about the event. It's best to simply use `preventDefault`!

这个懒方法有一个问题，它不仅阻止了浏览器的默认动作，同时也阻止了事件继续冒泡。这意味着元素上绑定的其它事件监听器将不会被触发，因为它们根本就不知道有事件发生。此时不妨使用 `preventDefault` 吧！

Seasoned JavaScript developers will see this post and say "I knew those," but at one point or another, they got tripped up on some of these points. Be mindful of the little things in JavaScript because they can make a big difference.

JavaScript 老鸟们看到这篇文章可能会说“我早知道了”，但说不定什么时候，他们就会在某一点上栽跟头。提醒大家留意 JavaScript 中的各种小细节，失之毫厘谬以千里啊！

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/21)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
