# 实战中踩到的 JSON 小坑

前端工程师对 JSON 是再熟悉不过了。它语法简单，表现力强，常用于前后端的数据交换。

在实战中，我也遇到过跟 JSON 有关的小坑。

## JSON 数据中的 tab 字符

### 背景

某日在手机版的商品详情页发现了一个隐蔽的 bug，在 iOS 5 下并不会触发，但在当前最新版的 Chrome、Firefox 和 iOS 6+ 下可以重现。故障表现为个别详情页的脚本会挂掉，无法工作。

### 分析

经过简单的隔离分析之后，把疑点定位到某个 JSON 的解析操作上。该页面的脚本使用了浏览器原生的解析 JSON 的方法 `JSON.parse()`，但奇怪的是，此方法偶尔会抛出异常，导致后续代码无法执行。（示意如下）

![json-traps-in-practice-tab-1](https://f.cloud.github.com/assets/1231359/1215544/b014571e-266a-11e3-9e4f-6b92f6a22011.png)


进一步分析发现，与 iOS 5 相比，iOS 6（以及其它较新的浏览器）更严格地遵守了 JSON 规范——如果 JSON 数据中需要出现 tab 字符，必须是经过转义的（`"\t"` 或 `"\u0009"`）；如果是直接出现，则被认为是非法字符，`JSON.parse()` 方法会报错。

（相关的讨论可以参见 [这里](https://code.google.com/p/chromium/issues/detail?id=52560)，其中提到了 JSON 的规范 [RFC4627](http://www.ietf.org/rfc/rfc4627.txt)。）

### 后续

JSON 数据中怎么会出现 tab 字符呢？

![json-traps-in-practice-tab-2](https://f.cloud.github.com/assets/1231359/1215545/b453dc82-266a-11e3-94f5-6788739adbd1.png)

原来，详情页是以这个 JSON 为载体，将后端数据传递给前端脚本；而这个 JSON 是由 PHP 代码手工拼接的，其中的某个字段是从数据库中直接读取输出的。运营同学在从 Excel 向数据库导入数据时，很可能不小心在数据中混入了一些 tab 字符。

所以，这也算是一个教训，对于那些人工生成的字符数据，还是需要做一定的过滤和校验。在这个案例中，更好方式应该是先把数据存入一个 PHP 数组，再用 `json_encode()` 将其编码为 JSON 字符串，以确保 JSON 数据的规范性。

## JSON 键名中的空格

### 背景

某天，某后端同学声称，他已经按需求开发完成了一个 API。好的，在浏览器里跑一下，打开 Firebug 查看一下 Ajax 返回的 JSON 数据，看起来没问题。

![json-traps-in-practice-space-1](https://f.cloud.github.com/assets/1231359/1215548/b7d956c0-266a-11e3-8696-8a3286bcde60.png)

但在渲染这块数据的时候，发现 `thumbnail` 字段总是无法输出正确的值，总是显示 `undefined`。难道是拼写有误？我从 Firebug 的 JSON 视图（上图）中复制粘贴过来还是照样出错。真是怪事！

### 分析

在格式化过的 JSON 视图中看不出问题，那就进一步追查原始数据吧。于是把 Ajax 的返回数据切换到原码视图，搜索 `thumbnail` 关键字，终于发现问题所在。

![json-traps-in-practice-space-2](https://f.cloud.github.com/assets/1231359/1215549/bc597e5a-266a-11e3-8185-66baadccd380.png)

原来这个字段的键名尾部有一个空格。空格也是键名的一部分，我当然无法输出这个带尾巴的字段了。

后端哥哥，你玩儿我呢吧？

### 后续

后端哥哥把那个空格去掉，这没什么好说的。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/29)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
