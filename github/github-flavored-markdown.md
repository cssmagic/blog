# [译] GitHub 风格的 Markdown 语法

> * Original: [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown)
> * Translated by: [cssmagic](https://github.com/cssmagic)

GitHub uses what we're calling "GitHub Flavored Markdown" (GFM) for messages, issues, and comments. It differs from standard Markdown (SM) in a few significant ways and adds some additional functionality.

GitHub 使用一种被称为“GitHub 风格的 Markdown 语法”（GFM）来书写版本注释、Issue 和评论。它和标准 Markdown 语法（SM）相比，存在一些值得注意的差异，并且增加了一些额外功能。

If you're not already familiar with Markdown, you should spend 15 minutes and go over the excellent [Markdown Syntax Guide][9] at Daring Fireball.

如果你对 Markdown 还不是很熟悉，那就应该花 15 分钟到 Daring Fireball 去复习一下这个精彩的 [Markdown 语法指南][9]。（译注：本文末尾提供了简体中文版的 Markdown 教程。）

If you prefer to learn by example, see the following source and result:

如果你更愿意通过案例来学习，则不妨看看下列源码和渲染结果的对照：

* [Source][10]
* [Result][11]
* [源码][10]
* [渲染结果][11]

> **Tip**: On Markdown-enabled portions of the site, press **`m`** on your keyboard to display a cheat sheet.

> **提示**：在站内所有可用 Markdown 的场合，按 **`m`** 键可以显示快捷帮助。

## Differences from traditional Markdown

## 与传统 Markdown 的差异

### Newlines

### 换行

The biggest difference that GFM introduces is in the handling of linebreaks. With SM you can hard wrap paragraphs of text and they will be combined into a single paragraph. We find this to be the cause of a huge number of unintentional formatting errors. GFM treats newlines in paragraph-like content as real line breaks, which is probably what you intended.

GFM 引入的最大差异就是对换行的处理。在 SM 语法中，即使在一段文本中插入硬回车，这些文本仍然会被合并为一个段落。我们发现，这个特性导致了大量非预期的格式化错误。GFM 会把段落内容中的换行视为真正的换行，而这很可能正是你所期望的。

The next paragraph contains two phrases separated by a single newline character:

下面这个段落被一个换行符分隔成了两个短语：

	Roses are red
	Violets are blue

becomes

将被渲染为：

Roses are red
Violets are blue

### Multiple underscores in words

### 单词中的多个下划线

It is not reasonable to italicize just _part_ of a word, especially when you're dealing with code and names often appear with multiple underscores. Therefore, GFM ignores multiple underscores in words.

没有理由只把一个单词的 **一部分** 显示为斜体，尤其是当你在处理代码和那些经常出现多个下划线的名称时。因此，GFM 将忽略单词中的多个下划线。

	perform_complicated_task
	do_this_and_do_that_and_another_thing

becomes

将被渲染为：

perform_complicated_task
do_this_and_do_that_and_another_thing

### URL autolinking

### 链接自动识别

GFM will autolink standard URLs, so if you want to link to a URL (instead of setting link text), you can simply enter the URL and it will be turned into a link to that URL.

GFM 将自动为标准的 URL 加链接，所以如果你只想链接到一个 URL（而不想设置链接文字），那你简单地输入这个 URL 就可以，它将被自动转换为一个链接。（译注：Email 地址也适用于此特性。）

### Fenced code blocks

### 围栏式代码块

Markdown converts text with four spaces at the front of each line to code blocks. GFM supports that, but we also support fenced blocks. Just wrap your code blocks in \`\`\` and you won't need to indent manually to trigger a code block.

Markdown 会把每行前面空四格的文本转换为代码块。GFM 也支持这种语法，同时，我们还支持围栏式代码块。只要把你的代码块包裹在 \`\`\` 之间，你就不需要通过无休止的缩进来标记代码块了。

If you are indenting your code blocks with spaces, keep in mind that code within lists needs to be indented _eight_ times in order to be properly marked as a code block.

如果你使用空格来缩进代码块，请留意列表中的代码块需要缩进 **8** 个空格，以确保它会被正确地标记为代码块。

### Syntax highlighting

### 语法着色

We take code blocks a step further and add syntax highlighting if you request it. In your fenced block, add an optional language identifier and we'll run it through syntax highlighting. For example, to syntax highlight Ruby code:

我们在处理代码块方面更进一步，你可以为代码码指定语法着色效果。在围栏式代码块中，你可以指定一个可选的语言标识符，然后我们就可以为它启用语法着色了。举个例子，这样可以为一段 Ruby 代码着色：

	```ruby
	require 'redcarpet'
	markdown = Redcarpet.new("Hello World!")
	puts markdown.to_html
	```

We use [Linguist][12] to perform language detection and syntax highlighting. You can find out which keywords are valid by perusing [the languages YAML file][13].

我们使用 [Linguist][12] 来进行语言识别和语法着色。你可以在 [语言 YAML 文件][13] 中查证哪些语言标识符是有效的。

### Task Lists

### 任务清单

Further, lists can be turned into [Task Lists][14] by prefacing list items with `[ ]` or `[x]` (incomplete or complete, respectively).

不仅如此，列表还可以被转换为 [任务清单][14]，只需要为列表项的开头加上 `[ ]` 或 `[x]` 即可（分别表示未完成和已完成）。

	- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
	- [x] list syntax required (any unordered or ordered list supported)
	- [x] this is a complete item
	- [ ] this is an incomplete item

	- [x] 支持 @提到某人、#引用、[链接]()、**格式化** 和 <del>标签</del> 等语法
	- [x] 需要使用列表语法来激活（无序或有序列表均可）
	- [x] 这是一个已完成项目
	- [ ] 这是一个未完成项目

This feature is enabled for Issue and Pull Request descriptions, and comments. Task lists are also available in Gist comments, as well as Gist Markdown files. In those contexts, the task lists are rendered with checkboxes that you can check on and off.

这个特性会在 Issue 和 Pull Request 的描述和评论中启用。任务清单同样可用于 Gist 的评论和 Markdown 格式的 Gist。在这些场合，任务清单将渲染出复选框，供你勾选或清除。

See the [Task Lists blog post][14] for more details.

详情参见 [关于任务清单的博文][14]。

### Quick quoting

### 快速引用

Typing `r` on your keyboard lets you reply to the current issue or pull request with a comment. Any text you select within the discussion thread before pressing `r` will be added to your comment automatically and formatted as a blockquote.

按下键盘的 `r` 键将为当前的 Issue 或 Pull Request 添加评论。在按下 `r` 键之前，你在讨论区中选中的任何文本都将自动以一个块级引用的形式插入到你的评论中。（译注：这听起来只是一个便民功能，并不是语法上的差异。下面的一些功能也是这样。）

### Name and Team @mentions autocomplete

### @ 提到人名或团队名时的自动补全

Typing an `@` symbol will bring up a list of people or teams on a project. The list will filter as you type, so once you find the name of the person or team you are looking for, you can use the arrow keys to select it and then hit enter or tab to complete the name. For teams, just enter the @organization/team-name and all members of that team will get subscribed to the issue.

按下 `@` 符号将弹出一个列表，列出这个项目相关的人或团队。这个列表会随着你的输入不断匹配筛选，因此一旦你在列表中发现了你要找的人名或团队名，你就可以用方向键来选中它，然后按回车或 tab 键来补全。对于团队来说，只需要输入 @组织名/团队名，那么团队内的所有成员都将收到提醒。

The result set is restricted to repository collaborators and any other participants on the thread, so it's not a full global search. It uses the same fuzzy filter as the file finder and works for both usernames and full names.

列表的匹配范围仅限制在当前仓库的贡献者以及当前讨论的参与者，因此它并不是一个全局性的搜索。它和文件查找器使用相同的模糊筛选算法，并且同时适用于用户名和全名。

Check out the blog posts for more information about @mention autocompletes for [users][15] and [teams][16].

详情参见关于 @ 提到 [用户][15] 和 [团队][16] 时自动补全的博文。

### Emoji autocomplete

### Emoji 表情符号的自动补全

Typing `:` will bring up a list of emoji suggestions. The list will filter as you type, so once you find the emoji you're looking for, just hit tab or enter to complete the highlighted result.

按下 `:` 将会弹出一个表情符号的建议列表。这个列表会随着你的输入不断匹配筛选，因此一旦你在列表中发现了你要找的表情符号，你就可以按回车或 tab 键来补全当前高亮的那一项。

### Issue autocompletion

### Issue 的自动补全

Typing `#` will bring up a list of Issue and Pull Request suggestions. Type a number or any text to filter the list, then hit tab or enter to complete the highlighted result.

按下 `#` 将会弹出一个 Issue 和 Pull Request 建议列表。输入数字或任何文本可以不断筛选这个列表，然后按回车或 tab 键可以补全当前高亮的那一项。

### Zen Mode (fullscreen) writing

### 禅意模式（全屏书写模式）

Zen Mode allows you to go fullscreen with your writing. You'll find the Zen Mode button on comment, issue, and pull request forms across the site.

禅意模式允许你以全屏模式进行书写。在站内的评论、Issue 和 Pull Request 表单中，你都可以找到禅意模式按钮。

![Zen Mode button](https://f.cloud.github.com/assets/1231359/765983/e75e36a6-e847-11e2-93f0-ddcbb18b6bb3.png)

You can also use it when creating and editing files by using the Zen Mode button above the file box.

在创建或编辑文件时，你也可以在文件框顶部找到禅意模式按钮。

Zen Mode offers you two themes to choose from, light or dark. You can change which theme you are using with the switcher at the top right of the window.

禅意模式提供了两种主题可供选择，浅色版和深色版。你可以用窗口右上角的切换按钮来更换当前主题。

Check out the blog post for more information about [Zen writing mode][18].

详情参见 [关于禅意模式的博文][18]。

### References

### 事件引用

Certain references are auto-linked:

引用特定的事件将会自动创建链接：

	* SHA: 16c999e8c71134401a78d4d46435517b2271d6ac
	* User@SHA: mojombo@16c999e8c71134401a78d4d46435517b2271d6ac
	* User/Project@SHA: mojombo/github-flavored-markdown@16c999e8c71134401a78d4d46435517b2271d6ac
	* #Num: #1
	* User#Num: mojombo#1
	* User/Project#Num: mojombo/github-flavored-markdown#1

becomes

将被渲染为：

* SHA: [16c999e][19]
* User@SHA: [mojombo@16c999e][19]
* User/Project@SHA: [mojombo/github-flavored-markdown@16c999e][19]
* \#Num: [\#1][11]
* User\#Num: [mojombo#1][11]
* User/Project\#Num: [mojombo/github-flavored-markdown#1][11]

### Code

### 实现

The newline and underscore modification code can be seen in below.

关于换行与下划线的差异实现可参见 [这个 Gist](https://gist.github.com/mojombo/118964)。

If you find a bug in the rendering, please [contact support][2] and let us know.

如果你发现了渲染方面的 bug，请 [联系支持团队][2] 让我们知道。

***

## 译者后记

GitHub 的这篇文档组织得不是很有条理，也存在一些笔误（<del>已联系支持团队</del> 官方已修正）。

### 推荐阅读

* [Markdown 语法说明（简体中文版）](http://wowubuntu.com/markdown/)

   [2]: https://help.github.com/contact?article_slug=github-flavored-markdown
   [9]: http://daringfireball.net/projects/markdown/syntax
   [10]: http://github.github.com/github-flavored-markdown/sample_content.html
   [11]: https://github.com/mojombo/github-flavored-markdown/issues/1
   [12]: https://github.com/github/linguist
   [13]: https://github.com/github/linguist/blob/master/lib/linguist/languages.yml
   [14]: https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments
   [15]: https://github.com/blog/1004-mention-autocompletion
   [16]: https://github.com/blog/1121-introducing-team-mentions
   [18]: https://github.com/blog/1379-zen-writing-mode
   [19]: https://github.com/mojombo/github-flavored-markdown/commit/16c999e8c71134401a78d4d46435517b2271d6ac

***

> 原文版本：2013-07-07 （如果原文已更新，请提醒我。）

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/13)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
