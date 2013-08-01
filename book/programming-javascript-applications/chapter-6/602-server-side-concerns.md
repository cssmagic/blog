# [译] [PJA] [600] 服务器端的关注点

> * Original: [Server Side Concerns](http://chimera.labs.oreilly.com/books/1234000000262/ch06.html#server_side_concerns)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Server Side Concerns

There was a time when the server side did a lot of work, but that time has come and gone in the world of JavaScript applications. Many current JavaScript applications defer most of the rendering and business logic to the client. For most apps, the server side will look something like this:

  1. RESTful or REST-like API.

  2. Static file-server.

  3. A single-page index route that pre-injects data for the initial page load. (This can be replaced by the static file server if you defer the page data to a subsequent AJAX request).

There are many ways to accomplish these goals, but you should have little trouble finding alternatives via Google. In this section, you'll learn how to get the job done with Node and Express.

### Getting Started With Node and Express

Node is a server-side JavaScript environment with many attractive features:

  * A fast JavaScript engine (built on V8).

  * Asynchronous by default philosophy (nothing should block).

  * Event-loop design (much like the browser environment).

  * Networking as a first class citizen (create production capable servers with few lines of code).

  * A highly usable streams API.

  * A large, rapidly growing developer community.

  * A simple, CommonJS-based module solution that guarantees module encapsulation (your var declarations are limited to module scope).

  * A developer friendly package management system with thousands of open-source packages to choose from.

Some of these features might take some getting used to if you are accustomed to server-side environments that allow features such as blocking IO and a single thread per connection (for convenient state management). However, you'll find that the incredible performance boost achieved by non-blocking request/response cycles is well worth the learning effort.

Don't underestimate the value of the asynchronous by default philosophy. That is the key to Node's incredible performance in production environments.

Where other environments force users to wait in line while files load or network operations take place, Node fires off the request and keeps accepting new connections and executing other code paths while the asynchronous event does its work in the background.

Processes can spend an incredible amount of time waiting for file reads and network requests, especially if they encounter an error. Node just keeps cruising along. It's like getting out of congested city streets with stop lights at every block and on to an open freeway.

Node isn't fast simply because of the performance of the V8 JavaScript engine (though that does help). It's fast because it doesn't waste time waiting around for things to happen. There are other platforms that share some of JavaScript's performance characteristics: Twisted Python and Tornado spring to mind. They're fast for the same reason. However, even though they are more mature, they can't compete with the active membership of the JavaScript developer community.

Node comes packaged with a module management solution called npm. It gives you access to a package registry stocked with thousands of open-source packages, and makes it very easy for you to contribute your own, or use a private git repository for proprietary work. Of course, it's easy to mix and match open-source and proprietary packages in a single application.

#### Installing Node and Express

First, make sure you have node installed. There are installers available from the [Node homepage][26], but I like to use `nvm` so that I can easily switch between different versions of node. To install node with `nvm`:

    $ curl https://raw.github.com/creationix/nvm/master/install.sh | sh

For more on `nvm`, check out the docs on the [Github repository][27]. With node installed, you'll need to create a new directory for your project:

    $ mkdir my-first-project
    $ cd my-first project

Then initialize your project:

    $ npm init

Express is currently the most popular application framework for Node. It's easy to learn and use, and it has a vibrant developer community. If you're going to build applications in Node, chances are you'll eventually use express. There's no time like the present to get started. Install express:

    $ npm install --save express

That's it. You're ready to get started!

If this is your first time using Node and Express, it might be helpful to see what some of the community believes are the current set of best practices. [Node Bootstrap][28] aims to show new users some common practices in the Node / Express community, using Twitter Bootstrap. Among other things, there's an example of using the cluster module to manage multiple instances of the server (utilizing all available CPU cores).

#### Organizing Files in Node

It's a good idea to follow the emerging file organization trends in existing, popular Node repositories. That way, anybody familiar with Node should be able to find their way around your repository. Here are some common file locations:

  * Main `./index.js`, `./server.js`, or `./yourentryfile.js` in the root.

  * Supporting files in `./lib/`

  * Static http files in `./public/`

  * Views or templates in `./views/`

  * Command-line executables in `./bin/`

  * Tests in `./test/` (or `./spec/` if you`re a jasmine cool-aid drinker)

  * Npm Scripts in `./scripts/`

  * Config in `./config/`

  * Documentation in `./doc/`

  * Examples in `./examples/`

  * Performance analysis in `./benchmarks/`

  * Native c / c%2B%2B source in `./source/`

The [npm repository][29] serves as a good example.

#### Node Libraries

Some of my favorite Node libraries include:

  * [Mout][30] Like Underscore / LoDash. Stuff that should probably be included in JavaScript.

  * [Express][31] Web application framework.

  * [Nconf][32] Application config.

  * [Hogan][33] Mustache for express.

  * [Superagent][34] Communicate with APIs.

  * [Socket.io][35] Realtime communications (websockets).

  * [Q][36] Promises.

  * [Async][37] Asynchronous functional utilities.

  * [Bunyan][38] Logging.

  * [Tape][39] Testing.

  * [Cuid][40] Better than guid/uuid for web applications.

  * [Derby.js][41] Add realtime, collaborative MVC to express.

  * [Node-http-proxy][42] Proxy your service APIs.

#### Configuration

Don't include configuration data in your app repository (including secrets, paths to file locations, server hostnames, etc..). Instead, set up environment files with examples for sane defaults. Check in the examples, but don't check in the actual configuration. Following this rule of thumb will make deployment / ops support for the app a lot easier. Check an example file into your app repo: `s3.env.example`

    S3_KEY=mykey
    S3_SECRET=mysecret

Then copy it and fill in the real values when you install the app:

    $ cp s3.env.example s3.env

Use a package like `nconf` to make the environment variables available in your app. Make sure that the real environment files get added to `.gitignore` so that you don't accidentally check them into your repository.

One of the first stumbling blocks you might run into moving from browsers to Node is that you can't rely on your closure state to be reserved for a single user. You have a single instance of the app, with a single pool of memory, and a potentially unbounded number of incoming connections.

State needs to be kept in a database, or passed as parameters through function calls. For example, each request in an Express application will have corresponding request and response objects. That may be a good place to store in-memory state for a single request/response cycle. Likewise, singletons are a good way to store state that will be shared for all requests, such as your application configuration, but otherwise, they're usually an anti-pattern in Node applications.

#### Express

There are a lot of application frameworks available for Node. One popular framework that I find particularly useful is Express. It's basically an HTTP server built on top of Node's http module and Connect middleware.

#### Create your app

To create an express app instance, you'll need to require express, and call the function that gets returned:

    var express = require('express'),

      // Create app instance.
      app = express();

#### Routing

Express has a built-in app router. It's pretty simple to use. First, request method names correspond to the methods you call to set up your route. `GET` is `.get()`. `POST` is `.post()` and so on. To create a route that will handle any request type, use `.all()`. Pass the route as the first parameter, and a function as the second parameter:

    app.get('http://www.cssmagic.net/', function (req, res) {
      res.setHeader('Content-Type', 'text/plain');

      res.end('Hello, world!');
    });

Routes have easy parameter matching:

    app.get('http://www.cssmagic.net/:name', function(req, res){
      var name = req.params.name;

      res.send('Hello, ' %2B name);
    });

A route can be a regular expression:

    app.get(/(Hugh|Kevin)/, function (req, res, next) {
      var name = req.params[0], // Whitelisted user
        output;

      // Write something to output...

      res.send('Hello, ' %2B name);
    });

#### Middleware

Middleware is software that takes an incoming request, processes it, and passes it on to the next piece of middleware in the chain. Express middleware takes the form:

    // Add some data to the request object that your other
    // midleware and routes can use.
    app.use(function (req, res, next) {
      req.foo = 'bar';
      next();
    });

Here's how it works in the context of an express server:

    'use strict';
    var express = require('express'),

      // Create app instance.
      app = express(),

      // Use the `PORT` environment variable, or port 44444
      port = process.env.PORT || 44444;

    // The new middleware adds the property `foo` to the request
    // object and sets it to 'bar'.
    app.use(function (req, res, next) {
      req.foo = 'bar';
      next();
    });

    app.get('http://www.cssmagic.net/', function (req, res) {
      res.setHeader('Content-Type', 'text/plain');

      // Send the value passed from the middleware, above.
      res.end(req.foo);
    });

    app.listen(port, function () {
      console.log('Listening on port ' %2B port);
    });

Point a browser at the new server, or just use curl:

    $ curl http://localhost:44444/
    bar

Handling errors is just as simple. Again, you'll use middleware:

    'use strict';
    var express = require('express'),

      // Create app instance.
      app = express(),

      // Use the `PORT` environment variable, or port 44444
      port = process.env.PORT || 44444;

    // Some middleware that produces an error:
    app.use(function (request, response, next) {
      var bar;

      try {

        // This will throw because `foo` is undefined.
        request.foo = foo.get('bar');

      } catch (error) {

        // Pass the error to the next error handler in the
        // middleware chain. If you forget `return` here,
        // it will continue to process the rest of the
        // function, and probably throw an unhandled exception.

        return next(error);
      }

      // Do something with bar.
    });

    // Tell express to process routes before it gets to the error handler.
    app.use(app.router);

    // Error handlers take four parameters. The first is the error.
    // Generally, you'll want to add your error handler to the bottom of
    // your app.use() stack.
    app.use(function (error, request, response, next) {

      // Log the error.
      console.log(error);

      // Send the user a friendly message:
      response.send(500, 'Your request was not handled successfully. ' %2B
        'Our smartest fix-it guy has already been alerted. ' %2B
        'Contact us if you need help.');

      // Use setTimeout to give the app time to log and clean up,
      // but shut down ASAP to avoid unintended behavior.
      // Could also use setImmediate() in recent versions of Node.
      setTimeout(function () {
        process.exit(1);
      }, 0);

    });

    app.get('http://www.cssmagic.net/', function (req, res) {
      res.setHeader('Content-Type', 'text/plain');

      // Sadly, nobody will ever see this friendly greeting.
      res.end('Hello, world!');
    });

    app.listen(port, function () {
      console.log('Listening on port ' %2B port);
    });

You can clean up after a lot of errors. In fact, sometimes an error is an expected probability. For example, there's a chance that sometimes remote services won't be available, and you can recover from that condition and try again later. However, sometimes you just won't get the answer you're looking for and there's nothing you can do to recover. You don't want to keep your server running with undefined state. In the case of errors that you can't easily recover from, it's important to shut down the process as quickly as possible.

##### Let it Crash

Processes crash. Like all things, your server's runtime will expire. Don't sweat it. Log the error, shut down the server, and launch a new instance. You can use Node's cluster module, forever (a Node module available on npm), or a wide range of other server monitor utilities to detect crashes and repair the service in order to keep things running smoothly, even in the face of unexpected exceptions.

#### Templates

Express comes with some built-in handling of templates, but it must be configured. You have to tell Express which view engine to use in order to process templates, and where to find the views. First, you'll want to require your template engine. For Mustache templates, you can use Hogan:

    var hulk = require('hulk-hogan');

Most of the settings for express are specified with `app.set()`. You'll need to use it to configure express to use the template engine of your choice. There are four options that you should be aware of:

    // Tell express where to find your templates.
    app.set('views', __dirname %2B 'http://www.cssmagic.net/views');

    // By default, Express will use a generic HTML wrapper (a layout)
    // to render all your pages. If you don't need that, turn it off.
    app.set('view options', {layout: false});

    // Tell express which engine to use.
    app.set('view engine', 'hulk-hogan');

    // Specify the extension you'll use for your views.
    app.engine('.html', hulk.__express);

Remember to define a route that uses your new view. Assuming you've used your middleware to build a data object on the request object called `req.data` (see Middleware, above):

    app.all('http://www.cssmagic.net/', function (req, res) {
      res.render('index', req.data, function callback(err, html) {
        // Handle error.
      });
    });

You can leave off the callback parameter and any errors will be internally passed via `next(err)` for your generic error handlers to catch. If you pass the callback, that automatic error handling will not occur, and you should handle the error explicitly.

#### Next Steps

Of course, you want to do a lot more with your app than return a hard-coded message to your users. The good news is that there are drivers for just about any database you can dream of. You can use a variety of template libraries, and of course, serve static files. I encourage you to dive into the Node module playground and take a look around. For starters, here's a simple static file server example using the built-in static middleware:

    var express = require('express'),

        app = express(), // Create the express app.

        // Try pulling the port from the environment. Or
        // default to 5555 if no environment variable is set.
        port = %2Bprocess.env.PORT || 5555;

    // .bodyParser() parses the request body and creates the
    // req.body object.
    app.use( express.bodyParser() );

    // .methodOverride() lets you simulate DELETE and PUT
    // methods with POST methods. Common boilerplate.
    app.use( express.methodOverride() );

    // .static() creates a static file server, which looks for
    // assets in the /public directory, in this case.
    app.use( express.static(__dirname %2B 'http://www.cssmagic.net/public') );

    // app.router handles path routing for express apps.
    app.use( app.router );

    // Express comes with a default error handler that is
    // intended for development use. You'll want to implement
    // your own for production systems.
    app.use( express.errorHandler() );

    app.listen(port, function () {
      console.log('Server listening on port ' %2B port);
    });

Have a look at the [Express guide][43] and [API reference][44] for a lot more useful examples, and the [Node Manual][45] for Node API documentation. There are lots of useful gems that you'll want to learn more about.

[26]: http://nodejs.org
[27]: https://github.com/creationix/nvm
[28]: https://github.com/inadarei/nodebootstrap
[29]: https://github.com/isaacs/npm
[30]: http://moutjs.com/
[31]: http://expressjs.com/
[32]: https://github.com/flatiron/nconf
[33]: http://twitter.github.com/hogan.js/
[34]: https://github.com/visionmedia/superagent
[35]: http://socket.io/
[36]: https://github.com/kriskowal/q
[37]: https://github.com/caolan/async
[38]: https://github.com/trentm/node-bunyan
[39]: https://github.com/substack/tape
[40]: http://usecuid.org/
[41]: http://derbyjs.com/
[42]: https://github.com/nodejitsu/node-http-proxy
[43]: http://expressjs.com/guide.html
[44]: http://expressjs.com/api.html
[45]: http://nodejs.org/api/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
