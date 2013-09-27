# [译] iOS7 之 HTML5 综合评测：亮点、弱点和槽点

> * Original: [The HTML5 Scorecard: The Good, The Bad and the Ugly in iOS 7](http://www.sencha.com/blog/the-html5-scorecard-the-good-the-bad-and-the-ugly-in-ios7/)
> * Translated by: [cssmagic](https://github.com/cssmagic)

![iOS7-hero.png][58]

We've been testing the final release of iOS 7 over the last few days against our usual battery of HTML5 tests. Normally we're effusive about new releases of iOS to the point of fanboy-dom, but this time, and for the first time ever, we're disappointed in the execution of iOS software. Although there are some impressive performance gains in SVG and JavaScript, the sheer number of bugs and broken features, clearly mark this release as a beta. While nowhere as bad as the Android 3 browser -- our all time champ of broken web releases -- we recommend that organizations standardized on HTML5 development hold off upgrading until an iOS update fixes these issues.

过去几天以来，我们一直在用我们的那套常规 HTML5 测试套件来测试 iOS 7 的最终发布版。正常来说，每当迎来 iOS 的新版发布，我们都忍不住像果粉一般喜大普奔；但这一次，也是有历以来的第一次，我们对 iOS 软件的完成度表现表示失望。虽然在 SVG 和 JavaScript 方面有一些突出的性能增长，但面对如此之多的 bug 和不正常的特性，我们只能无情地为这个版本打上 beta 的标签。当然，它还远远没有败坏到 Android 3 自带浏览器的程度——那才是无可争议的烂浏览器之最终奥义——但我们仍然建议那些以 HTML5 作为开发标准的公司暂不升级，等到 iOS 7 后续版本修复这些问题之后再说。

## iOS 7 Bugs &amp; Features

## iOS 7 的 bug 和新功能

Max Firtman has already done an excellent first pass about [the new features, bugs and quirks in iOS 7's web runtime][59]. If you haven't read his post, you should read it now. We will not repeat all the findings here; but to review, there are two very big bugs in iOS 7. First, WebSQL permissions to increase the default 5MB of space for an app to the previously permitted 50MB limit no longer work correctly, and require a workaround. Second, "Save to Home Screen" apps are basically broken. Once more than four apps are saved to home screen, the save slots are recycled and sometimes duplicated, and the phone has to be rebooted in order to clear itself. Further, any external URI no longer opens correctly and all JavaScript modal dialogs (alert, prompt etc.) are disabled. Finally, If your app uses AppCache and you are managing state via hash or other mechanisms, the history object will never update, disabling history.back.

Max Firtman 已经对 [iOS7 浏览器的新功能、bug 以及奇怪特性][59] 进行了出色的首轮总结，强烈建议你先读一遍这篇文章。（译注：也可以参考这篇文章的 [中文译版](http://jinlong.github.io/blog/2013/09/23/safari-ios7-html5-problems-apis-review/)。）本文不再重复他的所有发现，我们只回顾一下其中的两个巨大的 bug。

首先，单个应用对 WebSQL 存储空间的需求从默认的 5MB 申请为 50 MB 上限的过程不再正常工作（译注：实际上是 iOS 7 在这方面的许可策略发生了变化），并且需要想办法绕过去。

其次，“保存到主屏幕”的应用基本上废掉了。似乎主屏幕最多只提供 4 个坑来显示 web app 图标，一旦你试图突破这个数量，web app 图标就会循环覆盖，有时甚至出现重复，此时手机不得不重启才能将主屏幕恢复正常。此外，所有的外部 URL 都无法正常打开（译注：包括可以拨打电话的 `tel:` 伪协议链接），而且所有的 JavaScript 原生模态对话框（`alert` 和 `prompt` 等等）也都被禁用。别着急，还有，如果你的应用使用了 AppCache（译注：HTML5 的离线应用缓存特性）并且你通过 hash 或其它机制来管理浏览进程，那么 history 对象永远都不会更新，`history.back` 也被禁用。

> "We recommend that organizations standardized on HTML5 development, hold off on upgrading to iOS 7 until an update fixes these issues."

> “我们建议那些以 HTML5 作为开发标准的公司暂不升级，等到 iOS 7 后续版本修复这些问题之后再说。”

Beyond these major bugs, there are also some very troublesome design decisions in iOS 7. First, there is no way to hide the URL bar using JavaScript (and the user no longer has a "full screen" button in mobile Safari). This will throw a wrench into layouts if your app relies on absolute positioning. One workaround, [suggested by Andrea Giammarchi][60], is to ask the user to take an action (such as swiping up) in order to reset into full-screen. But there is no programmatic way to do this (as of yet). And once you are in full-screen, tapping anywhere in the bottom region first summons the browser chrome and there is no way to cancel this. This makes for poor UX for bottom-positioned toolbars: the first user tap summons the browser chrome, which boosts the tool-bar up the page, and then requires the user to tap again to take an action. There are related problems with the status bar [which can be worked around.][61]



In addition to these decisions, right and left swipe gestures within about 10 percent of display edge are always grabbed by iOS and treated as a forward/back request, and not passed to the browser. Furthermore, if you've built back/forward behavior into your app using history push-state, then accidental swipes will load the previous state as if it was a prior web-site. This will probably be unexpected behavior. Chrome for Android was the first browser to introduce this behavior, but has now removed it based on feedback from web developers. We hope Apple follows suit quickly.

In our own testing, we discovered a number of additional bugs in the iOS 7 runtime.

* On iPad, an orientation change when an input is focused shifts content unpredictably, and causes screen rendering artifacts.
* Launching and quitting the same home screen app several times can hard lock the device requiring a hardware reboot.
* requestAnimationFrame animations do not appear to background correctly, and cause performance degradation in RAF animations on the active page. This defeats one of the major purposes of using RAF animations.
* On iPad, if the document body is set to 100 percent height, content is shifted upwards by 20px in landscape mode. This can be worked around by calling window.scrollTo(0, 0) on the orientationchange event.
* In certain cases, resizing a composited layer (an element with 3D transform) does not repaint it correctly. Instead, the cached bitmap is stretched.
* CSS Animations will sometimes fire before implicit z-indexes have been calculated, resulting in incorrect z layering during an animation.
* Scripts running within Web Workers are not suspended unless either the originating page is explicitly killed, or the Safari process is explicitly terminated. Neither switching to another tab, nor minimizing Safari, nor turning off the screen seem to stop Worker execution. This is a serious issue that allows any web page to drain the battery of an iOS 7 device and slow down performance of the whole system without a user alert.

## Performance Characteristics

In addition to feature/bug testing, we also put iOS 7 through a battery of our standard performance tests on an iPhone 5 running iOS 6.1 vs. iOS 7. There are some remarkable increases in benchmark performance as well as some very notable misses. First up, we want to note that something odd has happened to the JavaScript timer on iOS 7. In previous versions, iOS had an exceptionally well implemented timer: 4ms with extremely good consistency (see below). But using [John Resig's standard timer test][62] resulted in this odd profile: a timer that jumps between 4ms and 12ms with clockwork regularity and much more noise than iOS 6.

![iOS7 timer][63]

Figure 1A: JavaScript timer resolution: iPhone 5/iOS 7

![iOS6 timer][64]

Figure1B: JavaScript timer resolution: iPhone 5/iOS 6

Perhaps this is a limitation of the test in some way, but it's certainly nothing we've ever seen before, and one more reason to make sure that you use requestAnimationFrame for JavaScript animation.

In good news, raw JavaScript performance has increased substantially. SunSpider 1.0 is about 15% faster on iOS 7 vs iOS 6.1, and iOS 7's Octane score is 70% better vs. iOS 6. Some Octane tests showed dramatic speed-ups: Navier-Stokes performance increased by almost 4x. By comparison, Safari on my 2 year old MacBook clocks in at 5,600 -- so iOS 7 is now 50% as fast as desktop Safari on Octane! This is either some serious JIT hacking, or we also speculate that there may be some GPU offloading of general computation in iOS 7?

![iOS7][65]

Figure 2: Octane Benchmark - iPhone 5 iOS 6 vs. iOS 7 (higher is better)

But it's not all good news on the performance front. During the iOS 7 beta, we were concerned at the very slow DOM interaction benchmarks that we were seeing from Dromaeo on iOS 7, and expected that Apple would get performance back to snuff before final release. For DOM traversal, attributes and modification, performance is now back at iOS 6 levels, which is good. However DOM Query is still 50% of iOS 6 speed. This is a major concern for the many HTML5 apps that perform high numbers of DOM queries, and this needs to be on Apple's fix-list for its next update.

![iOS7][66]

Figure 3: Dromaeo benchmark - iOS 6 vs iOS 7 (iOS 6 = 1.00 - higher is better)

## Graphics Performance

Test of Canvas performance show a minor improvement in iOS 7 -- about 10% in the [Fishtank][67] test and on [Mindcat][68] microbenchmarks. But SVG is the real revelation. Thanks to a switch to a new drawing algorithm, SVG Path drawing speed has improved _200x_. Yes that's literally 200 times faster. In iOS 6, [a 10,000 segment SVG path took about 11 seconds to draw][69]. In iOS 7 that's now 53 milliseconds. iOS is now 6x faster than the Surface RT -- the previous champ at SVG drawing performance.

![iOS7][70]

Figure 4: SVG Path Drawing Benchmark (lower is better)

Other SVG capabilities experience similar speed-ups. Some SVG Filter operations now appear to be GPU accelerated -- which means that meaningful filter animations are now possible on iOS. But performance is dependent on specific filters. Color transformations (Color Matrix &amp; Color Curves) and displacementMaps are fast. Complex compositing and lighting effects are still slow.

And now the real killer. In the rush to get iOS 7 out the door, making sure SVG animation via JavaScript was fast seems to have been dropped on the floor. Animating SVG with JavaScript is now a hit or miss proposition. [Animating 500 SVG circles][71] used to be 50 fps on iOS 6. On iOS 7, the animation simply freezes after a few frames. We tried other apps that have interactive UI elements built with SVG, and we saw a similar severe performance degradation.

## iOS 7: A Beta Release of Web

![20130924-iOS7.png][72]Given all these bugs and issues, combined with some genuine major advances, it's hard not to interpret this as a beta release that was rushed into production for the release of the iPhone 5S. In a way, it reminds us of the Android 3 release -- which was rushed into production for the Motorola Xoom tablet -- with severe bugs and performance deficiencies. We're eagerly awaiting the release of the first update for iOS 7 when we hope Apple delivers on its usual commitment to quality.

But beyond bugs, the design decisions in iOS 7 clearly privilege consumer content over business applications. We remain convinced that Enterprises that want to deploy HTML5 applications to mobile devices can't rely on consumer browsers and need a secure and predictable mobile environment designed for business applications. iOS 7 has convinced us that more than ever that the future of HTML5 app deployment for business is [Sencha Space][11].

**Written by Michael Mullany**
Michael Mullany is Sencha's CEO. He has held various product and executive marketing roles at influential Silicon Valley startups Netscape, Loudcloud, and VMware. He holds an MBA from Stanford University and a BA in economics from Harvard College.

[1]: http://www.sencha.com/img/sencha-large.png
[2]: http://www.sencha.com#content
[3]: http://www.sencha.com/products/
[4]: http://www.sencha.com/products/complete/
[5]: http://www.sencha.com/products/touch-bundle/
[6]: http://www.sencha.com/products/touch/
[7]: http://www.sencha.com/products/extjs/
[8]: http://www.sencha.com/products/gxt/
[9]: http://www.sencha.com/products/architect/
[10]: http://www.sencha.com/products/animator/
[11]: http://www.sencha.com/products/space/
[12]: http://www.sencha.com/support/
[13]: http://www.sencha.com/support/services/
[14]: http://www.sencha.com/support/faq/
[15]: http://www.sencha.com/training/
[16]: http://www.sencha.com/training/courses/
[17]: http://www.sencha.com/company/
[18]: http://www.sencha.com/company/team/
[19]: http://www.sencha.com/company/careers/
[20]: http://www.sencha.com/company/events/
[21]: http://www.sencha.com/company/press/
[22]: http://www.sencha.com/company/partners/
[23]: http://www.sencha.com/company/contact/
[24]: http://www.sencha.com/company/customers/
[25]: http://www.sencha.com/blog/
[26]: http://www.sencha.com/company/contact
[27]: http://www.sencha.com/store/
[28]: http://www.sencha.com/store/sencha-complete/
[29]: http://www.sencha.com/store/sencha-touch-bundle/
[30]: http://www.sencha.com/store/architect/
[31]: http://www.sencha.com/store/extjs/
[32]: http://www.sencha.com/store/touch/
[33]: http://www.sencha.com/store/gxt/
[34]: http://www.sencha.com/store/animator/
[35]: http://www.sencha.com/store/ordering-information/
[36]: http://www.sencha.com/store/licensing-faq/
[37]: http://www.sencha.com/store/authorized-resellers/
[38]: http://www.sencha.com/login
[39]: http://www.sencha.com/forum/register.php
[40]: http://www.sencha.com/forum/
[41]: http://www.sencha.com/apps/
[42]: http://developer.sencha.com/
[43]: http://www.senchadevs.com/
[44]: http://market.sencha.com/
[45]: http://try.sencha.com/
[46]: http://docs.sencha.com/
[47]: http://docs.sencha.com/extjs/
[48]: http://docs.sencha.com/extjs3/
[49]: http://docs.sencha.com/gxt/3/
[50]: http://docs.sencha.com/touch/
[51]: http://docs.sencha.com/touch/1-1/
[52]: http://docs.sencha.com/touch-charts/
[53]: http://docs.sencha.com/architect/2/#!/guide
[54]: http://docs.sencha.com/animator/#!/guide
[55]: https://www.sencha.com/store/cart
[56]: http://www.sencha.com
[57]: http://twitter.com/share
[58]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-hero.png
[59]: http://www.mobilexweb.com/blog/safari-ios7-html5-problems-apis-review
[60]: http://webreflection.blogspot.com
[61]: http://druckit.wordpress.com/2013/09/24/about-sencha-touch-2-x-phonegapcordova-and-ios-7/
[62]: http://ejohn.org/apps/timers/
[63]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-1.png
[64]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-5.png
[65]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-2.png
[66]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-3.png
[67]: http://ie.microsoft.com/testdrive/performance/fishietank/
[68]: http://dl.dropboxusercontent.com/u/1865210/mindcat/canvas_perf.html
[69]: http://bl.ocks.org/stepheneb/1296930
[70]: http://cdn.sencha.io/img/20130924-iOS7/iOS7-4.png
[71]: http://themaninblue.com/experiment/AnimationBenchmark/svg/
[72]: http://cdn.sencha.io/img/20130924-iOS7.png

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
