# [译] [PJA] [508] 基于 CommonJS、npm、Grunt 和 Browserify 构建客户端代码

> * Original: [Building Client-Side Code With CommonJS, npm, Grunt, and Browserify](http://chimera.labs.oreilly.com/books/1234000000262/ch05.html#building_clientside_code_with_commonjs_npm_grunt_and_browserify)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Building Client-Side Code With CommonJS, npm, Grunt, and Browserify

There are a variety of competing standards for module management with JavaScript, but due to the popularity of Node, by far the most established is npm %2B CommonJS. There have been efforts to create a similar combination of features for the client-side, including AMD, [Yeoman][14], and [Bower][15], but none of them can compete with the simplicity and package availability of npm and CommonJS modules.

To demonstrate, take a look at how you might build a minimal client-side guestlist app (just in case you need a bouncer at your birthday party).

### Defining the App

Most new software projects use agile development methods to produce quick software creation and enhancement iterations. In agile software, the time between releases is measured in days or weeks rather than months or years. To learn more about agile, see [The Art of Agile Development: Pragmatic guide to agile software development], by James Shore, Chromatic.

Typically when you set out to build an app, you'll start with a list of _user stories_. You can use those scenarios to come up with _acceptance criteria_, which can be further distilled into functional unit tests.

A user story is a short, simple description of some action that your user might want to perform. For example, "As an event organizer, I want to check arriving guests against the guestlist." User stories should always contain a role along with the story. Answer the questions, "who is performing the action?" and "what is the user trying to accomplish?"

To express this example story in code, you'll need a list element with clickable links in list items. The unit tests might look something like this:

Example&nbsp;5-2.&nbsp;guestlist/test/test.js

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

You'll need to include that in your QUnit HTML file:

Example&nbsp;5-3.&nbsp;guestlist/test/index.html

            QUnit Test Suite

These tests will obviously fail at first. That's a good thing. If you can see your tests fail before you make them pass, you eliminate the possibility that your test results are showing you false positives. For this reason, _TDD_ (Test Driven Development) proponents advocate writing your unit tests before you implement the code that they're written against. Because you're writing from an API user perspective when you write tests first, using test driven development forces you to write more modular, testable code. You may also notice that your APIs are easier to use because you're not worried about implementation details when you write your tests -- you won't accidentally leak implementation details into your APIs.

In fact, while I was writing this example, I started with the tests above, and then decided to implement the features. I was tempted to use some Twitter Bootstrap controls out of the box to implement my styles, but that would have forced me to expose unnecessary markup details in the API because Bootstrap makes assumptions about the tags you'll use to implement controls. As a result, I adapted the stylesheet to pass the tests, and the API is better for it. Had I not written failing tests first, I might have accepted the extra markup, and over-complicated the example, and then written tests that passed against the inferior implementation.

Twitter Bootstrap is a popular CSS framework that is sometimes useful for prototyping web applications quickly. It's a good idea to play with it and see if it would be useful to you. Remember though that it's designed to be tweaked and customized. Don't feel like you have to conform to the default markup and classes if they don't fit your particular use-case.

There's a running joke about how many sites look the same because they use all the same Bootstrap styles and controls. You may want to mix it up a little. There's an interesting blog post on the topic called, ["Customize Twitter Bootstrap To Not Look Bootstrap-y" by Antonin Januska.][16]

Time to see the test output from QUnit:

![Failing QUnit Screenshot][17]

Figure&nbsp;5-3.&nbsp;Failing QUnit Screenshot

Failing tests will be listed in red. To make the tests turn green, you'll need to implement the features. Normally it's wise to break the code up into separate concerns. There's a lot more on that topic in [Chapter&nbsp;6][18]. In this case, you'll separate presentation concerns from data management. The data will come from a stubbed model module named `guestlistmodel.js`. For now it will just return some hard-coded names. You can implement loading and saving later:

Example&nbsp;5-4.&nbsp;guestlist/src/guestlistmodel.js

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

As you can see, this file is pretty straightforward. It just defines an `api` object and sets it on `module.exports` to expose it to other modules in accordance with the CommonJS specification.

The code to manipulate the list DOM elements will go into a file named `guestlistview.js`:

Example&nbsp;5-5.&nbsp;guestlist/src/guestlistview.js

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

This is the file doing all the work. First it uses `.require()` to get a reference to jQuery, and sets a few self-documenting variable names. The `toggleCheckedIn()` function is an event handler for the `click` event.

The list element gets added. Note that it's using jQuery's `.on()` method to delegate the click events. `.on()` is the recently recommended way to hook up event handlers in jQuery. It replaces the deprecated `.bind()`, `.live()`, and `.delegate()` methods with a simplified syntax, and more consistent signature.

By delegating to the parent ordered list element, you can replace, remove, and add children to the list without worrying about removing and replacing event listeners. There won't be any memory leaks, and you don't have to wait until the whole list is rendered before you attach the listener. If you're hooking up listeners to DOM elements, most of the time, delegating to an ancestor is the right approach.

The `.render()` method takes an array of guest names, iterates over each one, and adds a corresponding list item to the `$listView` element. It then returns the rendered element to the calling function.

The rest of the code simply defines the public API and exposes it via CommonJS.

Some developers will intersperse `module.exports` assignments throughout a module. I find that having a single `module.exports` at the bottom of the file more clearly documents the module's public API.

So far, the modules don't know about each other, so there's no way for them to do any work. To bring all the pieces together and manage the initial render, you'll need a higher level abstraction to kick things off. Enter `app.js`:

Example&nbsp;5-6.&nbsp;guestlist/src/app.js

    var $ = require('jquery-browserify'),
      guestlistModel = require('./guestlistmodel'),
      guestlistView = require('./guestlistview'),
      $container = $('http://chimera.labs.oreilly.com#container');

    $(function init() {
      var guestlistData = guestlistModel.load();
        $guestlist = guestlistView.render(guestlistData);
      $container.empty().append($guestlist);
    });

This one should be fairly simple to follow. It uses `.require()` to reference `guestlistModel` and `guestlistView`, loads the guestlist, passes the data into `guestlistView.render()`, and adds it to the `container` element.

The `.append()` line at the end of the `init()` function calls jQuery's `.empty()` method first for a couple of important reasons: First, if there's anything in that space already, it should be replaced, but it also releases references to event listeners so that the memory can be garbage collected. This is a better strategy than simply calling `.html()`. The latter is by far the more popular method, but it can be a major source of bugs and confusion when you start to develop large, client-heavy applications in JavaScript.

None of this is going to work yet, because the modules all need to be compiled together in order of their dependencies. For that, you'll need Browserify.

[Browserify][19] is a Node module that makes CommonJS modules work in the browser, using a server-side build step. The `browserify` command is available to kick off the bundle:

    $ browserify src/app.js -o public/app.js

That's a bit too manual, though. You'll want to use `grunt` to automate the build. That way you can lint, build, and run your unit tests all in one step. Start with `package.json`:

Example&nbsp;5-7.&nbsp;guestlist/package.json

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

Since you'll be deploying this app, you should consider all of the code it uses to be part of the app, including its dependencies. You don't want those dependency versions shifting around under your feet during your deploy step. The less uncertainty and moving parts you have in a deploy step the better. For that reason, you're going to want to check in your `node_modules` directory (don't add it to `.gitignore`).

Because all of the dependencies are going to be checked into the repository, you don't need to specify the versions to install. The "`*`" indicates that you want to use the latest version.

You'll also need a gruntfile. Currently the latest stable version of `grunt` looks for `grunt.js` by default. That's changed in the current alpha version, so you might need to use `gruntfile.js` as the filename. Here's what it looks like:

Example&nbsp;5-8.&nbsp;guestlist/grunt.js

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

The Grunt configuration file is just a Node JavaScript module, so you can write functions in it, evaluate expressions, and so on.

Browserify requires a little extra configuration sometimes. Please refer to the ` [grunt-browserify][20] ` documentation for specific settings. Just don't forget to load the grunt tasks with `grunt.loadNpmTasks('grunt-browserify')`.

The `.registerTask()` calls make grunt command line arguments available. For example, all the `default` tasks will run when you execute `grunt` without any arguments, and `grunt test` will only run the tests.

Time for some action:

    $ grunt
    Running "lint:all" (lint) task
    Lint free.

    Running "browserify:public/app.js" (browserify) task

    Running "qunit:browser" (qunit) task
    Testing index.html..OK
    &gt;&gt; 3 assertions passed (32ms)

    Done, without errors.

Notice the QUnit task output. The tests are green, and in this form, you can run them on an automated continuous integration system.

In case you're curious, the browser output looks like this:

![Passing QUnit Screenshot][21]

Figure&nbsp;5-4.&nbsp;Passing QUnit Screenshot

In general, CSS is a little outside the scope of this book, but there are a few tips you should be aware of. For some great ideas on how to use CSS for applications, see [[Scalable and Modular Architecture for CSS], by Jonathan Snook][22].

The Guestlist code employs a custom icon font for the checkbox. Fonts provide a major advantage over traditional methods like `png` icons. For instance, they are infinitely scaleable, and you can apply any CSS you'd normally apply to text. You can also create a single, custom font that is easy for clients to download. The font was created using a free custom font app called [IcoMoon][23]. The icon font related CSS looks like this:

Example&nbsp;5-9.&nbsp;Icon Font CSS

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
