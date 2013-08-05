# [译] [PJA] [508] 基于 CommonJS、npm、Grunt 和 Browserify 构建客户端代码

> * Original: [Building Client-Side Code With CommonJS, npm, Grunt, and Browserify - Chapter 5. Modules - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#building_clientside_code_with_commonjs_npm_grunt_and_browserify)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Building Client-Side Code With CommonJS, npm, Grunt, and Browserify

## 基于 CommonJS、npm、Grunt 和 Browserify 构建客户端代码

There are a variety of competing standards for module management with JavaScript, but due to the popularity of Node, by far the most established is npm & CommonJS. There have been efforts to create a similar combination of features for the client-side, including AMD, [Yeoman][14], and [Bower][15], but none of them can compete with the simplicity and package availability of npm and CommonJS modules.

市面上有很多种相互竞争的 JavaScript 模块管理标准，但由于 node 的流行，到目前为止，npm 和 CommonJS 是最即成事实的。仍然还有很多努力来为客户端创建一个类似的特性组合，包括 AMD、[Yeoman][14] 和 [Bower][15]，但它们中还没有谁可以在易用性和包的可用性方面与 npm 和 CommonJS 模块系统相媲美。

To demonstrate, take a look at how you might build a minimal client-side guestlist app (just in case you need a bouncer at your birthday party).

为了演示一下，来瞧一瞧你会怎样构建一个最小化的客户端的来宾清单应用（假设你的生日聚会有保镖守门）。

### Defining the App

### 定义你的 App

Most new software projects use agile development methods to produce quick software creation and enhancement iterations. In agile software, the time between releases is measured in days or weeks rather than months or years. To learn more about agile, see *The Art of Agile Development: Pragmatic guide to agile software development*, by James Shore, Chromatic.

大多数新的软件项目使用敏捷开发方式来提高软件的开发速度和迭代频率。在敏捷软件中，版本更迭的周期往往只是数天或数周，而不是相隔几个月或几年。如果你想进一步了解敏捷开发，参见由 James Shore 和 Chromatic 编写的《The Art of Agile Development: Pragmatic guide to agile software development》一书。

Typically when you set out to build an app, you'll start with a list of _user stories_. You can use those scenarios to come up with _acceptance criteria_, which can be further distilled into functional unit tests.

通常当你着手构建一个应用时，你会从一系列 *用户故事* （User Story）开始。你可以从这些场景中总结出 *验收准则*，它们可以进一步提练为功能性的单元测试。

A user story is a short, simple description of some action that your user might want to perform. For example, "As an event organizer, I want to check arriving guests against the guestlist." User stories should always contain a role along with the story. Answer the questions, "who is performing the action?" and "what is the user trying to accomplish?"

一个用户故事是对于你的用户可能想要完成的某些动作的简短描述。举个例子，“作为一个活动的组织者，我想通过来宾清单来核对客人的出席情况”。用户故事应该总是包含一个角色。回答这些问题，“谁来完成这个动作”以及“用户想要完成什么任务”。

To express this example story in code, you'll need a list element with clickable links in list items. The unit tests might look something like this:

为了用代码来表述这些示例故事，你将会需要一个列表元素，它的列表项还将包含可点击的链接。这个单元测试看起来可能是这样的：

Example 5-2. `guestlist/test/test.js`

示例 5-2. `guestlist/test/test.js`

```js
var $list = $('http://chimera.labs.oreilly.com#guestlist-view'),
  checkedinClass = 'icon-check',
  guestSelector = '.guest';

test('Guestlist', function () {
  ok($list.length,
    'List element should have guests.');
});

test('Guests', function () {

  // Grab the first guest from the list
  var $guest = $($list.find(guestSelector)[0]),
    guestExists = !!$guest[0];

  // Simulate click
  $guest.click();

  ok($guest.hasClass(checkedinClass),
      'Should be checked on click');

  $guest.click();

  // To avoid a false positive, make sure
  // you have a guest element to test against.
  ok(guestExists &amp;&amp; !$guest.hasClass(checkedinClass),
      'Should toggle off when clicked again');
});
```

You'll need to include that in your QUnit HTML file:

你将需要在你的 QUnit HTML 文件中加入以下代码：

Example 5-3. `guestlist/test/index.html`

示例 5-3. `guestlist/test/index.html`

```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>
	<section>
		<h1 id="qunit-header">QUnit Test Suite</h1>
		<h2 id="qunit-banner"></h2>
		<div id="qunit-testrunner-toolbar"></div>
		<h2 id="qunit-userAgent"></h2>
		<ol id="qunit-tests"></ol>
	</section>

	<section id="container"></section>

	<script src="jquery.js"></script>
	<script src="qunit.js"></script>
	<script src="../public/app.js"></script>
	<script src="test.js"></script>
	<link href="qunit.css" rel="stylesheet"></style>

</body>
</html>
```

These tests will obviously fail at first. That's a good thing. If you can see your tests fail before you make them pass, you eliminate the possibility that your test results are showing you false positives. For this reason, _TDD_ (Test Driven Development) proponents advocate writing your unit tests before you implement the code that they're written against. Because you're writing from an API user perspective when you write tests first, using test driven development forces you to write more modular, testable code. You may also notice that your APIs are easier to use because you're not worried about implementation details when you write your tests -- you won't accidentally leak implementation details into your APIs.

这些测试在第一次运行时显然会失败。这是一件好事。如果你可以看到你的测试失败，在你让它们通过之前，你消除可能性：你的测试结果正在显示你误报。基于这个原因，*TDD* （测试驱动开发）的支持者们主张在实现代码之前就为它编写单元测试。因为你正在编写从一个 API 用户角度，当你首先编写测试时，使用测试驱动开发强迫你写出更加模块化的、可测试的代码。你也可能注意到了，你的 API 会更易于使用，因为你不用操心实现的细节，当你写测试时——你不会意外地泄露实现细节到你的 API 中。

In fact, while I was writing this example, I started with the tests above, and then decided to implement the features. I was tempted to use some Twitter Bootstrap controls out of the box to implement my styles, but that would have forced me to expose unnecessary markup details in the API because Bootstrap makes assumptions about the tags you'll use to implement controls. As a result, I adapted the stylesheet to pass the tests, and the API is better for it. Had I not written failing tests first, I might have accepted the extra markup, and over-complicated the example, and then written tests that passed against the inferior implementation.

事实上，当我写这个示例的时候，我由上面的测试开始，然后决定完成这些特性。我受诱惑去使用一些开箱可用的 Twitter Bootstrap 控件来实现我的样式，但那会强迫我去暴露一些不必要的标记细节到 API 中，因为 Bootstrap 做了一些假设关于你将会使用的标签来完成控件。作为结果，我改编了样式表来通过测试，并且 API 也变得更好了。如果我没有先写失败的测试，我可能就会接受那些额外的标记，并且把这个示例过度复杂化，然后又要写测试来通过这些低劣的实现。

Twitter Bootstrap is a popular CSS framework that is sometimes useful for prototyping web applications quickly. It's a good idea to play with it and see if it would be useful to you. Remember though that it's designed to be tweaked and customized. Don't feel like you have to conform to the default markup and classes if they don't fit your particular use-case.

Twitter Bootstrap 是一个流行的 CSS 框架，有时对于快速实现 Web 应用程序的原型来说非常实用。用好它是一个好主意，看看它是不是对你同样实用。记住它是被设计成可以微调和自定义的。不要感觉好像你不得不遵循默认的标记和类名，如果它们不符合你的特定的用例。

There's a running joke about how many sites look the same because they use all the same Bootstrap styles and controls. You may want to mix it up a little. There's an interesting blog post on the topic called, ["Customize Twitter Bootstrap To Not Look Bootstrap-y" by Antonin Januska.][16]

有一个流行的笑话是关于有多少个站点看起来相似，这是因为它们都在用相同的 Bootstrap 样式和控件。你可能想把它稍微搅和一下。有一个有趣的博文就是说这个的，[Antonin Januska 带来的《把 Twitter Bootstrap 自定义得不那么俗》][16]。

Time to see the test output from QUnit:

我们该看一下 QUnit 的测试输出了：

![Failing QUnit Screenshot][17]

Figure 5-3. Failing QUnit Screenshot

图 5-3. 失败的 QUnit 截图

Failing tests will be listed in red. To make the tests turn green, you'll need to implement the features. Normally it's wise to break the code up into separate concerns. There's a lot more on that topic in [Chapter 6][18]. In this case, you'll separate presentation concerns from data management. The data will come from a stubbed model module named `guestlistmodel.js`. For now it will just return some hard-coded names. You can implement loading and saving later:

失败的测试会显示为红色。为了让测试变绿，你需要完成这些特性。通常把代码打破成分离的关注点会是明智的。在 [第六章][18] 中我们会详细讨论这个话题。在这里，你将从数据管理中剥离出表现层的关注点。数据会取自一个断开的模型模块，名叫 ``。目前它会仅仅返回一些写死的名字。你可以稍后再完成加载和保存功能：

Example 5-4. `guestlist/src/guestlistmodel.js`

示例 5-4. `guestlist/src/guestlistmodel.js`

```js
var api = {
    load: function load() {
      return [
        'Jimi Hendrix',
        'Billie Holiday',
        'Nina Simone',
        'Jim Morrison',
        'Duke Ellington',
        'John Bonham'
      ];
    }
  };

module.exports = api;
```

As you can see, this file is pretty straightforward. It just defines an `api` object and sets it on `module.exports` to expose it to other modules in accordance with the CommonJS specification.

如你所见，这个文件非常直接了当。它仅仅定义了一个 `api` 对象，然后把赋值给  来暴露给其它模块，与 CommonJS 规范一致。

The code to manipulate the list DOM elements will go into a file named `guestlistview.js`:

而操作列表的 DOM 元素的代码将存进一个叫做 `guestlistview.js` 的文件中：

Example 5-5. `guestlist/src/guestlistview.js`

示例 5-5. `guestlist/src/guestlistview.js`

```js
var $ = require('jquery-browserify'),
  checkedinClass = 'icon-check',
  listClass = 'dropdown-menu',
  guestClass = 'guest',

  toggleCheckedIn = function toggleCheckedIn(e) {
    $(this).toggleClass(checkedinClass);
  },

  $listView = $('', {
    id: 'guestlist-view',
    'class': listClass
  }).on('click', '.' %2B guestClass, toggleCheckedIn),

  render = function render(guestlist) {

    $listView.empty();

    guestlist.forEach(function (guest) {
      $guest = $('' %2B
        '' %2B guest %2B
        '');
      $guest.appendTo($listView);
    });

    return $listView;
  },

  api = {
    render: render
  };

module.exports = api;
```js

This is the file doing all the work. First it uses `.require()` to get a reference to jQuery, and sets a few self-documenting variable names. The `toggleCheckedIn()` function is an event handler for the `click` event.

这就是这个文件所做的所有工作。首先它使用 `require()` 来获取对 jQuery 的引用，在后设置了一些自我描述的变量名。那个 `toggleCheckedIn()` 函数是一个事件处理函数，用于处理 `click` 事件。

The list element gets added. Note that it's using jQuery's `.on()` method to delegate the click events. `.on()` is the recently recommended way to hook up event handlers in jQuery. It replaces the deprecated `.bind()`, `.live()`, and `.delegate()` methods with a simplified syntax, and more consistent signature.

列表元素被添加进来。请注意它在使用 jQuery 的 `.on()` 方法来委托点击事件。 是目前 jQuery 推荐绑定事件处理函数的方式。它取代了过时的 `.bind()`、`.live()` 和 `.delegate()` 方法，使用了一个简化的语法，以及更一致的签名。

By delegating to the parent ordered list element, you can replace, remove, and add children to the list without worrying about removing and replacing event listeners. There won't be any memory leaks, and you don't have to wait until the whole list is rendered before you attach the listener. If you're hooking up listeners to DOM elements, most of the time, delegating to an ancestor is the right approach.

通过委托到有序列表这个父元素，你可以替换、移除、增加子元素到列表中，而不需要担心增减事件监听器。这不会产生内存泄露，而且你在绑定监听器之前不用去等待整个列表渲染完成。如果你需要在 DOM 元素上绑定监听器，大多数情况下，委托到一个祖先元素往往是正确的方式。

The `.render()` method takes an array of guest names, iterates over each one, and adds a corresponding list item to the `$listView` element. It then returns the rendered element to the calling function.

 方法接收来宾姓名的数组，遍历每个姓名，然后增加一个相关的列表项到  元素中。最后它返回渲染出的元素来调用函数。

The rest of the code simply defines the public API and exposes it via CommonJS.

剩下的代码简单地定义了公开 API，并且通过 CommonJS 规范暴露出来。

Some developers will intersperse `module.exports` assignments throughout a module. I find that having a single `module.exports` at the bottom of the file more clearly documents the module's public API.

有些开发者会在模块内部四处零星地为  赋值。而我认为在模块底部只保留一条赋值的做法可以更加清晰地表明这个模块的所有公开 API。

So far, the modules don't know about each other, so there's no way for them to do any work. To bring all the pieces together and manage the initial render, you'll need a higher level abstraction to kick things off. Enter `app.js`:

到目前为止，模块们彼些不知道，所以它们还无法协同工作。把所有的零件组合起来，管理初始渲染，你会需要一个更高级别的抽象来发动你的大作。进入 `app.js` 文件：

Example 5-6. `guestlist/src/app.js`
示例 5-6. `guestlist/src/app.js`

```js
var $ = require('jquery-browserify'),
  guestlistModel = require('./guestlistmodel'),
  guestlistView = require('./guestlistview'),
  $container = $('http://chimera.labs.oreilly.com#container');

$(function init() {
  var guestlistData = guestlistModel.load();
    $guestlist = guestlistView.render(guestlistData);
  $container.empty().append($guestlist);
});
```

This one should be fairly simple to follow. It uses `.require()` to reference `guestlistModel` and `guestlistView`, loads the guestlist, passes the data into `guestlistView.render()`, and adds it to the `container` element.

这段代码应该很容易看明白。它使用了 `.require()` 来引用  和 ，加载来宾名单，将数据传入 ，然后将它们加入  元素中。

The `.append()` line at the end of the `init()` function calls jQuery's `.empty()` method first for a couple of important reasons: First, if there's anything in that space already, it should be replaced, but it also releases references to event listeners so that the memory can be garbage collected. This is a better strategy than simply calling `.html()`. The latter is by far the more popular method, but it can be a major source of bugs and confusion when you start to develop large, client-heavy applications in JavaScript.

 函数最后包含  的一行首先调用了  的  方法，这是基于一些重要的原因：首先：如果那里已经有一些东西的话，它应该被替换掉，而这样做也将释放对事件监听器的引用，从而内存可以被回收。这个策略比单纯地调用  方法要更好一些。后者明显更加流行，但当你在开发重客户端的 JavaScript 应用程序时，它可能会导致 bug 或一些莫名其妙的情况。

None of this is going to work yet, because the modules all need to be compiled together in order of their dependencies. For that, you'll need Browserify.

它们还无法正式投入工作，因为所有模块都需要按照它们的依赖关系进行编译打包。因此，你需要 Browserify。

[Browserify][19] is a Node module that makes CommonJS modules work in the browser, using a server-side build step. The `browserify` command is available to kick off the bundle:

 是一个 Node 模块，可以令 CommonJS 模块工作在浏览器中，通过一个服务器端的构建步骤。 命令可以启动软件包。

    $ browserify src/app.js -o public/app.js

That's a bit too manual, though. You'll want to use `grunt` to automate the build. That way you can lint, build, and run your unit tests all in one step. Start with `package.json`:

但这看起来有一点手工。你可能会想用  来实现自动构建。通过这种方式，你只需一步就可以校验、构建、运行你的单元测试。从  开始：

Example 5-7. guestlist/package.json

示例 5-7. guestlist/package.json

```js
{
  "name": "guestlist",
  "version": "0.1.0",
  "author": "Eric Elliott",
  "description": "A handy tool for bouncers.",
  "keywords": ["party", "guestlist"],
  "main": "dist/app.js",
  "scripts": {
    "test": "grunt test"
  },
  "dependencies": {
    "jquery-browserify": "*"
  },
  "devDependencies": {
    "traverse": "*",
    "grunt": "*",
    "grunt-browserify": "*",
    "browserify": "*"
  },
  "engines": {
    "node": "&gt;=0.6"
  }
}
```

Since you'll be deploying this app, you should consider all of the code it uses to be part of the app, including its dependencies. You don't want those dependency versions shifting around under your feet during your deploy step. The less uncertainty and moving parts you have in a deploy step the better. For that reason, you're going to want to check in your `node_modules` directory (don't add it to `.gitignore`).
鉴于你可能会把这个应用部署出去，你应该把这个应用所用到的代码看作这个应用的一部分，包括它的依赖关系。你不会想要那些依赖版本在你部署的期间变来变去。在部署步骤中，不确定因素和活动部件当然是越少越好。因此，你将尝试把你的  目录提交至代码仓库（千万不要把它加到  文件中）。

Because all of the dependencies are going to be checked into the repository, you don't need to specify the versions to install. The "`*`" indicates that you want to use the latest version.

由于所有的依赖库都将被提交到仓库，你就不需要特别指定需要安装的版本。这里的  表明了你想使用最新的版本。

You'll also need a gruntfile. Currently the latest stable version of `grunt` looks for `grunt.js` by default. That's changed in the current alpha version, so you might need to use `gruntfile.js` as the filename. Here's what it looks like:

你还需要有一个 Grunt 启动文件。目前最新的稳定版 Grunt 会默认去找 `grunt.js` 文件。但在当前的内测版中，这一行为发生了变化，因此你可能需要使用  作为文件名。这个文件看起来是这样的：

Example 5-8. guestlist/grunt.js
示例 5-8. guestlist/grunt.js

```js
/*global module*/
module.exports = function(grunt) {
  'use strict';
  grunt.initConfig({

    // Read package.json into an object for later
    // reference (for example, in meta, below).
    pkg: '',

    meta: {

      // A template to add to the top of the bundled
      // output.
      banner: '
/*!  ' %2B
        '- v - ' %2B
        '
 ' %2B
        '' %2B
        '* Copyright (c) ' %2B
        ' ' %2B
        ';
' %2B
        ' * Licensed under the ' %2B
        ' license */'
    },

    // Specify which files to send through JSHint.
    lint: {
      all: ['./grunt.js', './src/**/*.js',
        './test-src/test.js']
    },

    // JSHint configuration options.
    jshint: {
      browser: false,
      node: true,
      strict: false,
      curly: true,
      eqeqeq: true,
      immed: true,
      latedef: true,
      newcap: true,
      nonew: true,
      noarg: true,
      sub: true,
      undef: true,
      unused: true,
      eqnull: true,
      boss: false
    },

    // Specify test locations for QUnit.
    qunit: {
      browser: ['test/index.html']
    },

    // Configuration for browserify.
    browserify: {
      "public/app.js": {
        requires: ['traverse'],
        entries: ['src/**/*.js'],
        prepend: [''],
        append: [],
        hook: function () {
          // bundle is passed in as first param
        }
      }
    }

  });

  // Load browserify tasks. Needed for bundling.
  grunt.loadNpmTasks('grunt-browserify');

  // Setup command line argument tasks. For e.g.:
  // $ grunt # executes lint, browserify, qunit
  // $ grunt test # runs qunit task, only.
  grunt.registerTask('default', 'lint browserify qunit');
  grunt.registerTask('install', 'browserify');
  grunt.registerTask('test', 'qunit');
};
```

The Grunt configuration file is just a Node JavaScript module, so you can write functions in it, evaluate expressions, and so on.

grunt 配置文件就是一个 Node 的 js 模块，因此你可以在它里面编写函数、执行表达式、等等。

Browserify requires a little extra configuration sometimes. Please refer to the ` [grunt-browserify][20] ` documentation for specific settings. Just don't forget to load the grunt tasks with `grunt.loadNpmTasks('grunt-browserify')`.

 有时需要一点额外的配置。详细设置请参考  文档。别忘了要使用  来加载  任务。

The `.registerTask()` calls make grunt command line arguments available. For example, all the `default` tasks will run when you execute `grunt` without any arguments, and `grunt test` will only run the tests.

这里的多个  调用将使  的命令行参数变得可用。举个例子，所有的  任务将运行，当你执行  不指定任何参数，而且  将会只运行测试。

Time for some action:

该来些动作了：

    $ grunt
    Running "lint:all" (lint) task
    Lint free.

    Running "browserify:public/app.js" (browserify) task

    Running "qunit:browser" (qunit) task
    Testing index.html..OK
    &gt;&gt; 3 assertions passed (32ms)

    Done, without errors.

Notice the QUnit task output. The tests are green, and in this form, you can run them on an automated continuous integration system.

请注意 QUnit 任务的输出。测试都是绿色的了，而且使用这种形式，你可以在一个自动的持续集成系统中运行测试。

In case you're curious, the browser output looks like this:

如果你还好奇，可以看一下浏览器中的输出：

![Passing QUnit Screenshot][21]

Figure 5-4. Passing QUnit Screenshot

图 5-4. Passing QUnit Screenshot

In general, CSS is a little outside the scope of this book, but there are a few tips you should be aware of. For some great ideas on how to use CSS for applications, see [*Scalable and Modular Architecture for CSS*, by Jonathan Snook][22].

大体来说，CSS 有点超出了本书的范畴，但你应该知道一些小技巧。对于如何在应用程序中使用 CSS 有一些好想法，参见  带来的《可扩展模块化的 CSS 架构》一书。

The Guestlist code employs a custom icon font for the checkbox. Fonts provide a major advantage over traditional methods like `png` icons. For instance, they are infinitely scaleable, and you can apply any CSS you'd normally apply to text. You can also create a single, custom font that is easy for clients to download. The font was created using a free custom font app called [IcoMoon][23]. The icon font related CSS looks like this:

来宾名单应用的代码使用了一个自定义的图标字体来显示复选框。字体提供了比传统方式（比如  格式的图片）更大的优势。比如说，它们可以无限放大，而且你可以对它应用任何适用于文本的 CSS 样式。你也可以创建一个单独的、自定义的字体，从而很易于客户端下载。这种字体可以使用一个叫做  的免费字体自定义应用来创建。跟图标字体相关的 CSS 代码如下：

Example 5-9. Icon Font CSS

示例 5-9. Icon Font CSS

```css
@font-face {
  font-family: 'guestlist';
  src:url('fonts/guestlist.eot');
  src:url('fonts/guestlist.eot?#iefix')
      format('embedded-opentype'),
    url('fonts/guestlist.svg#guestlist')
      format('svg'),
    url('fonts/guestlist.woff') format('woff'),
    url('fonts/guestlist.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

[class^="icon-check"]:before,
[class*=" icon-check"]:before {
  font-family: 'guestlist';
  font-style: normal;
  font-size: .75em;
  speak: none;
  font-weight: normal;
  -webkit-font-smoothing: antialiased;
}
.icon-check:before {
  content: "\e000";
}
.dropdown-menu .icon-check .name {
  padding-left: 1em;
}
```

[14]: http://yeoman.io/
[15]: http://yeoman.io/packagemanager.html
[16]: http://antjanus.com/blog/web-design-tips/user-interface-usability/customize-twitter-bootstrap-into-themes/
[17]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0503.tiff
[18]: http://chimera.labs.oreilly.com/ch06.html (Chapter&nbsp;6.&nbsp;Separation of Concerns)
[19]: https://github.com/substack/node-browserify
[20]: https://github.com/pix/grunt-browserify
[21]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0504.tiff
[22]: http://smacss.com/
[23]: http://icomoon.io/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
