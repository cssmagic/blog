# [译] 在 PageSpeed Insights 中分析移动网站（又名：如何做到一秒渲染一个移动页面）

> * Original: [Mobile Analysis in PageSpeed Insights - PageSpeed Insights - Google Developers](https://developers.google.com/speed/docs/insights/mobile)
> * Translated by: [cssmagic](https://github.com/cssmagic)

> **声明**：原文版权属于 Google，原文以 CC BY 3.0 协议发布，原文中的代码部分以 Apache 2.0 协议发布。中文翻译部分并非官方文档，仅供参考。

PageSpeed Insights analyzes a page to see if it follows our recommendations for making a page render in under a second on a mobile network. Research has shown that any delay longer than a second will cause the user to interrupt their flow of thought, creating a poor experience. Our goal is to keep the user engaged with the page and deliver the optimal experience, regardless of device or type of network.

PageSpeed Insights 可以分析一个网页是否遵循了我们的攻略，从而在移动网络上用不到一秒的时间完成一次页面渲染。研究显示，任何超过一秒钟的延迟都将打断用户的思维顺流状态，带来较差的体验。我们的目标是不论在任何设备或网络条件下，都要维持用户在网页中的沉浸状态，提供更好的体验。

It is not easy to meet the one second time budget. Luckily for us, the whole page doesn’t have to render within this budget, instead, **we must deliver and render the above the fold (ATF) content in under one second, which allows the user to begin interacting with the page as soon as possible**. Then, while the user is interpreting the first page of content, the rest of the page can be delivered progressively in the background.

想要满足一秒的时间预算并不是那么容易。不过幸运的是，我们并不需要在这个时间预算内渲染出整个页面，而是 **要在一秒以内传输并渲染出首屏内容，让用户尽快与页面互动**。然后，当用户在和首屏内容进行互动时，页面的剩余部分可以在后台持续加载完整。

（译注：原文中的“ATF”一词源自传统出版业，指的是报纸头版的中折线以上区域，这块黄金区域往往用来刊登最有吸引力的新闻。延伸到互联网领域，ATF 指的是页面中不需要滚动就可以看到的首屏内容。）

## Adapting to high latency mobile networks

## 适应高延迟的移动网络

Meeting the one second ATF criteria on mobile devices poses unique challenges which are not present on other networks. Users may be accessing your site through a variety of different 2G, 3G, and 4G networks. Network latencies are significantly higher than a wired connection, and consume a significant portion of our 1000 ms budget to render the ATF content:

在移动设备上达到“首屏秒开”的准则，需要面对其它网络所遇不到的独特挑战。用户可能正通过 2G、3G 或 4G 等各种各样的网络来访问你的网站。移动网络的延迟要明显高于有线连接，并且将消耗我们“首屏秒开”预算中的一大部分：

* 200-300 ms roundtrip times for 3G networks
* 50-100 ms roundtrip times for 4G networks
* 3G 网络的往返时间将消耗 200-300 ms
* 4G 网络的往返时间将消耗 50-100 ms

3G is the dominant network type around the world, and while 4G deployments are in progress around the world, you should still expect that the majority of users will be accessing your page on a 3G network. For this reason, we have to assume that each network request will take, on average, 200 milliseconds.

3G 是全球范围内占据统治地位的移动网络，而 4G 网络正在普及之中，你需要明白你的主流用户仍然在使用 3G 网络来访问你的页面。基于这个原因，我们不得不假设平均每次网络请求将消耗 200 ms。

With that in mind, let’s now work backwards. If we look at a typical sequence of communication between a browser and a server, 600 ms of that time has already been used up by network overhead: a DNS lookup to resolve the hostname (e.g. google.com) to an IP address, a network roundtrip to perform the TCP handshake, and finally a full network roundtrip to send the HTTP request. This leaves us with just 400 ms!

明白了这一点之后，我们来倒推一下时间。如果我们来分析一下浏览器与服务器之间一次典型的通信过程，会发现 600 ms 的时间就已经被网络本身消耗掉了：一次 DNS 查询用于将主机名（比如 google.com）解析为 IP 地址，一次网络往返用于发起 TCP 握手，以及最后一次完整的网络往返用于发送 HTTP 请求。我们就只剩下 400 ms 了！

![Render a mobile page in 1 second](https://f.cloud.github.com/assets/1231359/945972/52d28f44-0324-11e3-9977-270d62a17a09.png)

> #### [Figure 1] Render a mobile page in 1 second

> #### [图 1] 一秒渲染一个移动页面

> * DNS Lookup (200 ms)
> * TCP Connection (200 ms)
> * HTTP Request and Response (200 ms)
> * DNS 查询 (200 ms)
> * TCP 连接 (200 ms)
> * HTTP 请求与响应 (200 ms)

> 600 ms mandatory 3G network overhead which you cannot do anything about

> 这 600 ms 是不可避免的 3G 网络消耗，你对此无能为力。

> * Server Response Time (200 ms)
> * Client-Side Rendering (200 ms)
> * 服务器响应时间 (200 ms)
> * 客户端渲染 (200 ms)

> 400 ms which **you** can optimize by updating your server and structuring your page appropriately (what the tool tries to help you with)

> 这 400 ms 是你可以优化的，只要你合理地更新你的服务器，并且适当地构建你的页面（这正是 PageSpeed Insights 这个工具可以帮到你的）。

## Delivering the sub one second rendering experience

## 实现半秒渲染的体验

After subtracting the network latency, we are left with just 400 milliseconds in our budget, and there is still a lot of work to do: server must render the response, client-side application code must execute, and the browser must layout and render the content. With that in mind, the following criteria should help us stay within the budget:

在除去网络延迟之后，我们的预算只剩下区区 400 ms 了，但我们仍然还有大量的工作要做：服务器必须渲染出响应内容，客户端应用程序代码必须执行，而且浏览器必须完成内容的布局和渲染。了解了这些之后，下面这些准则将帮助我们控制住预算：

### (1) Server must render the response (< 200 ms)

### (1) 服务器必须在 200 ms 以内渲染出响应内容

Server response time is the time it takes for a server to return the initial HTML, factoring out the network transport time. Because we only have so little time, this time should be kept at a minimum - ideally within 200 milliseconds, and preferably even less!

服务器响应时间就是在除去网络传输时间之后，一台服务器首先返回 HTML 所花费的时间。因为我们剩下的时间实在太少了，这个时间应该控制在最低限度——理想情况下应该低于 200 ms，而且越少越好！

### (2) Number of redirects should be minimized

### (2) 重定向的次数应该减至最少

Additional HTTP redirects can add one or two extra network roundtrips (two if an extra DNS lookup is required), incurring hundreds of milliseconds of extra latency on 3G networks. For this reason, we strongly encourage webmasters to minimize the number, and ideally eliminate redirects entirely - this is especially important for the HTML document (avoid “m dot” redirects when possible).

额外的 HTTP 重定向会增加一次或两次额外的网络往返（如果需要再次查询 DNS 的话就是两次），这在 3G 网络上将导致几百毫秒的额外延迟。因此，我们强烈建议网站管理员们减少重定向的次数，而且最好完全消除重定向——这对 HTML 文档来说尤其重要（尽可能避免重定向到 “m.” 二级域名的行为）。

### (3) Number of roundtrips to first render should be minimized

### (3) 首次渲染所需的网络往返次数应该减至最少

Due to how TCP estimates the capacity of a connection (i.e. [TCP Slow Start](http://en.wikipedia.org/wiki/Slow-start)), a new TCP connection cannot immediately use the full available bandwidth between the client and the server. Because of this, the server can send up to 10 TCP packets on a new connection (~14KB) in first roundtrip, and then it must wait for client to acknowledge this data before it can grow its congestion window and proceed to deliver more data.

由于 TCP 在评估连接状况方面采用了一套特殊机制（参见 [TCP 慢启动](http://en.wikipedia.org/wiki/Slow-start)），一次全新的 TCP 连接无法立即用满客户端和服务器之间的全部有效带宽。在这种情况下，服务器在首次网络往返中，通过一个新连接（约 14kB）只能发送最多 10 个 TCP 包，接下来它必须等待客户端应答这些数据，然后才能增加它的拥塞窗口并继续发送更多数据。

Due to this TCP behavior, it is important to optimize your content to minimize the number of roundtrips required to deliver the necessary data to perform the first render of the page. Ideally, the ATF content should fit under 14KB - this allows the browser to paint the page after just one roundtrip. Also, it is important to note that the 10 packet ([IW10](http://tools.ietf.org/html/draft-hkchu-tcpm-initcwnd-01)) limit is a recent update to the TCP standard: you should ensure that your server is upgraded to latest version to take advantage of this change. Otherwise, the limit will likely be 3-4 packets!

考虑到 TCP 的这种行为，优化你的内容就显得十分重要。传输必要数据、完成页面首次渲染所需的网络往返次数应该减至最少。理想情况下，首屏内容应该小于 14KB——这样浏览器才能在一次网络往返之后就可以绘制页面。同时，还有一个关键点需要留意，10 个数据包上限（[IW10](http://tools.ietf.org/html/draft-hkchu-tcpm-initcwnd-01)）源自 TCP 标准的最近一次更新：你应该确保你的服务器软件已经升级到最新版本，以便从这次更新中获益。否则，这个上限将可能降到 3～4 个数据包。

### (4) Avoid external blocking JavaScript and CSS in above-the-fold content

### (4) 避免在首屏内容中出现外链的阻塞式 JavaScript 和 CSS

Before a browser can render a page to the user, it has to parse the page. If it encounters a non-async or blocking external script during parsing, it has to stop and download that resource. Each time it does that, it is adding a network round trip, which will delay the time to first render of the page.

浏览器必须先解析页面，然后才能向用户渲染这个页面。如果它在解析期间遇到一个非异步的或具有阻塞作用的外部脚本的话，它就不得不停下来，然后去下载这个资源。每次遇到这种情况，都会增加一次网络往返，这将延后页面的首次渲染时间。

As a result, the JavaScript and CSS needed to render the above the fold region should be inlined, and JavaScript or CSS needed to add additional functionality to the page should be loaded after the ATF content has been delivered.

结论就是，首屏渲染所需的 JavaScript 和 CSS 应该内嵌到页面中，而用于提供附加功能的 JavaScript 和 CSS 应该在首屏内容已经传输完成之后再加载。

### (5) Reserve time for browser layout and rendering (200 ms)

### (5) 为浏览器的布局和渲染工作预留时间 (200 ms)

The process of parsing the HTML, CSS, and executing JavaScript takes time and client resources! Depending on the speed of the mobile device, and the complexity of the page, this process can take hundreds of milliseconds. Our recommendation is to reserve 200 milliseconds for browser overhead.

解析 HTML 和 CSS、执行 JavaScript 的过程也将消耗时间和客户端资源！取决于移动设备的速度和页面的复杂度，这个过程将占用几百毫秒。我们的建议是预留 200 ms 作为浏览器的时间消耗。

### (6) Optimize JavaScript execution and rendering time

### (6) 优化 JavaScript 执行和渲染耗时

Complicated scripts and inefficient code can take tens and hundreds of milliseconds to execute - use built-in developer tools in the browser to profile and optimize your code. For a great introduction, take a look at our [interactive course for Chrome Developer Tools](http://discover-devtools.codeschool.com).

执行复杂的脚本和低效的代码可能会耗费几十或上百毫秒——可以使用浏览器内建的开发者工具来收集概况、优化代码。如果你想深入这个话题，不妨看看我们的[《Chrome 开发者工具交互教程》](http://discover-devtools.codeschool.com)。

> **Note**: The above is also not the complete list of all possible optimizations - it is a list of top mobile criteria to deliver a sub one second rendering time - and all other [web performance best practices](https://developers.google.com/speed/docs/insights/rules) should be applied. Check out [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights) for further advice and recommendations.

> **请注意**：以上并未列举出所有可能的优化方案——只列出了一些移动端达成半秒渲染时间的基本准则——其它所有的[网页性能最佳实践](https://developers.google.com/speed/docs/insights/rules)也应该运用起来。到 [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights) 来看看，获取进一步的建议和推荐方案。

> For a deep-dive on the above mobile criteria, also check out

> 如果需要深入探索上述移动端准则，请参阅：

> * Optimizing the Critical Rendering Path for Instant Mobile Websites ([slides](http://bit.ly/mobilecrp), [video](http://www.youtube.com/watch?v=YV1nKLWoARQ)).
> * Instant Mobile Websites: Techniques and Best Practices ([slides](http://commondatastorage.googleapis.com/io-2013/presentations/239-%20Instant%20Mobile%20Websites-%20Techniques%20and%20Best%20Practices.pdf), [video](https://www.youtube.com/watch?v=Bzw8-ZLpwtw))
> * 为极速移动网站优化渲染的关键路径 ([幻灯片](http://bit.ly/mobilecrp)、[视频](http://www.youtube.com/watch?v=YV1nKLWoARQ)).
> * 极速移动网站：技巧与最佳实践 ([幻灯片](http://commondatastorage.googleapis.com/io-2013/presentations/239-%20Instant%20Mobile%20Websites-%20Techniques%20and%20Best%20Practices.pdf), [视频](https://www.youtube.com/watch?v=Bzw8-ZLpwtw))

## FAQ

## 常见问题

### How do 4G networks impact above mobile criteria?

### 4G 网络对上述移动端准则有何影响？

Lower roundtrip latencies are one of the key improvements in 4G networks. This will help enormously, by reducing the total network overhead time, which is currently over 50% of our one second budget on 3G networks. However, 3G is the dominant network type around the world, and will be for years to come - you have to optimize pages with 3G users in mind.

较低的网络往返延迟是 4G 网络的一处关键改良。减少所有的网络损耗时间对网页性能将有巨大帮助，而目前在 3G 网络上这些损耗就占用了我们一秒预算中的大半时间。不管怎样，3G 仍然是全球最主流的移动网络，并且在今后几年都将如此——我们在优化网页时不得不把 3G 用户放在心上。

### I am using a JavaScript library, such as JQuery, any advice?

### 我正在使用一个 JavaScript 类库，比如 jQuery，有什么建议吗？

Many JavaScript libraries, such as JQuery, are used to enhance the page to add additional interactivity, animations, and other effects. However, many of these behaviors can be safely added after the ATF content is rendered. Investigate moving the execution and loading of such JavaScript until after the page is loaded.

大多数 JavaScript 类库，比如 jQuery，通常用来增强页面、提供附加的交互、动画和其它效果。但是，大多数这些行为可以安全地在首屏渲染之后再加入进来。研究一下是否可以把这些 JavaScript 的加载和执行推迟到页面加载之后。

### I am using a JavaScript framework, to construct the page, any advice?

### 我在正使用一个 JavaScript 框架来渲染整个页面，有什么建议吗？

If the content of the page is constructed by client-side JavaScript, then you should investigate inlining the relevant JavaScript modules to avoid extra network roundtrips. Similarly, leveraging server-side rendering can significantly improve first page load performance: render JS templates on the server, inline the results into HTML, and then use client-side templating once the application is loaded.

如果页面内容是由客户端 JavaScript 来渲染的，那么你应该研究一下是否可以把相关的 JavaScript 模块都内嵌进来，以免产生额外的网络往返开销。同样，利用服务器端渲染可以显著地改善首次页面加载的性能：在服务器端渲染 JS 模板，并内嵌到 HTML 中，然后一旦应用程序加载完成就立即在客户端渲染模板。

### How will SPDY and HTTP 2.0 help?

### SPDY 和 HTTP 2.0 协议会有什么帮助？

SPDY and HTTP 2.0 both aim to reduce latency of page loads by making more efficient use of the underlying TCP connection (multiplexing, header compression, prioritization). Further, server push can further help improve performance by eliminating extra network latency. We encourage you to investigate adding SPDY support on your servers, and switching to HTTP 2.0 once the standard is ready.

SPDY 和 HTTP 2.0 协议的目标都是通过更有效地利用底层的 TCP 连接（多路复用、头压缩、优先化处理），来减少页面的加载延迟。而且服务器 push 通过消除额外的网络延迟，可以进一步促进性能的改善。我们建议你为服务器增加对 SPDY 协议的支持，并且当 HTTP 2.0 标准就绪之后就立即切换过去。

### How do I identify critical CSS on my page?

### 如何识别页面中的要害 CSS？

In Chrome Developer Tools, open the Audits panel, and run a Web Page Performance report, in the generated report, look for Remove unused CSS rules. Or use any other third party tool, or script, to identify which CSS selectors are applied on each page.

在 Chrome 开发者工具中，打开审计（Audits）面板，然后运行一次网页性能（Web Page Performance）报告。在生成的报告中，找一下“删除未使用的 CSS 规则（Remove unused CSS rules）”。也可以使用其它第三方工具或脚本，来识别每个页面分别应用了哪些 CSS 选择符。

### Can these best practices be automated?

### 这些最佳实践可以自动化吗？

Absolutely. There are many commercial and open-source web performance optimization (WPO) products which can help you meet some or all of the criteria above. For open-source solutions, take a look at the [PageSpeed optimization tools](https://developers.google.com/speed/pagespeed/optimization).

当然可以。有很多商业的或开源的网页性能优化（WPO）产品都可以帮你达成上述部分或全部准则。对于开源解决方案，不妨看看 [PageSpeed 优化工具](https://developers.google.com/speed/pagespeed/optimization)。

### How do I tune my server to meet these criteria?

### 我怎样调整我的服务器来符合这些准则？

First, ensure that your server is running an up-to-date version of the operating system. In order to benefit from the increased initial TCP congestion window size (IW10), you will need Linux kernel 2.6.39+. For other operating systems, consult the documentation.

首先，确保你的服务器正在运行最新版的操作系统。为了从 TCP 初始拥塞窗口数量的增加（IW10）中获益，你需要 2.6.39+ 版本的 Linux 内核。对于其它操作系统，请查阅相关文档。

To optimize server response time, instrument your code, or use an application monitoring solution to identify your bottleneck - e.g., scripting runtime, database calls, RPC requests, rendering, etc. The goal is to render the HTML response within 200 milliseconds.

为了优化服务器的响应时间，请测评你的代码性能，或使用监控程序来发现性能瓶颈——比如脚本运行时、数据库调用、RPC 请求、渲染等等。最终目标就是在 200 ms 内渲染出 HTML 响应内容。

### What about Content Security Policy?

### 内容安全策略（Content Security Policy）怎么办？

If you are using CSP, then you may need to update your default policy.

如果你正在使用 CSP，那么你可能需要更新你的默认策略。（译注：CSP 是一项用于防范 XSS 的安全性规范，具体参见 [Content Security Policy - 维基百科](http://en.wikipedia.org/wiki/Content_Security_Policy)。）

First, inline CSS attributes on HTML elements(e.g., `<p style=...>`) should be avoided where possible, as they often lead to unnecessary code duplication, and are blocked by default with CSP (disabled via “unsafe inline” on “style-src”). If you have inline JavaScript, then you will need to update the CSP policy with “unsafe-inline” to enable its execution. By default, CSP will block all inline script tags.

首先，尽可能避免在 HTML 元素中内联 CSS 属性（比如这样 `<p style=...>`），因为它们常常导致不必要的重复代码，而且默认会被 CSP 拦截（对 `style-src` 字段使用 `unsafe-inline` 指令可以禁用此类拦截）。如果你使用了内联的 JavaScript，那么你需要在 CSP 策略中使用 `unsafe-inline` 指令来令其执行。默认情况下，CSP 将拦截所有内联脚本标签。（译注：这里的“内联 JavaScript”包括多种形态的 HTML 内部的脚本代码，包括类似 `<script>foo();</script>` 这样的内嵌脚本标签、`<a href="javascript:foo();">` 这样的伪协议 URL、以及 `<a href="#" onclick="foo();">` 这样的事件处理属性。）

Have more questions? Please feel free to ask and provide feedback in our discussion group at [page-speed-discuss](http://groups.google.com/group/page-speed-discuss).

还有其它问题？请在我们的 [page-speed-discuss](http://groups.google.com/group/page-speed-discuss) 讨论组内随意提问或提供反馈。

***

> 原文版本：2013-08-06 （如果原文已更新，请提醒我。）

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/20)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
