# UTM 参数、URL 和 HTML 实体的那点事

> 这篇文章是写给运营同学的科普文。

## 源起

想写这篇文章，是因为最近在 GA 中发现了一些问题。

EmarSys 是公司新签约的 EDM 服务商，在 GA 中已经可以看到最新一期 EDM 带来的流量。但它的媒介参数似乎不正确，理论上应该设置为 `email`。

![图1](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-01.png)

运营部的同学认为提供给 EDM 的链接不会有错，于是我深入分析之后便有了这篇文章。写完它，我以后应该就不需要口头再解答很多问题了。

***

## UTM 参数

UTM 参数的作用这里暂不赘述。我们先看一个正常的、加了 UTM 参数的链接（URL），它通常是这样的：

`http://www.foobar.com/?utm_source=google&utm_medium=cpc&utm_campaign=test&utm_term=test`

简单小结一下参数规则：

* UTM 参数可能有一个或多个。
* 每个 UTM 参数由参数名和参数值组成，使用等号（`=`）连接。
* 多个 UTM 参数之间使用 and 字符（`&`）连接。
* 所有参数使用问号（`?`）附加到原始链接的尾部。
* （其它略微高级一点的规则与主题无关，暂且略过。）

实际工作中，建议使用[专门的链接生成工具](https://support.google.com/analytics/answer/1033867?hl=en)来为链接添加 UTM 参数，避免手工失误。

当有人用浏览器访问这个 URL 时，UTM 参数就会发挥作用。

![图2](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-02.png)

好的，如果你只是提供一个最终链接给一个靠谱的 Agency，那么直接提供上面的链接就可以了。但是如果是自己制作 EDM，情况会稍稍复杂一些。

## HTML 实体

EDM 的本质实际上是一个 HTML 页面（或一段 HTML 代码），理论上它需要遵守 HTML 规范。

我们在上面提到的 `&` 字符在 HTML 代码中是一个特殊字符，有特殊用途，它不能直接代表它自己。如果要在 HTML 页面中表达这个字符时，你需要在源代码中把它写成 `&amp;`。这种写法叫做“HTML 实体”，其它一些字符也需要以实体的形式来写入 HTML 代码中（比如大于号 `>` → `&gt;`、人民币符号 `￥` → `&yen;` 等等）。

所以，如果要把链接加到 EDM 中的某个元素身上，在 HTML 源码中就需要这样写（摘自 EDM 源文件）：

![图3](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-03.png)

当然，用户并不会接触到源代码。用户通常是使用邮件客户端（比如 FoxMail、Outlook 等）或浏览器来查看邮件，这些程序都是遵循 HTML 规范来开发的，它们可以正确地解析实体，将其转换为本来的字符。

所以，虽然我们在源代码中看到链接使用的是 `&amp;` 实体，但邮件在显示的时候，这些实体会被解读为 `&` 字符。也就是说，用户在查看邮件的时候，会得到一个正确的链接。如下图（EDM 源文件在浏览器中的效果）：

![图4](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-04.png)

***

好，文章正文到此已经结束。不过文章开头的问题还没有解决，所以我们继续。

## 继续分析问题

到目前为止，事情看起来都还不错，对吧？

可是，我们并不是直接发送 HTML 文件，而是通过 EDM 投放系统（比如目前刚刚开始使用的 EmarSys）来完成邮件的发送。一封 EDM 从我们做好的 HTML 页面到发送到用户的邮箱中，经历了一些处理。其中一个相当重要的处理步骤，是把页面中原有的链接（通常已经加上了 UTM 参数）“包装”起来。也就是说，并不会把原链接直接提供给用户，而是把原链接替换成一个“中转链接”（格式大约是 `http://link2.foobar.com/u/nrd.php?p=XXX`）。

我们观察一下收到的 EDM 邮件，可以发现这一点：

![图5](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-05.png)

这个中转链接会把用户带到真正的目标页面。（为什么 EmarSys 要使用这种中转链接？其实几乎所有成熟的 EDM 服务商都会这样做，这样做有一些好处，不过这里也不赘述了。）

## 发现问题

铺垫了这么久，终于要发现真相了——问题就出在 EmarSys 的系统和这个中转链接。

这个系统并不能正确识别 HTML 页面中的实体，在生成中转链接的过程中，并不能把原链接中的 `&amp;` 实体解析为它的本意 `&` 字符，而是直接理解为实体的字面。这样一来，用户会被中转链接带到一个错误的、不是我们本意的地址。

下图是我对中转链接的跟踪，它通过 HTTP 重定向（`302`）实现跳转，跳转目标由 `Location` 字段指定：

![图6](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-06.png)

发现问题了吧？如果点击 EDM 中的链接，用户真正到达的地址是这样的：

![图7](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-07.png)

不要小看这几个字符的差异，这个 URL 的实际效果已经不是我们最初期望的那样了。如果你分析一下，会发现这个页面（除了 `utm_source` 参数以外）真正接收到的是 `amp;utm_media` 这样的参数，而不是原本的 `utm_media` 等等。参数传错了，GA 当然也就收不到正确的值，所以实际上不仅媒介参数有问题，活动、内容、关键字参数都没有收到：

![图8](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-08.png)

![图9](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-09.png)

![图10](http://www.cssmagic.net/blog/pic/201306/utm-param-url-and-html-entity-10.png)

## 解决方案

目前我们所能做的：

1. 向 EmarSys 反馈此问题，要求修复此缺陷。
2. 在此问题修复之前，我们在 HTML 代码的链接中，不使用实体，直接使用 `&` 字符。（需要注意的是，这样编写的 HTML 代码是不规范的，我们这样做仅仅是为了绕过 EmarSys 系统的缺陷。）

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/12)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
