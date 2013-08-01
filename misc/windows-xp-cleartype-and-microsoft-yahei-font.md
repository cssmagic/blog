# Windows XP、ClearType 和微软雅黑字体的那点事

> 本文的部分内容整理自我对此问题的解答： [XP 中微软雅黑的安装率是多少？哪个版本的更新包中带了微软雅黑字体？ClearType 在 XP 中是自动打开的吗？ - 知乎](http://www.zhihu.com/question/19920765/answer/13383799)

## 背景

> 多年以来，中文网页设计师一直处于一种“字体匮乏”的窘境之中。

> 这是因为从中文 Windows 的第一个版本 3.2 开始，在相当长的一段时期内，系统自带的中文字体就仅仅局限于苍白单调的“宋黑仿楷”。加上长期以来 Windows 在字体渲染技术方面的严重缺失，使得设计师在为中文网页选择正文字体时，基本上就只有 **中易宋体的点阵形态** 这唯一的选择。

> 直到 Vista 携微软雅黑字体横空出世，中文网页设计师才仿佛看到了世界的曙光。

> 微软雅黑脱胎于方正集团旗下的新锐字体“兰亭黑”家族，不仅字体的间架结构针对屏幕阅读场景进行改造，同时还针对液晶屏幕下小字号的显示效果进行逐字的笔划微调工作（hinting），以保证最终为用户呈现出清晰、平滑、易于阅读的显示效果。

> 因此，微软雅黑一经推出，便在中文网页设计界引发不小的震动。网页设计师和前端工程师们不禁打起它的主意——如何将微软雅黑字体广泛、安全地应用到网页设计中去？或者说，微软雅黑字体的普及率究竟如何？

## 微软雅黑在 XP 中的安装率

微软雅黑是 Vista 及更高版本 Windows 的标配字体，但不是 XP 的标配字体。XP 的任何一个 SP 或更新包都没有（将来也不太可能）包含它。

XP 系统上的微软雅黑字体，通常有两种来源：

* 用户主动下载安装。
* 安装 Office 2007 以上版本时自动获得。

![windows-xp-cleartype-and-microsoft-yahei-font-0](https://f.cloud.github.com/assets/1231359/792942/94590f26-ebd0-11e2-9bbc-f4ef1263c7b6.png)

由此可见，稍微“高端”一些的 XP 用户都有可能安装了微软雅黑字体，但 **具体比率不详**。

对此，我的建议是：

* 考虑这个比率不如考虑目标受众群的划分和取舍。
* 在一定程度上考虑好无雅黑情况下的平稳退化。

## 微软雅黑与 ClearType

ClearType 是微软开发的 **次像素字体渲染** 技术，这项技术的本质是充分利用液晶显示屏单颗像素内的 RGB 三基色的次像素（sub-pixel，也称作“亚像素”或“子像素”）、独立控制每颗次像素的明暗度，在次像素的级别进行字体的渲染和显示，从而令字体在水平方向上的渲染分辨率达到了原来的三倍。

微软雅黑字体只有在使用 ClearType 技术进行渲染时，才会达到最佳视觉效果。下面的图片展示了不同情况下微软雅黑字体的渲染效果：

1. 已启用 ClearType 渲染方式
2. 已启用传统的字体平滑渲染方式
3. 未启用任何字体平滑技术

![windows-xp-cleartype-and-microsoft-yahei-font-1](https://f.cloud.github.com/assets/1231359/792943/9737be4a-ebd0-11e2-9f89-034aeba0c0fa.png)

从 Vista 开始，Windows 系统默认开启 ClearType 特性。但 Windows XP 是否支持 ClearType 字体渲染模式呢？

## ClearType 在 XP 中的启用率

Windows XP 原生支持 ClearType，但可能是出于对性能的考虑，默认是关闭状态。用户可以通过以下步骤在 XP 中启用 ClearType：

> 控制面板 → 显示 → 外观 → 效果 → 使用下列方式使屏幕字体的边缘平滑：清晰

![windows-xp-cleartype-and-microsoft-yahei-font-2](https://f.cloud.github.com/assets/1231359/792944/98e618d6-ebd0-11e2-872e-93c9b1a4005b.png)

XP 用户还可以通过安装微软提供的 ClearType 设置工具（[ClearType Tuner PowerToy](http://www.microsoft.com/typography/ClearTypePowerToy.mspx)）来获得对 ClearType 效果的更多控制。

![windows-xp-cleartype-and-microsoft-yahei-font-3](https://f.cloud.github.com/assets/1231359/792945/9eaea29c-ebd0-11e2-9a6b-356cb11c3a41.png)

那么，在 XP 中手动打开 ClearType 的用户比率有多少？这个数字恐怕不是很乐观。但是幸运的是，IE 7.0 及以上版本都是在自身视口内强制开启 ClearType 的（哪怕你用的不是液晶显示器）。

而 XP 下的非 IE 用户呢？或许你可以假设他们都比较“高端”，已经给自己的 XP 手动开启 ClearType 了。

## 结语

在国内，Windows XP 操作系统目前仍然拥有不可忽视的用户数量。如果要在网页中使用微软雅黑作为主力字体，我们不得不考虑 XP 环境下所存在的一些不确定因素。

那么，我们有没有可能对 XP 用户的这些情况进行针对性的探测和统计，以便根据数据来做决策呢？

对于用户的 ClearType 设置情况，网页中的 JavaScript 脚本无法获取。而对于用户是否安装了微软雅黑字体，实际上是有探测方法的，有兴趣的朋友请继续关注后续文章。

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/15)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
