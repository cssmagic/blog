# CSS 类名的单词连字符：下划线还是横杠？

> 本文的部分内容整理自我对此问题的解答： [命名 CSS 的类或 ID 时单词间如何连接？ - 知乎](http://www.zhihu.com/question/19748433/answer/14666716)

## 问题

> CSS 类或 ID 命名时单词间连接通常有这几种写法：

> * 驼峰式： `solutionTitle`、`solutionDetail`
* 用横杠连接： `solution-title`、`solution-detail`
* 下划线连接： `solution_title`、`solution_detail`

> 应该采用哪种写法呢？选择的时候是出于个人习惯还是有别的考虑？

> 看了下豆瓣，美团，淘宝的源码，都是采用 `solution_title` 的写法。

## 我的回答

首先定个性，这是个纯粹的“代码风格”问题。

什么是“代码风格”问题？有一些特征：

* 技术规范不会硬性规定。虽然规范有时可能会提供指导性的建议，或者有时会有行业大牛出来鼓吹最佳实践。

* 个性化十分明显。也就是萝卜青菜各有所爱、公说公有理婆说婆有理，永无定论。

扯完之后说一下我自己的习惯：

#### 以前用下划线

主要原因是在编辑器中双击可以选中；另外自己觉得下划线好看（纯个人喜好）。除此以外可能还有一点“小白式谨慎”（避免与 CSS 属性名/值弄混、避免与减号弄混），或者我的启蒙教材就是使用下划线的。

#### 现在主要使用连字符

后来逐渐接手或参与了一些别人的项目，接触过各种代码风格。在老外的一些项目中接触到大量的使用连字符的命名，看多了感觉也不难看。在编辑器中也可以通过“双击并拖动”来选中，所以就逐渐过渡到了连字符。

#### 在特殊场合用驼峰式

驼峰式写法输入不方便、引入了大小写的复杂度、可读性无优势，因此很少在日常开发中使用。而正因为如此，我目前主要在一些框架级的类名中使用，以便于日常开发的命名习惯区分开，避免无意中污染框架级样式的可能性。

### 关于标准

有网友提到：

> HTML 和 CSS 语法中，无论是属性名和值，用到连接符的地方都是 `-` 没有 `_`。Follow 标准有益无害。

这种说法我并不赞同。因为“follow 标准”一说没有根据，而且逻辑不清。

我们很容易理清一件事——给元素的 class 和 id 命名，本质上是给 HTML 标签的 class 与 id 属性写入值。HTML 的 **标签属性值** 的合法性与 HTML 标签属性名、CSS 属性的名/值的命名习惯有关系吗？

说到“标准”，其实我也完全不知道 class 和 id 的合法值是什么、不知道下划线是否合法，甚至记不太清楚 class 与 id 的值是否是大小写敏感的。为此，我查阅了现行规范 HTML 4.01 和 CSS 2.1 的部分章节。CSS 2.1 是这样说的：

> In CSS, identifiers (including element names, classes, and IDs in selectors) can contain only the characters [a-zA-Z0-9] and ISO 10646 characters U+00A0 and higher, plus *__the hyphen (-) and the underscore (_)__*; ...

也就是说，用下划线来连接多个单词作为 class 或 id 的值，是合法的。

（贺师俊老师提示道：CSS 1 和 2 的规范在这一点上有错误，没有把下划线加进去；直到 CSS 2.1 中，这个问题才被修正。）

## 其它观点

### 关于可读性

贺师俊老师（@hax）提出了一个容易被忽略但实际上很重要的因素：

> `-` 比 `_` 有一点好的地方是 `_` 有时候会难以分辨，就好像空格一样。而 class 里面有没有空格是挺重要的。比较以下三种用法：

> * `<div class="a_very_very_very_long long_class short_class">`
* `<div class="a-very-very-very-long long-class short-class">`
* `<div class="aVeryVeryVeryLong longClass shortClass">`

### 关于编辑器

很多同学提到了不同的单词连接方式对选择操作的影响，比如双击可以直接选中用 `_` 连接的多个单词，但用 `-` 连接的多个单词则无法全部选中，选区会在 `-` 处终止，即只能选中一个单词。

#### Sublime Text

对此，一丝同学（@yisibl）在 [微博](http://weibo.com/1397442732/AbFJggD7F) 做了科普：

> CSS 命名用连字符 `-` 不能双击选中的问题一直是一个伪命题，这是编辑器的问题，因为这个而选用下划线 `_` 实在有些牵强。在 Sublime Text 2 的全局配置文件 `Preferences.sublime-settings` 中找到 `word_separators` 字段，删掉其中的 `-` 即可双击选中一连串的多个单词。

![hyphen-in-css-class-dash-or-underscore-sublime-text](https://f.cloud.github.com/assets/1231359/1259347/aad93850-2be2-11e3-8df5-1973834307cb.png)

#### Vim

也有一位 [潘魏增同学](http://www.zhihu.com/question/19748433/answer/12870737) 提供了 Vim 的配置方法：

> 如果使用 vim，可以设置 set iskeyword+=-，这样就可以匹配选中以 - 连接的关键词，search 和 mark 的时候会比较方便。

（抱歉，这里只是纯转发，暂时无法验证。）

#### UltraEdit

我在 Windows 下一般用 UltraEdit 干活，它有一种操作叫做“Ctrl + 双击”。而且我们可以设置此操作的分界符，很灵活。

![hyphen-in-css-class-dash-or-underscore-ultraedit](https://f.cloud.github.com/assets/1231359/1259348/ad536d1c-2be2-11e3-848a-2952911abf1d.png)

#### 鼠标选择

如果你的编辑器不支持上述配置或操作，要想一次性选中以 `-` 连接的多个单词，其实也是有解决方案的：双击的最后一击先不要松开，再左右拖动就可以以单词为单位扩张选区。（这种选择方式几乎适用于所有编辑器，而且 Windows 和 Mac 通吃。）

实际上我并不建议像前面几种方法那样在编辑器中取消 `-` 的分界符身份，而是建议使用这种“双击 + 拖动”的方法来选择任意数量的单词。因为，某些时候你只想选中 `one-two-three` 中的 `one-two` 或 `two-three` 或单个单词，那么这种方法显然更自由更精确——想选少选少，想选多选多。

#### --- Bonus Track ---

如果你在使用 WebStorm（或它的兄弟 IDE），就不要用鼠标点来点去了，不优雅。

你可以把光标移到某个单词上，用 `Ctrl + W`（在 Mac 下是 `Cmd + W`）快捷键就可以选中当前单词；更神奇的是，这个快捷可以连续使用，可以不断把选区扩张到更大的语法单元：单词 → 一串单词 → 整个字符串（或语句） → 对象（或函数作用域） → …… → 整个文件。

（唯一不便的是，这个快捷键在其它程序中是关闭当前窗口的操作，容易混淆，建议在 IDE 中给这个操作分配其它的快捷键。）

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/42)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
