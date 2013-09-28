# [译] iOS 7 之 HTML5 综合评测：亮点、弱点和槽点

> * Original: [The HTML5 Scorecard: The Good, The Bad and the Ugly in iOS 7](http://www.sencha.com/blog/the-html5-scorecard-the-good-the-bad-and-the-ugly-in-ios7/)
> * Translated by: [cssmagic](https://github.com/cssmagic)

![iOS7 iPhone](https://f.cloud.github.com/assets/1231359/1230752/fe826190-2802-11e3-92e9-3f1fb0c058c2.png)

We've been testing the final release of iOS 7 over the last few days against our usual battery of HTML5 tests. Normally we're effusive about new releases of iOS to the point of fanboy-dom, but this time, and for the first time ever, we're disappointed in the execution of iOS software. Although there are some impressive performance gains in SVG and JavaScript, the sheer number of bugs and broken features, clearly mark this release as a beta. While nowhere as bad as the Android 3 browser -- our all time champ of broken web releases -- we recommend that organizations standardized on HTML5 development hold off upgrading until an iOS update fixes these issues.

过去几天以来，我们一直在用我们的那套常规 HTML5 测试套件来考验 iOS 7 的最终发布版。正常来说，每当迎来 iOS 的新版发布，我们都忍不住像果粉一样喜大普奔；但这一次，也是有史以来的第一次，我们对 iOS 软件的完成度表示失望。虽然在 SVG 和 JavaScript 方面有一些突出的性能增长，但面对如此之多的 bug 和不正常的特性，我们只能无情地为这个版本打上 beta 的标签。当然，它还远远没有败坏到 Android 3 浏览器的程度——那才是无可争议的烂浏览器之终极奥义——但我们仍然建议那些以 HTML5 作为开发标准的公司暂不升级，等到 iOS 7 后续版本修复这些问题之后再说。

## iOS 7 Bugs &amp; Features

## iOS 7 的 bug 和特性

Max Firtman has already done an excellent first pass about [the new features, bugs and quirks in iOS 7's web runtime][59]. If you haven't read his post, you should read it now. We will not repeat all the findings here; but to review, there are two very big bugs in iOS 7. First, WebSQL permissions to increase the default 5MB of space for an app to the previously permitted 50MB limit no longer work correctly, and require a workaround. Second, "Save to Home Screen" apps are basically broken. Once more than four apps are saved to home screen, the save slots are recycled and sometimes duplicated, and the phone has to be rebooted in order to clear itself. Further, any external URI no longer opens correctly and all JavaScript modal dialogs (alert, prompt etc.) are disabled. Finally, If your app uses AppCache and you are managing state via hash or other mechanisms, the history object will never update, disabling history.back.

Max Firtman 已经对 [iOS7 浏览器的新功能、bug 以及奇怪特性][59] 进行了出色的首轮总结，强烈建议你先读一遍这篇文章。（译注：也可以参考 [这篇文章的中文译版](http://jinlong.github.io/blog/2013/09/23/safari-ios7-html5-problems-apis-review/)。）本文不再重复他的所有发现，我们只回顾一下其中两个巨大的 bug。

首先，单个应用对 WebSQL 存储空间的需求从默认的 5MB 申请为 50 MB 上限的过程无法正常实现（译注：实际上是 iOS 7 在这方面的许可策略发生了变化），需要想办法绕过去。

其次，“保存到主屏幕”的 web app 基本上废掉了。似乎主屏幕最多只提供 4 个蹲位来容纳 web app 图标，一旦你试图突破这个数量，web app 图标就会相互循环覆盖，有时甚至出现重复，此时手机不得不重启才能将主屏幕恢复正常。此外，所有的外部 URL 都无法正常打开（译注：包括可以拨打电话的 `tel:` 伪协议链接），而且所有的 JavaScript 原生模态对话框（`alert()` 和 `prompt()` 等等）也都被禁用。别着急，还有，如果你的应用使用了 AppCache（译注：HTML5 的离线应用缓存特性）并且你通过 hash 或其它机制来管理浏览进程，那么 history 对象永远都不会更新，`history.back()` 方法也被禁用。

> "We recommend that organizations standardized on HTML5 development, hold off on upgrading to iOS 7 until an update fixes these issues."

> “我们建议那些以 HTML5 作为开发标准的公司暂不升级，等到 iOS 7 后续版本修复这些问题之后再说。”

Beyond these major bugs, there are also some very troublesome design decisions in iOS 7. First, there is no way to hide the URL bar using JavaScript (and the user no longer has a "full screen" button in mobile Safari). This will throw a wrench into layouts if your app relies on absolute positioning. One workaround, [suggested by Andrea Giammarchi][60], is to ask the user to take an action (such as swiping up) in order to reset into full-screen. But there is no programmatic way to do this (as of yet). And once you are in full-screen, tapping anywhere in the bottom region first summons the browser chrome and there is no way to cancel this. This makes for poor UX for bottom-positioned toolbars: the first user tap summons the browser chrome, which boosts the tool-bar up the page, and then requires the user to tap again to take an action. There are related problems with the status bar [which can be worked around.][61]

抛开这些重大 bug 不谈，iOS 7 还采用了一些非常讨厌的设计决策。首先，无法通过 JavaScript 来隐藏地址栏（而且 Safari 也不再提供一个“全屏”按钮）。如果你的应用依赖于绝对定位，这个问题将破坏应用的布局。有一个变通方案（[由 Andrea Giammarchi 提议][60]），就是要求用户做一个动作（比如向上滚屏），以便进入全屏状态。但到目前为止，还没有一个纯程序的方式可以实现类似的效果。而且一旦你进入了全屏状态，在屏幕底部区域的任意位置点击，会首先呼出浏览器边框（包括浏览器的工具栏和地址栏），而且没有办法能阻止这种行为。这将导致定位在底部的工具栏呈现出非常恶心的用户体验（译注：这里的工具栏是指应用自己实现的工具栏）：用户的第一次点击工具栏会呼出浏览器的边框，工具栏会被向上推起，然后用户还需要移动手指并再次点击，才能最终触发工具栏上的某个动作按钮。状态栏也有着类似的问题，不过幸好有人找出了 [一个变通方法][61]。

（译注：上面这个链接所描述的状态栏问题实际上只出现在基于 PhoneGap 开发的混合应用身上，并不涉及 Safari 或 web view 形态的页面布局。下个版本的 PhoneGap 很可能会从自身层面来解决这个问题。）

In addition to these decisions, right and left swipe gestures within about 10 percent of display edge are always grabbed by iOS and treated as a forward/back request, and not passed to the browser. Furthermore, if you've built back/forward behavior into your app using history push-state, then accidental swipes will load the previous state as if it was a prior web-site. This will probably be unexpected behavior. Chrome for Android was the first browser to introduce this behavior, but has now removed it based on feedback from web developers. We hope Apple follows suit quickly.

除了这些设计决策之外，从屏幕边缘大约 10% 区域内发起的左右横扫手势总是会被系统捕获，并被视为一次后退/前进的导航操作，而不会作为事件传递给浏览器。不仅如此，如果你的应用使用了历史记录的 pushState 接口来管理后退/前进行为，那么无意的横扫动作将导致页面回到上一个状态，本质上相当于后退到上了一个页面。这很可能不是我们所期望的行为。Android 版的 Chrome 是最先引入了这种行为的浏览器，但后来又在 web 开发者的要求下去除了。我们也希望苹果能尽快回到正轨。

（译注：上面提到的后退/前进手势只会在 Safari 下起作用，主屏幕 web app 或 web view 并没有这两个手势。）

In our own testing, we discovered a number of additional bugs in the iOS 7 runtime.

在我们自己的测试中，我们还在 iOS 7 中发现了一些其它 bug。

* On iPad, an orientation change when an input is focused shifts content unpredictably, and causes screen rendering artifacts.

* 在 iPad 上，当一个输入框获得焦点时，屏幕旋转会导致页面内容无规律地偏移，以及屏幕渲染错乱。

* Launching and quitting the same home screen app several times can hard lock the device requiring a hardware reboot.

* 启动并退出同一个主屏幕 web app 数次之后，会导致设备锁死，只能通过硬件重启来解决。

* requestAnimationFrame animations do not appear to background correctly, and cause performance degradation in RAF animations on the active page. This defeats one of the major purposes of using RAF animations.

* requestAnimationFrame 动画（以下简称 RAF 动画）在后台运作不正常，而且会导致当前页面的 RAF 动画性能下降。这一点直接灭掉了 RAF 动画的最大优势。

* On iPad, if the document body is set to 100 percent height, content is shifted upwards by 20px in landscape mode. This can be worked around by calling window.scrollTo(0, 0) on the orientationchange event.

* 在 iPad 上，如果 `body` 的高度被设置为 100% 时，在横屏状态下，内容会向上偏移大约 20px。还好这也是可以补救的，我们在 `orientationchange` 事件上调用一次 `window.scrollTo(0, 0)` 就可以了。（译汪：这个方法真的好吗？如果我在竖屏状态下滚动到一半再旋转为横屏呢……）

* In certain cases, resizing a composited layer (an element with 3D transform) does not repaint it correctly. Instead, the cached bitmap is stretched.

* 在某些情况下，一个经过渲染合成的图层（比如一个具有 3D 变形属性的元素）在改变大小时无法正确地重绘。仅仅是把已缓存的位图（即以前渲染完成的结果）拉伸一下完事，并没有重新渲染生成。

* CSS Animations will sometimes fire before implicit z-indexes have been calculated, resulting in incorrect z layering during an animation.

* CSS 动画有时会在隐式的 `z-index` 被计算之前启动，这导致动画期间图层的堆叠不正确。

* Scripts running within Web Workers are not suspended unless either the originating page is explicitly killed, or the Safari process is explicitly terminated. Neither switching to another tab, nor minimizing Safari, nor turning off the screen seem to stop Worker execution. This is a serious issue that allows any web page to drain the battery of an iOS 7 device and slow down performance of the whole system without a user alert.

* 在 Web Worker 内运行的脚本永远不会停止，除非发起 Web Worker 的页面被强制关闭，或者 Safari 进程被强制终止。切换到另一个页面、最小化 Safari（译注：即切换到后台）或者锁屏都无法停止 Web Worker 的运行。这是一个很严重的问题，它放任任意网页在没有任何提示的情况下把电池耗光，并拖慢整个系统的性能。

## Performance Characteristics

## 性能评估

In addition to feature/bug testing, we also put iOS 7 through a battery of our standard performance tests on an iPhone 5 running iOS 6.1 vs. iOS 7. There are some remarkable increases in benchmark performance as well as some very notable misses. First up, we want to note that something odd has happened to the JavaScript timer on iOS 7. In previous versions, iOS had an exceptionally well implemented timer: 4ms with extremely good consistency (see below). But using [John Resig's standard timer test][62] resulted in this odd profile: a timer that jumps between 4ms and 12ms with clockwork regularity and much more noise than iOS 6.

除了特性测试和 bug 测试之外，我们对 iOS 7 进行了一系列我们自己的标准性能测试，并对比 iPhone 5 上 iOS 7 和 iOS 6.1 的测试结果。在跑分方面，有一些值得注意的增长，同时也有一些无法忽视的倒退。首先，我们想指出，iOS 7 上的 JavaScript 定时器出现了一些奇怪的情况。在上一个 iOS 版本中，定时期简直就是梦幻般地完美：4ms 并且有着非常好的一致性（见下图）。但 iOS 7 在进行 [John Resig 的标准定时器测试][62] 时，结果十分奇怪：定时器的间隔在 4ms 和 12ms 之间有规律地来回摆动，与 iOS 6 相比要杂乱不少。

![iOS7 timer](https://f.cloud.github.com/assets/1231359/1230755/055f5e1e-2803-11e3-9034-08cd4bd0bb1c.png)

Figure 1A: JavaScript timer resolution: iPhone 5/iOS 7

图 1A：JavaScript 定时器解析度：iPhone 5/iOS 7

![iOS6 timer](https://f.cloud.github.com/assets/1231359/1230757/09d28ef8-2803-11e3-9368-c9170358de1a.png)

Figure 1B: JavaScript timer resolution: iPhone 5/iOS 6

图 1B：JavaScript 定时器解析度：iPhone 5/iOS 6

Perhaps this is a limitation of the test in some way, but it's certainly nothing we've ever seen before, and one more reason to make sure that you use requestAnimationFrame for JavaScript animation.

可能这个测试在某些方面存在局限性，但这种情况是我们以前从未见过的，于是我们又多了一个使用 requestAnimationFrame 来实现动画的理由。

In good news, raw JavaScript performance has increased substantially. SunSpider 1.0 is about 15% faster on iOS 7 vs iOS 6.1, and iOS 7's Octane score is 70% better vs. iOS 6. Some Octane tests showed dramatic speed-ups: Navier-Stokes performance increased by almost 4x. By comparison, Safari on my 2 year old MacBook clocks in at 5,600 -- so iOS 7 is now 50% as fast as desktop Safari on Octane! This is either some serious JIT hacking, or we also speculate that there may be some GPU offloading of general computation in iOS 7?

好消息还是有的，纯 JavaScript 性能有了大幅度的提升。与 iOS 6 相比，iOS 7 的 SunSpider 1.0 跑分提升了 15%，Octane 跑分提升了 70%。部分 Octane 测试显示出了惊人的速度增长：Navier-Stokes 运算的性能涨幅几乎达到了 4 倍。要知道在两年前的老 MacBook 笔记本上，Safari 的 Octane 综合得分是 5600 分，相比之下，现在 iOS 7 的性能已经相当于桌面平台的 50% 了！这有可能是某些 JIT 技巧的功劳，也有可能是 GPU 在 iOS 7 下以某种方式分担了 CPU 的运算工作？

![Octane Benchmark](https://f.cloud.github.com/assets/1231359/1230758/0c470826-2803-11e3-8292-3fee4016d36f.png)

Figure 2: Octane Benchmark - iPhone 5 iOS 6 vs. iOS 7 (higher is better)

图 2：Octane 性能评分：iPhone 5 iOS 6 与 iOS 7 相比（得分越高越好）

But it's not all good news on the performance front. During the iOS 7 beta, we were concerned at the very slow DOM interaction benchmarks that we were seeing from Dromaeo on iOS 7, and expected that Apple would get performance back to snuff before final release. For DOM traversal, attributes and modification, performance is now back at iOS 6 levels, which is good. However DOM Query is still 50% of iOS 6 speed. This is a major concern for the many HTML5 apps that perform high numbers of DOM queries, and this needs to be on Apple's fix-list for its next update.

不过在性能方面并不都是好消息。在 iOS 7 的 beta 期间，我们就曾为 iOS 7 在 Dromaeo 测试上超低的 DOM 操作得分捏把汗，并期待苹果在最终正式版中扭转局面。果然，在 DOM 遍历、存取属性和修改操作方面，iOS 7 的性能已经回到了 iOS 6 的水准，这很好。但是 DOM 查询的速度仍然只有 iOS 6 的 50% 左右。这对很多需要大量查询 DOM 的 HTML5 应用来说，会是一个很大的顾虑，这也是苹果在下一次更新时需要重点考虑的问题。

![Dromaeo benchmark](https://f.cloud.github.com/assets/1231359/1230759/0f008448-2803-11e3-910a-0a8c2896d954.png)

Figure 3: Dromaeo benchmark - iOS 6 vs iOS 7 (iOS 6 = 1.00 - higher is better)

图 3：Dromaeo 性能评分：iOS 6 与 iOS 7 相比（iOS 6 的得分换算为 1.00，得分越高越好）

## Graphics Performance

## 图形性能

Test of Canvas performance show a minor improvement in iOS 7 -- about 10% in the [Fishtank][67] test and on [Mindcat][68] microbenchmarks. But SVG is the real revelation. Thanks to a switch to a new drawing algorithm, SVG Path drawing speed has improved _200x_. Yes that's literally 200 times faster. In iOS 6, [a 10,000 segment SVG path took about 11 seconds to draw][69]. In iOS 7 that's now 53 milliseconds. iOS is now 6x faster than the Surface RT -- the previous champ at SVG drawing performance.

在 Canvas 性能方面，iOS 7 表现出了少许进步——在 [Fishtank][67] 测试和 [Mindcat][68] 性能评分中均有 10% 左右的提升。但真正令人惊讶的是 SVG 性能。得益于全新的绘图算法，SVG 路径绘制速度提升了 **200 倍**，是的，你没有看错，两百倍。在 iOS 6 中，[一条 10,000 个片段的 SVG 路径需要花费 11 秒才能绘制完成][69]；但在 iOS 7 中，只需要 53 毫秒。iOS 目前以 6 倍性能领先于 Surface RT——上一代的 SVG 性能之王。

![SVG Path Drawing Benchmark](https://f.cloud.github.com/assets/1231359/1230760/11d38e36-2803-11e3-8424-1e97af857c24.png)

Figure 4: SVG Path Drawing Benchmark (lower is better)

图 4：SVG 路径绘制性能评分（得分越少越好）

Other SVG capabilities experience similar speed-ups. Some SVG Filter operations now appear to be GPU accelerated -- which means that meaningful filter animations are now possible on iOS. But performance is dependent on specific filters. Color transformations (Color Matrix &amp; Color Curves) and displacementMaps are fast. Complex compositing and lighting effects are still slow.

其它的 SVG 指标方面也有着类似的速度提升。一些 SVG 滤镜操作看起来已经支持 GPU 加速——这意味着精心设计的滤镜动画在 iOS 上将成为可能。不过性能表现还是因滤镜而异，色彩变换（色彩矩阵与色彩曲线）和置换贴图是最快的，但复杂的合成和光照效果仍然比较慢。

And now the real killer. In the rush to get iOS 7 out the door, making sure SVG animation via JavaScript was fast seems to have been dropped on the floor. Animating SVG with JavaScript is now a hit or miss proposition. [Animating 500 SVG circles][71] used to be 50 fps on iOS 6. On iOS 7, the animation simply freezes after a few frames. We tried other apps that have interactive UI elements built with SVG, and we saw a similar severe performance degradation.

接下来的这个才是极品。苹果赶着把 iOS 7 推到台前，结果把 SVG 动画这件事儿抛到九霄云外了。用 JavaScript 实现的 SVG 动画这一需求被完全地漠视了。[500 个动态的 SVG 圆圈测试][71] 在 iOS 6 上可以轻易跑到 50 帧/秒，但在 iOS 7 上，动画在跑了几帧之后就完全卡住了。我们心有不甘，又尝试了其它一些基于 SVG 实现 UI 交互的应用，但我们遭遇到了同样不忍直视的低下性能。

## iOS 7: A Beta Release of Web

## iOS 7 在 web 方面仍未成熟

![iOS7 Logo](https://f.cloud.github.com/assets/1231359/1230754/01ffc1e6-2803-11e3-8930-adc652ad30f2.png)

Given all these bugs and issues, combined with some genuine major advances, it's hard not to interpret this as a beta release that was rushed into production for the release of the iPhone 5S. In a way, it reminds us of the Android 3 release -- which was rushed into production for the Motorola Xoom tablet -- with severe bugs and performance deficiencies. We're eagerly awaiting the release of the first update for iOS 7 when we hope Apple delivers on its usual commitment to quality.

综合所有这些 bug 和缺陷，以及少许有诚意的进步，我们不得不认为，为了配合 iPhone 5s 的如期发布，一个 beta 级别的系统就这么仓促地被推到了台前。在某种程度上，这让我们想起了 Android 3 的发布——为了配合 Motorola Xoom 平板电脑的发布而不得不赶着上线——结果留下了严重的 bug 和性能缺陷。我们急切盼望 iOS 7 后续更新的发布，同时我们希望苹果可以履行它一直以来对品质的承诺。

But beyond bugs, the design decisions in iOS 7 clearly privilege consumer content over business applications. We remain convinced that Enterprises that want to deploy HTML5 applications to mobile devices can't rely on consumer browsers and need a secure and predictable mobile environment designed for business applications. iOS 7 has convinced us that more than ever that the future of HTML5 app deployment for business is [Sencha Space][11].

但是抛开这些 bug 不谈，iOS 7 所采用的设计策略是十分清晰的——重消费者内容，轻商业应用。我们相信，那些需要将 HTML5 应用部署到移动端的企业们不能依赖消费级浏览器，而是需要一个专为商业应用设计的、安全可控的移动环境。iOS 7 更加明确地告诉我们，HTML5 商业应用部署方式的未来就是 [Sencha Space][11]。

（译注：你妹，翻译了这大半天，原来是篇软文？不过 iOS 7 这回真的让人揪心啊，曾经的 web 技术先锋，曾经的最佳 web app 平台，如今迷失在自己曾经的辉煌里。）

**Written by Michael Mullany**

**作者介绍**

Michael Mullany is Sencha's CEO. He has held various product and executive marketing roles at influential Silicon Valley startups Netscape, Loudcloud, and VMware. He holds an MBA from Stanford University and a BA in economics from Harvard College.

Michael Mullany 是 Sencha 的 CEO。他曾在很多有影响力的硅谷创业公司（比如网景、Loudcloud、VMware）中担任过多个产品和营销总监职位。他获得了斯坦福大学的 MBA 学位，以及哈佛大学的经济学学士学位。

[11]: http://www.sencha.com/products/space/
[59]: http://www.mobilexweb.com/blog/safari-ios7-html5-problems-apis-review
[60]: http://webreflection.blogspot.com
[61]: http://druckit.wordpress.com/2013/09/24/about-sencha-touch-2-x-phonegapcordova-and-ios-7/
[62]: http://ejohn.org/apps/timers/
[67]: http://ie.microsoft.com/testdrive/performance/fishietank/
[68]: http://dl.dropboxusercontent.com/u/1865210/mindcat/canvas_perf.html
[69]: http://bl.ocks.org/stepheneb/1296930
[71]: http://themaninblue.com/experiment/AnimationBenchmark/svg/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/33)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
