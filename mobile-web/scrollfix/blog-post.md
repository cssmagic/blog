# [译] ScrollFix.js：一个 iOS5 溢出滚动的（有限）修复方案

> * Original: [A (partial) fix for iOS5 overflow scrolling (ScrollFix.js)](http://blog.joelambert.co.uk/2011/10/14/a-fix-for-ios5-overflow-scrolling-scrollfix-js/)
> * Translated by: [cssmagic](https://github.com/cssmagic)

**Update**: *Unfortunately this does not 100% solve the problem, the script falls down when handling touch interactions when a scrolling section is bouncing/rubber banding. I don’t think this issue can be worked around (but would love to be proved wrong!). As such, I don’t advise the use of `overflow: scroll` for web apps until Apple provide a fix, I’d recommend continuing to use [iScroll](http://cubiq.org/iscroll-4) or [Scrollability](http://joehewitt.github.io/scrollability/) in the meantime!*

**更新**：遗憾的是，这个脚本不能百分之百地解决问题，如果滚动区块正处在橡皮筋效果的回弹过程中，此时的触摸交互无法修正（译注：有机会再详述）。我认为这个问题无法变通解决（但我希望你能证明我错了）。因此，我不建议在 web app 中使用 `overflow: scroll`，除非将来 Apple 提供修复。我目前会推荐继续使用 [iScroll](http://cubiq.org/iscroll-4) 或 [Scrollability](http://joehewitt.github.io/scrollability/)。

***

One of the things I was most looking forward to in iOS5 was the added support for `overflow: scroll` and the associated `-webkit-overflow-scrolling: touch`.

iOS5 最令人期待的一点，就是增加对 `overflow: scroll` 以及与之相关的 `-webkit-overflow-scrolling: touch` 的支持。

After a bit of use, there is at least one issue with the implementation that makes it difficult to use for full screen web apps. Fortunately there is a work around.

在稍加试用之后，我发现这个特性的实现方式至少存在一个问题——很难用于全屏的 web app。幸运的是，我找到了一个变通方法。

The newly supported `overflow:scroll` is a great addition to Mobile Safari’s arsenal and works well except under the following conditions:

这个新特性令 Mobile Safari 如虎添翼，十分好用，但以下情况除外：

* The scroll area is at the top and the user tries to scroll up
* The scroll area is at the bottom and the user tries to scroll down.
* 滚动区域已经滚到顶部时，用户试图继续向上滚动。
* 滚动区域已经滚到底部时，用户试图继续向下滚动。

In a native app, you’d expect the content to rubber band but in Safari the whole page is scrolled instead.

在原生应用中，你会期望只有内容区呈现橡皮筋效果，但在浏览器中的效果是整个页面都被拖动了。

Enter [ScrollFix.js](https://github.com/joelambert/ScrollFix), a small script that works around this problem.

试试 [ScrollFix.js](https://github.com/joelambert/ScrollFix) 吧，一小段脚本就可以变通解决这个问题。

ScrollFix works around this by manually offsetting the `scrollTop` value to one away from the limit at either extreme, which causes the browser to use rubber banding rather than passing the event up the DOM tree.

ScrollFix 的原理是这样的，在触摸开始时，如果发现滚动区域已经处于极限状态时，就手工设置 `scrollTop` 的值，将滚动内容向边缘方向偏移 1px（这实际上改变了滚动区域的极限状态），从而诱使浏览器对滚动区块使用橡皮筋效果，而不会把触摸事件向上传播到 DOM 树（引起整页滚动）。

To better demonstrate the problem (and solution) here are a couple of videos:

为了更好地演示这个问题（和解决方案），这里放出两个视频（YouTube 需翻小墙）：

* [iOS5 overflow scrolling without ScrollFix.js](http://www.youtube.com/watch?v=Oe6xv8IgasA)
* [iOS5 overflow scrolling with ScrollFix.js](http://www.youtube.com/watch?v=kk7bMj0K0v4)
* [未使用 ScrollFix.js 的 iOS5 溢出滚动效果](http://www.youtube.com/watch?v=Oe6xv8IgasA)
* [已使用 ScrollFix.js 的 iOS5 溢出滚动效果](http://www.youtube.com/watch?v=kk7bMj0K0v4)

（译注：同时附上后者的 [在线演示](http://joelambert.github.io/ScrollFix/)，请使用 iOS 设备访问。）

ScrollFix is a work in progress (there are still bugs!) and can be downloaded for free from [GitHub](https://github.com/joelambert/ScrollFix). Please contribute code fixes or open tickets for discussion.

ScrollFix 仍然在开发中，还有一些 bug，但你可以在 [GitHub](https://github.com/joelambert/ScrollFix) 免费下载（译注：在原文发表后不久，这个项目就基本成熟了）。你可以贡献代码，也可以提交 issue 展开讨论。

***

### 译者后记

在 iOS5 发布之前，构建 web app 的一个老大难问题就是局部滚动。实现类似原生应用的“页头固定 + 内容滚动”这一经典布局，正是 web app 的一大痛点，这也催生了 iScroll 这类项目。

幸运的是，iOS5 对 `overflow: scroll` 的支持为 web app 的 UI 进化提供了新的契机。本文作者的探索进一步将梦想变为现实。

ScrollFix.js 用非常巧妙思路的解决了溢出滚动区域的触摸交互问题，更有价值的是，它使用的是浏览器的原生滚动特性，而不是像 iScroll 这样的模拟滚动实现。原生滚动带来的好处是更少的性能消耗、更灵敏的触控体验，同时可以与表单元素的交互行为合睦相处。

本文作者是个完美主义者，他在文章开头中提到的“小问题”实际上并没有那么严重（在 iOS 5.1 中已经很难重现，而且 iOS6 已经修复）。因此，我的建议是，如果你的项目只需要支持较高版本的 iOS 5+ 和 Android 4+，就果断放弃 iScroll，拥抱轻巧顺滑的原生滚动吧！

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [��Ҫ����](https://github.com/cssmagic/blog/issues/8) &nbsp; | &nbsp; [��Ҫ����](https://github.com/cssmagic/blog/issues/9)

&nbsp;
> * [更多文章](https://github.com/cssmagic/blog/issues)
> * [查看原文与评论](https://github.com/cssmagic/blog/issues/1)
