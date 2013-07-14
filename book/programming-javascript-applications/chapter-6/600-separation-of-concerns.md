# [译] 博文标题

> * Original: [XXXXXXXXXXXXXXXXXXXX](xxxxxxxxxxxxxxxxxxx)
> * Translated by: [cssmagic](https://github.com/cssmagic)


# Chapter&nbsp;6.&nbsp;Separation of Concerns

_Separation of concerns_ is the idea that each module or layer in an application should only be responsible for one thing, and should not contain code that deals with other things. Separating concerns reduces code complexity by breaking a large application down into many smaller units of encapsulated functionality.

It's easy to confuse separation of concerns with employing modules for the construction of your application, but separation of concerns also implies the layering of functionality in your application. For example, n-tier architecture and MVC architectures are the result of separating concerns across the entire application, rather than at the individual module level. The goal of MVC and related patterns is to separate data management and presentation.

Separation of concerns can be expressed as functions, modules, controls, widgets, layers, tiers, services, and so on. The various units of concern vary from one app to the next, and each different app may use a different combination. Functions and modules have already been discussed.

A _control_ is a reusable GUI input that enables user interaction with your application. For example, combo boxes, calendar inputs, sliders, buttons, switches, and knobs are all controls.

A _widget_ is a small application which is intended to be embedded in other applications. For example, [WordPress][4] allows developers to offer embeddable units of functionality to blog owners through its plugin ecosystem. There are many widgets to manage calendars, comments, maps, and all sorts of services from third party providers.

The word widget is historically used to mean the same thing as controls. To avoid confusion, this book will always refer to interactive form inputs as controls (or inputs), and widgets will always refer to embeddable mini applications.

_Layers_ are logical groupings of functionality. For example, a data layer might encapsulate functionality related to data and state, while a presentation layer handles display concerns, such as rendering to the DOM and binding UI behaviors.

_Tiers_ are the runtime environments that layers get deployed to. A runtime environment usually consists of at least one physical computer, an operating system, the runtime engine (e.g., Node, Java, or Ruby) and any configuration needed to express how the application should interact with its environment.

It's possible to run multiple layers on the same tier, but tiers should be kept independent enough that they can easily be deployed to separate machines, or even separate data centers. For large scale applications, it's usually also necessary that tiers can scale horizontally, meaning that as demand increases, you can add machines to a tier in order to improve its capacity to meet that demand.

## Client Side Concerns

There are several client-side concerns that almost every mature JavaScript application might deal with at some point:

  * Module management

  * Events

  * Presentation and DOM manipulation

  * Internationalization

  * Data Management and IO (including AJAX)

  * Routing (translating URLs to script actions)

  * Logging

  * Analytics Tracking

  * Authentication

  * Feature Toggling (decouple code deployment and feature release)

Various libraries and frameworks exist which generally attempt to provide some combination of mechanisms to deal with these concerns. Libraries roughly fall into two categories: Minimal and monolithic. Minimal libraries generally provide a handful of objects and methods which make it easier to build well organized applications. Perhaps the most famous example is Backbone.js, which provides Views, Models, Collections, Routers, and Events.

That may sound like a lot, until you compare it to something like the Dojo framework, which is something like a library of libraries. It includes an impressive array of components which do everything from extending JavaScript with ideas from other programming languages like aspects (Aspect Oriented Programming) to DOM widgets such as a calendar. Dojo is interesting because it started out more monolithic, and now it's actually a tiny module loader with a large library of components that you can use if you wish, without loading the entire Dojo framework.

Larger frameworks tend to be more heavy-handed (opinionated) about how your application architecture should function.

There is a growing trend in the JavaScript community moving in the direction of micro libraries, and micro library frameworks. [Microjs.com][5] has a list of interesting micro libraries. You should certainly explore the wealth of microlibraries available in the community, and use the ones that solve your problems.

You might see microlibrary proponents discourage the use of jQuery, not least because it's quite large compared to most microlibraries. However, there is some security in using a well tested library like jQuery because it is used and improved by thousands of other smart developers. It's usually safe to assume that jQuery will mostly behave as expected (of course, there are always exceptions).

Despite the claims of microlibary authors, most microlibraries have not undergone the same level of scrutiny as jQuery, and small libraries don't equate to bug free libraries. Everybody makes mistakes. Be prepared to debug them as you work with them. Please contribute fixes back to the author if you find and fix a bug.

It's also likely that you'll use most of jQuery's core feature set in every application that you build. I see no good reason to reassemble that feature set from microlibraries every time you start a new project.

Also be aware that many microlibraries may need to reinvent the wheel over and over again for commonly needed patterns, like copying attrubites from one object to another (as in jQuery or Underscore's `.extend()` method). It might seem like a good idea to use less code, but there's a chance you'll end up using more, instead, unless you go with a well organized microlibrary framework with components designed to be used together.

In order to decide which application frameworks or libraries might help you build your application, take a look at the [TodoMVC project][6], which compares many commonly used libraries, such as Backbone.js, Angular.js, Dojo, and YUI by showing you an example of what a simple Todo application looks like using each library.

### Module Management

Module management is concerned with supplying a standard architecture that allows your modules to fit together easily and communicate with each other without being tightly coupled.

The module manager prescribes a standard module API that all other modules can implement and a sandbox of common library functionality that any module can use, so that application modules can stay lightweight and focus exclusively on the service that they provide.

Module management typically supplies your application with:

  * Namespacing

  * Sandbox (a base layer of functionality that other modules can safely use)

  * Access to environment variables and bootstrapped data

  * Module lifecycle hooks (help with setup and teardown)

  * An event system for inter-module communication

Nicholas Zakas gave a related talk called [Scalable JavaScript Application Architecture][7], and Addy Osmani (author of Aura) wrote about it in ["Patterns For Large-Scale JavaScript Application Architecture"][8]. Often, module management implementations are part of a monolithic application library tightly coupled to a proprietary codebase, or both.

Aura is a good example of an open-source implementation with a fairly narrow focus on module management - with one clear difference: Aura has a strong focus on widgets, which are modules that have a UI component that needs to render itself to the screen. A generic module could perform any type of service, and may or may not need a dedicated space to render. A widget exists primarily for the purpose of suppling UI output or UI interaction.

For the sake of clarity and simplicity in the following examples, I wrote a simple module management solution called Tinyapp. It is an open source library. To get the source, clone it from Github:


    $ git clone git://github.com/dilvie/tinyapp.git

#### Getting Started

As of this writing, Tinyapp is rapidly evolving. Please see the [Tinyapp website][9] to learn how to download and install the latest version.

Simply `require()` it and initialize your app:


    var app = require('tinyapp');

    app.init({
      environment: environment,
      pageData: pageData,
      beforeRender: [promise1, promise2...]
    });

The `environment` object (optional) is the primary means of passing application environment configuration into your JavaScript application. Use it for passing in data that usually has to come from server side configuration, such as environment domain names, application ids for third party services, CDN urls, etc...

There are a couple of ways you could source the environment from the server side: load it with Ajax, or include it in the html with an inline script.

The `pageData` object is intended to register bootstrapped data with the app at page load time.

`beforeRender` is a list of promises which all must finish before the render phase begins. For example, many apps will need i18n translations to load before any module is allowed to render. By adding an i18n promise to the application's `beforeRender` queue, you can postpone render until the translations are loaded. Using `beforeRender` can prevent tricky race condition bugs, and provide a neat solution if you need a guaranteed way to handle tasks before the modules render. See [“Registration, Loading and Rendering”][10] for more about application and module timing hooks.

Be careful with the global `beforeRender` queue. Each item you add to it compounds the possibility that something in the queue will fail, which might significantly delay or block the render of your application. If what you're waiting for only impacts a single module, keep the waiting logic isolated to that module.

Here's how you might build up a typical Tinyapp module:

Require Tinyapp:


    var app = require('tinyapp');

Provide an API:


    var app = require('tinyapp'),

      hello = function hello() {
        return 'hello, world';
      },

      api = {
        hello: hello
      };

Export your module:


    var app = require('tinyapp'),

      hello = function hello() {
        return 'hello, world';
      },

      api = {
        hello: hello
      };

    module.exports = api;


This contrived example should be really be simplified even further. Since it doesn't actually use `tinyapp`, it doesn't need to require it, and since it's only exporting a single fuction (which is actually a pretty good idea for lots of modules), it could just be `module.exports = function hello() { `...

Any standard node-style module could be used as a Tinyapp module, but as you'll see, Tinyapp provides some utility and benefit that you'll use a lot.

#### Registration, Loading and Rendering

If you need to fetch some data asynchronously before you render your module, Tinyapp helps speed things up by launching your asynchronous calls as early as possible.

Module initialization is broken into two phases: load and render. Imagine you want to asynchronously fetch some data while the page is still loading so that you can begin to render it as early as possible. This can speed up your perceived page load times.

The solution is to kick off the asynchronous fetch at load time, so that it begins the download as early as possible: before DOM ready, and before the page is done processing all the HTML, CSS, and JavaScript on the page. Since the load is asynchronous, it will introduce only a few milliseconds of latency during the load phase, but it'll do most of the work in the background while the browser continues processing the rest of the page in parallel with the load.

If you have data that is secondary to the main purpose of the page, and you can get away with waiting until after the DOM is ready, it's a good idea to let your page load normally and fetch the data during the render step, instead of using the early load hook. This will reduce the number of page requests that must be handled at page load time (eliminating the latency hit) and hopefully also improve the perceived performance of your app.

Tinyapp allows you to hook into those page load phases individually so you can optimize page load times and manage render dependencies without worrying about asynchronous timing issues.

You can specify `load()` and `render()` callbacks by passing them into a registration method:


    app.register({
      load: load,
      render: render
    });

The `render()` callback is called after:

  * Any promise returned by `load()` is resolved. For example, if you return a jQuery AJAX promise from your `load()` function, it will wait until the AJAX fetch is complete. All jQuery AJAX calls return compatible promises.

  * All `beforeRender` callbacks have fired (see [“Getting Started”][11]).

  * The DOM is ready to be manipulated.

Registration is optional. If you don't need any of the features it provides, you aren't required to use it.

Time for an example. Imagine you have a widget that displays tour dates for bands. Here's how it could work:


    var
      app = require('tinyapp'),
      view = require('./view'),
      data,

      load = function load() {
        var url = 'http://api.bandsintown.com/artists/' %2B
          'Skrillex.json?api_version=2.0&amp;app_id=YOUR_APP_ID';

        whenLoaded = app.get(url);
        whenLoaded.done(function (response) {
          data = response;
        });

        return whenLoaded.promise();
      },

      render = function render() {
        view.render(data);
      };

    app.register({
      load: load,
      render: render,
    });

    module.exports = api;


Try not to do anything blocking in your `load()` function. For example, you might want to fetch the data that you need to complete your page render, but if you're loading a large collection and you need to iterate over the collection and do some data processing, save the data processing step for `render()` time, when you're not blocking the page render process.

_It's not safe to manipulate the DOM at all in your `load()` function._

### Events

_Events_ are a method of communicating between different application modules or components in order to achieve loose coupling, asynchronous communication, interprocess communication, or any combination of the above. For example, the browser environment is event-driven. It presents DOM elements to users (buttons and form inputs, for example), and then responds to user input by firing events for the application to listen for and handle.

You're probably already familiar with responding to existing browser DOM events. If you need a refresher, consult the [Mozilla DOM Reference][12] or "Dynamic HTML: The Definitive Reference" by Danny Goodman (O’Reilly). This section covers defining and using your own custom events, independent of the DOM.

In order to keep modules decoupled from each other, it's helpful to think of events as reports of what has happened, rather than commands for what should happen next. Other modules can listen for the events they care about and decide what to do next on their own.

#### Event Emitters

Often we need to provide a method API that can handle a range of asynchronous communication. A good solution to this problem is to return an object that emits events. DOM elements are event emitters, so that they can notify you of user input or other state changes. For example, most elements emit a click `event` when the user clicks on them, and the HTML5 video API emits events for loading progress, playback position changes, and a variety of other information that isn't immediately known.

It's possible to create your own event emitter API that you can mix into any JavaScript object. A minimal implementation might only have three methods:

.on(), .trigger(), .off()

Backbone.js is a client side MV* library that provides event emitters for models, views, routers, and collections. If you're using Backbone, (which relies on Underscore), you can turn any object into an event emitter like this:


    _.extend(myObject, Backbone.Events);

    myObject.on('my_event', function handle(data) {
      console.log(data);
    });

    object.trigger('my_event', 'hi'); // logs 'hi'

The downside to triggering events only on the object to which the events belong is that every listener must be given a reference to the object. In situations where there are potentially many objects listening, that strategy requires too much boilerplate code to be maintainable.

Another problem you might encounter is that listeners must be tightly coupled to emitters, which partially defeats the purpose of using events in the first place. To get around that, you can wire up emitters and receivers through an event mediator, or connector.

In cases where you have a views with child views that need to communicate up to the top level, this pattern becomes problematic because at each level, the child and parent must register with each other to relay messages back and forth. In that situation, you create a lot of code overhead, and debugging event passing bugs can become very painful.

If you have an object that needs to inform your application about asynchronous events such as incoming push notifications (chat notifications, new items added to collections, etc...), an event emitter might be the right solution.

#### Event Aggregators

In contrast to event emitters, an _event aggregator_ is a central object that collects events from multiple sources, and delivers them to registered subscribers. This is essentially a type of publish/subscribe pattern. An aggregator eliminates the need for specialized mediators to keep an emitter and listener decoupled. Again, with backb and Underscore, that might look something like this:


    var app = {
      bus: _.extend({}, Backbone.Events)};

    app.bus.on('my_event', function handle(data) {
      console.log(data);
    });

    app.bus.trigger('my_event', 'hi'); // logs 'hi'

Event aggregators can be enormously useful. Because neither emitter nor subscriber need know about the other, aggregators enable very loose coupling between modules. Each only needs a reference to the aggregator.

#### Queues and Stacks

One disadvantage of the publish/subscribe pattern is that there is no guarantee that a message will be processed once it is broadcast.

A _message queue_ is a type of event mediator that records every message it hears in a queue (or stack) for later processing. That log of messages can be permanently recorded, if needed, and the queue is actively managed, so it's possible to enforce guarantees that some listener got the message.

Often there is a need for separate modules or processes to collaborate in order to process input. For example, imagine that your application is a tool for photographers. It allows photographers to upload a photo, resize it, watermark it, and then send it to various social networks automatically.

When a photo is uploaded, it's added to a processing queue. Later, a pool of photo processing workers can independently pull photo processing jobs from the message queue as they are ready to process them, and message back when the photos are ready to be distributed across the web. Other workers can then pick up the finished photo messages and handle the distribution step. With this architecture, the processing workers can scale independently of the distribution workers.

If you need a message queue, you might be in luck. It's such a common pattern that there are several popular out of the box solutions, such as [Kestrel][13], or [Amazon Simple Queue Service (SQS)][14].

#### Choosing the Right Event Model

In reality, no single event model provides a complete solution to event management. It doesn't make sense to examine a bunch of different event models and chose just one for your application. Most web-based applications eventually need more than one type. Here are some example use cases:

For reusable libraries that deal with asynchronous behaviors, consider an event emitter.

For communication between models, views, logging systems, analytics systems, and so on, where it's advantageous to keep coupling to a minimum, go with an event aggregator.

For reliable communication between independently scalable and deployable application tiers, a message queue may be the right solution.

This guide has really only skimmed the surface of possible messaging patterns. I've intentionally focused only on the most common patterns that might be handy in almost every app that grows beyond a few thousand lines of code. For more ideas, take a look at Martin Fowler's writing on [Event Aggregator][15], [Observer Synchronization][16], and [Event Sourcing][17].

#### Events by Example

To better understand how the most common messaging patterns fit together, it might be useful to revisit the guestlist app introduced in [Chapter&nbsp;5][18].

Of course, the whole idea here is to keep the presentation concerns separate from the data management and domain logic concerns. Backbone.js can give you a leg up with that by encapsulating common data management tasks in models and collections, and encapsulating display and UI concerns in views.

All `Backbone.Model` instances are event emitters. When a model attribute changes, it emits a `change:` event, where `` is the name of the attribute that changed. Since you want to communicate the change to the view without tightly coupling the model to the view or vice verse, you'll want to have the model listen for its own change event, and rebroadcast it it the app level.

The view can listen for the app level `changed` event and respond by updating the list item that changed.

Here is the new guestlist model. You can listen to the `Backbone.Model` event emitter with, `this.on('change:checkedIn', handler)`:

Example&nbsp;6-1.&nbsp;guestmodel.js


    var Model = require('backbone-browserify').Model,
      app = require('./tinyapp'),

      // Set the checkedIn attribute on the model.

      toggleCheckedIn = function toggleCheckedIn() {
        this.set('checkedIn', !this.get('checkedIn'));
      },

      delegate = function delegate() {
        var sourceId = this.get('id');

        // Listen for toggled event, sent from the view.
        // sourceId is used to filter the event. The model
        // does not need to know where the event comes from --
        // only which item was clicked.

        app.on('toggled-checkedin', sourceId,
          toggleCheckedIn.bind(this));


        // Relay the change event so the view can listen for it
        // without knowing anything about the model.

        this.on('change:checkedIn', function (item) {

          // Send a shallow copy of the list item as the
          // message payload. Make sure the new checkedIn
          // state is easy to access.

          var event = app.extend({}, item, {
            sourceId: this.id,
            checkedIn: item.get('checkedIn')
          });


          // Broadcast the message on the aggregator.

          app.trigger('changed.checkedIn', event);
        }.bind(this));
      },

      // The collection expects a Backbone.Model constructor.

      api = Model.extend({
        initialize: delegate,
        toggleCheckedIn: toggleCheckedIn
      });

    module.exports = api;


Note the line:


    app.on('toggled-checkedin', sourceId,
      toggleCheckedIn.bind(this));

Tinyapp adds the concept of a `sourceId` to event listening. The idea is that you augment your triggered event object with the id of the object you're talking about. When you listen for the event (like this example illustrates), you can pass in the object's ID to filter out any events that don't have that `sourceId` on the event object. This can be handled at the application framework or event library level. For example, in Tinyapp, there's a wrapper around the `events.on` method that looks something like this:


    function on() {
      var args = [].slice.call(arguments),
        type = args[0],
        sourceId = args[1],
        callback = args[2];

      // If there's no sourceId, just pass this through
      // to the event emitter.

      if (args.length XXXXXXXXXXXXXXXXX= 2) {
        events.on.apply(events, arguments);
      } else {


        // Otherwise, filter out any events that don't match
        // the expected sourceId.

        events.on.call(events, type, function (event) {
          if (event.sourceId === sourceId) {
            callback(event);
          }
        });
      }
    }

Similar strategies include namespacing events, and creating separate channels (aggregators) for separate concerns.

Regardless of which mechanism you prefer for event communication, be careful not to refer to other modules directly (including via channels and namespaces). If modules know how to communicate with each other directly, it defeats the purpose of loose coupling, and you might as well give them a direct handle on each other. A common pitfall that I have fallen into myself is to use the module's namespace as the event namespace. Doing so gives every module that needs to listen knowledge of the emitting module by name.

Returning to the example app: The collection isn't doing much yet, but later you'll use it to add and remove guests. For now, it just gets a handle to the model constructor which it will use to instantiate new list items. The create method is just a thin wrapper around collection instantiation.

Example&nbsp;6-2.&nbsp;guestlistcollection.js


    var app = require('./tinyapp'),
      Model = require('./guestmodel'),
      Collection = require('backbone-browserify')
        .Collection.extend({
          model: Model
        }),

      create = function create(models) {
        models = models || app.pageData.guestList;

        return new Collection(models);
      },

      api = {
        create: create
      };

    module.exports = api;



Similar to how the model relays its own events, the view listens for DOM events and relays them on the app event aggregator so that other modules can listen (such as models or click loggers). Pay special attention to the `delegate()` and `relayClick()` functions:

Example&nbsp;6-3.&nbsp;guestlistview.js


    var app = require('./tinyapp'),

      // Assign Backbone.View to the View var.

      View = require('backbone-browserify').View,

      $ = app.$,
      checkedinClass = 'icon-check',
      listClass = 'dropdown-menu',
      guestClass = 'guest',


      // Rebroadcast DOM click events on the app event
      // aggregator.

      relayClick = function relayClick(e) {

        // Get the ID from the element and use it to
        // namespace the event.

        var sourceId = $(this).attr('id'),
          event = app.extend(e, {
            sourceId: $(this).attr('id')
          });

        app.trigger('toggled-checkedin', event);
      },

      delegate = function delegate() {

        // Delegate all click events to the parent element.

        this.$el.on('click', '.' %2B guestClass, relayClick);

        // Listen for changed events from the model
        // and make sure the element reflects the current
        // state.

        app.on('changed.checkedIn', function changeHandler(event) {
          var id = event.id;


          // Select the right list item by id.

          this.$el.find('http://www.cssmagic.net#' %2B id)
            .toggleClass(checkedinClass, event.checkedIn);

        }.bind(this));
      },

      render = function render(guestlist) {
        var $el = this.$el;

        $el.empty();

        // Loop over the passed-in guest models and render
        // them as li elements.

        guestlist.forEach(function (guestModel) {
          var $guest;
          guest = guestModel.toJSON();
          $guest = $('' %2B
            '' %2B guest.name %2B
            '');
          $guest.appendTo($el);
        });

        return this;
      },

      // Define the backbone view.
      GuestlistView = View.extend({
        tagName: 'ol',
        id: 'guestlist-view',
        className: listClass,
        initialize: delegate,
        render: render
      }),

      // Expose a factory function.
      create = function create() {
        return new GuestlistView();
      },

      api = {
        create: create
      };

    module.exports = api;



### MVC / MV*

_MVC_ (Model, View, Controller) is an architecture for managing separation of presentation logic from business domain logic in applications.

_Model_ refers to application state, and methods for manipulating and retrieving state. Usually there is a mechanism to notify observing views when the model state changes. There can be more than one view listening to a single model.

_View_ refers to the UI that gets drawn to the screen. It generally listens to the model so that it can update the UI when state changes, and sometimes to the controller so that it can handle presentation changes that do not impact the business domain (such as scrolling or showing / hiding UI).

_Controller_ refers to the user interaction logic. It updates the model in response to user requests. In some MVC interpretations, it is responsible for instantiating both the model and the view, and wiring up event listeners between them. Most server side web frameworks have controllers that route incoming requests to actions, which manipulate the model and serve updated views to the user.

![MVC][19]

Figure&nbsp;6-1.&nbsp;MVC

In the browser, where each element in the DOM also happens to be an event emitter, it might seem natural to handle a bit more of the input directly in the view (rather than the controller, as is common with MVC). Several alternatives to MVC have sprung up which deviate slightly from the MVC architecture. The alternatives are commonly grouped together and referred to as _MV*_. Typically there will be a notion of a model and a view (or something like them), and some other notions about how models and views interact.

The most popular MV* library is _Backbone_. Instead of models, views, and controllers, and an opinionated way of stitching them together, Backbone defines `View`, `Model`, `Collection`, and `Router` constructors. You are expected to subclass those constructors using the `.extend()` method available on each of the constructors.

Backbone's `.extend()` method creates classical style inheritance hierarchies, with all the pitfalls that go along with them. I have seen a couple of projects get into trouble by relying too much on Backbone's `.extend()` inheritance. Be careful about subclassing from subclasses. Instead, try passing exemplar prototypes and mixins into `.extend()` in order to facilitate code reuse. See [“Classical Inheritance is Obsolete”][20] and [“Prototypes”][21].

Backbone is not terribly opinionated about how you stitch the various pieces together, but most samples you'll find in the wild give a lot of responsibility to the view, which typically instantiates and keeps a reference to collections, models, or both. A typical arrangement might look something like this:

![Backbone][22]

Figure&nbsp;6-2.&nbsp;Backbone

`Backbone.Router` allows you to update the location bar when the user selects different views, or create actions that get triggered in response to the user navigating to a given URL. This is the primary responsibility of controllers on the server side, because URLs are generally the only way for the user to communicate with a server-side application. That's not the case in a client-side application.

`Backbone.View` is responsible for encapsulating view logic, such as how and when to display information. Views typically listen for DOM events, translate them into some intended action, and then emit events that other modules can subscribe to.

It's common to see views directly update models, collections, and routers, but when they do, it is a slippery slope. Often, developers get confused and start to put too much business logic in the view, which breaks the separation of concerns, and leads to code duplication and hard to find bugs across multiple views which need to share the same business rules. Instead, views should only manage the presentation concerns, and trigger events that other modules can listen for.

`Backbone.Model` is responsible for encapsulating state and business logic. When state changes, it emits events that views can subscribe to.

`Backbone.Collection` is an extremely useful abstraction that provides a managed way of dealing with collections of models, complete with Underscore's many functional methods, which you can use to query, filter, and sort your collections.

As you've already seen in the event examples, there are alternative ways to stitch Backbone's tools together. For example, for a an almost entirely decoupled implementation, you can route most of the communication through an event aggregator. That might look something like this:

![Backbone With Event Aggregator][23]

Figure&nbsp;6-3.&nbsp;Backbone With Event Aggregator

The solid lines represent direct references. Dashed lines represent event listeners. As you can see, with this setup, you don't need a lot of direct references, which allows your modules to be a lot less coupled. Views listen for state changes and view selection events, and emits user action events. The Router listens for user route selections, and emits route change events. Collections listen for add and remove events, and relay change events from the model. The model listens for UI input changes and emits change events.

This is the pattern you'll see developing in the examples, but it's certainly not the only way to handle separation of concerns in the browser.

There are many other MV* alternatives, including MVP, MVVM, RVP, etc... I'll leave it to you and Google to work out how all of them differ. The important thing to understand is that all of them exist to allow the separation of presentation logic and business domain logic. In short, your business logic should not be mixed in with your presentation logic and vice verse. You can explore various libraries frameworks at [TodoMVC.com][24].

### Presentation and DOM Manipulation

The primary purpose of a view in Backbone is to present the application's interface to the user and react to user input. As such, every Backbone view should be associated with some DOM. Backbone handles that association with the `.el` and `.$el` properties. The `.el` property is a direct reference to the view's root element, while `.$el` is a cached jQuery or Zepto object for the view's element.

Consider the following from `guestlistview.js`:

Example&nbsp;6-4.&nbsp;Creating a Backbone View


    // Define the backbone view.
    GuestlistView = View.extend({
      tagName: 'ol',
      id: 'guestlist-view',
      className: listClass,
      initialize: delegate,
      render: render
    }),

If specified, Backbone.View will construct `this.el` using the `tagName`, `className`, `id`, and `attributes` parameters. If you don't specify a `tagName`, it will create a `div`. You can also attach the view to an existing element by passing in a value for `.el`.

#### View Events

Backbone lets you pass in an events hash to declaratively specify event handlers. The hash takes the form, `{ 'eventName selector': 'callback' }`. If `'callback'` is a string, it will map to the method on the view object that matches the name. You can also pass an inline function or function reference. Backbone uses jQuery's `.on()` method, and delegates events to the view's root element (`this.el`) by default. If you omit the selector, the root element will be the target element.

Leaving off the selector is probably a code smell. Could you be delegating events to a parent element, instead?

##### Event Delegation

_Event delegation_ is the process of aggregating events from multiple sources to a single event handling mechanism. It's common to encounter large collections which share the same event handlers, but attach different listeners for each item in the collection.

For example, it might have been tempting to implement the guest list item as a separate view, and then rerender the guest template every time the status changed. That might look something like this:


    render = function render(data) {
      var $el = this.$el;

      // Prevent memory leaks in rerender cases.
      $el.off('click.' %2B this.className);
      $el.empty();

      processTemplate($el, data);

      // Reattach listener.
      $el.on('click.' %2B this.className, handleClick);

      return this;
    };

Note that this is essentially equivalent to passing an `events` hash to Backbone in the hypothetical guests `View.extend()`:


    events: {
      'click': handleClick
    }

Note that Backbone automatically delegates using jQuery's `.on()` method, however, passing a hash like this into an item-level view negates that capability. Instead, it will wire up a separate click handler for each list item.

This approach slows down the application in two ways: First, it takes longer to initialize, because of the extra wiring that must be performed for each event handler, for each item. It also consumes more memory, particularly if you chose the inline method to specify the event handler. If you do that, there will actually be a separate copy of the event handler function for each item.

You should be aware that you can leak memory if you bind event handlers to each item in a dynamic collection. For example, if you have a collection which utilizes the infinite scroll technique, you'll need to remove elements from the DOM as they scroll out of view to prevent browser performance issues or crashes. You must remember to remove the event listeners when you remove the items from the DOM. Backbone views have a `.remove()` method which will remove both the DOM listeners, and stop the view from listening for events from other objects.

You've already seen event delegation employed in the `guestlistview.js` `delegate()` function. Now you'll use the built in `Backbone.View` events hash to handle it, instead. Here's what the delegation looked like before:


    // From the delegate() function:
    // Delegate all click events to the parent element.
    this.$el.on('click', '.' %2B guestClass, relayClick);

You'll pull that code out of the delegate() function, and let Backbone handle it for you:


    // Define the backbone view.
    GuestlistView = View.extend({
      tagName: 'ol',
      id: 'guestlist-view',
      className: listClass,
      initialize: delegate,
      render: render,

      // Add the handler to the view object:
      relayClick: relayClick,

      // Let Backbone handle the event delegation:
      events: {
        'click .guest': 'relayClick'
      }
    }),

By delegating the `click` to the parent element, instead of to each list item, only one listener has to be attached per behavior, rather than one per behavior for each item in the list. What's more, you don't have to worry about removing the listener and adding it again if you need to render a list item again. In short, less code to write, and less risk of memory leaks.

Bonus: When you expose your event handlers on the view object, it makes them easier to unit test, because you don't have to figure out how to simulate the events in order to invoke the handlers. Keep in mind that events are a part of the API surface area, and event handlers should be tested, just like public methods.

If you implement list items as sub views, you still need to remember to call the item's `.remove()` method as the item scrolls away, because you still need to stop the view from listening to Backbone events. If you wire up any other kind of event listener (such as an app-level event aggregator), you should remember to remove those listeners, as well.

#### Templates

Templates help to separate display structure concerns from logic. Templates define the structure of your UI, along with some indication of where data should be injected. Templates can be reused with a variety of different data. A template processor combines data and templates to produce the DOM output that will be rendered to the screen.

It's common for template languages to support limited logic, such as if / else statements. I recommend that you avoid the temptation to use any of them, because they defeat the purpose of separating DOM structure from logic concerns -- instead, your logic just lives somewhere else. Even worse, your logic lives in two places, and it's even harder to find the source of bugs.

There are lots of different options for template rendering. Some common choices include Jade, Mustache, Haml, EJS, Plates and Pure. Plates (a Flatiron library) and Pure provide the ability to write templates in pure HTML, with no specialized DSL mixed in at all. Data gets associated via css-style selectors in the JavaScript view layer. Backbone relies on the Underscore library, which happens to include a simple template engine. For the sake of simplicity and familiarity, you'll see it used in the examples.

Let's revisit the guestlist view and replace the inline HTML with a template.

Before:


    '' %2B
      '' %2B guest.name %2B
      ''

After:


    
      
    

Even better, now that it isn't expressed in a JavaScript string, it's obvious that it doesn't belong in the JavaScript view layer at all. For a template this small, it wouldn't do any harm to simply embed it in the HTML for the page. You can do that using a script tag:


    
      
        
      
    

Unless the templates are tiny (a few lines), most templates should be in dedicated files. You may want to group them with corresponding views in the directory hierarchy. You can use a build step to pull templates into your HTML, or for templates that are only used rarely, fetch them as needed via AJAX.

If you're using a module system that doesn't pollute the global namespace, you'll need to explicitly require Underscore in order to access the template utility. If you're just including Backbone stand-alone, you don't need to do anything special to use Underscore's methods. Just use `_.template()` directly. In this case, Browserify will not leak dependencies into the global namespace, so you'll need to require it. In `guestlistview.js`, you'll insert the `require()` line like this:


    // Assign Backbone.View to the View var.
    View = require('backbone-browserify').View,

    // Grab the template utility from Underscore.
    template = require('underscore').template,

Of course, this isn't going to work until we add Underscore to `package.json`:


    "underscore": "*",

Save the file, and run:


    $ npm install

Now that the dependency is there, it's time to add the template code. Underscore lets you compile your HTML templates into an executable function in order to speed up template processing. You can do that near the top of the `render()` function so that it doesn't need to be called for every guest that gets rendered:

Example&nbsp;6-5.&nbsp;render() with Template


    render = function render(guestlist) {
      var $el = this.$el,

        // Compile the guest template.
        guestTemplate = template($('http://www.cssmagic.net#guestTemplate').html());

      $el.empty();


      // Loop over the passed-in guest models and render
      // them as li elements.
      guestlist.forEach(function (guestModel) {
        var guest;

        // Build the data object to pass into the template.
        guest = guestModel.toJSON();

        // Add the guestClass to the data object.
        guest.guestClass = guestClass;

        // Process the template data and append the output to the
        // list element.
        $el.append(guestTemplate(guest));
      });

      return this;
    }


Remember to run `grunt` to rebuild the project files, or set up a `grunt watch` task to build on file save. The tests should pass again. Having the lint and unit test tasks set up makes it easy to refactor like this with confidence, because you'll catch any mistakes early.

#### Web Components

_Web Components_ are a new standard way of creating reusable components and widgets with JavaScript, HTML, and CSS. The new standards currently lack widespread browser support, but may soon transform the way we write reusable components for JavaScript applications.

Shadow DOM %2B Custom elements = Web Components.

Web Components use a technology called _Shadow DOM_, which allows you to hide an entirely new document context, including HTML, CSS, and JavaScript. Shadow DOM is completely encapsulated from the main document. It gets its own document root (called _shadow root_), and its own execution context for JavaScript. Shadow DOM is also minimally affected by the CSS on the parent page. The DOM encapsulation features should feel familiar to anyone who has used iframes for encapsulation purposes.

Your shadow DOM can be made up of many other HTML elements, but they won't be visible in the DOM tree unless you specifically enable shadow DOM viewing in a debugger, such as Chrome Developer Tools.

_Custom tags_ are an important aspect of web components which allow you to simplify your DOM and write more semantic HTML. For example, if you need to create a knob widget, you can create a component called ``, which reads a lot better than ``. Custom tags can use prototypal inheritance to inherit the properties of other tags. You can simplify the creation of `` by sharing the prototype from ``, and setting some default properties (for example, by selecting `type="range"`). Use the new DOM method, `document.register()` to define the element.

Here's how you might define `` using the `document.register()` polyfill from Mozilla Web Components:

Example&nbsp;6-6.&nbsp;document.register()


    document.register('x-knob', {

      'prototype': Object.create( (window.HTMLInputElement ||
        window.HTMLSpanElement || window.HTMLElement).prototype ),

      'lifecycle': {

        created: function(proto) {
          this.type='range';
          console.log('x-knob created', this);
        },

        inserted: function() {
          console.log('x-knob inserted', this);
        },

        removed: function() {
          console.log('x-knob removed', this);
        },

        attributeChanged: function(attr, value) {
          console.log('x-knob attributeChanged', this, attr, value);
        }

      }
    });



Now that you have a definition, you can instantiate it with:


    var knob = document.createElement('x-knob');

Take a closer look at what happens:

Example&nbsp;6-7.&nbsp;Custom Tag Tests


    $(function () {

      test('document.register()', function () {

        equal(typeof document.register, 'function',
          'Should exist.');

      });

      test('document.createElement("x-knob")', function () {
        var knob = document.createElement('x-knob');

        ok(knob.getAttribute,
          'should produce a custom element.');
      });

      test('x-knob inheritance', function () {
        var knob = document.createElement('x-knob');

        ok(knob.checkValidity,
          'should inherit from input element.');
      });

      test('x-knob input type', function () {
        var knob = document.createElement('x-knob');

        equal(knob.type, 'range',
          'should have type="range".');
      });

    });


As exciting as all this is, it's still bleeding edge. There is currently disagreement in the community about this mechanism, arguing that if we go down this road, we'll lose a lot of the semantic meaning that the community has pushed so hard for. Ian Hickson argues along these lines:

Wouldn't it be better to add a new attribute so that we can preserve the semantics of existing elements? For example: ``. An obvious counter argument is that popular tags will become part of the semantic vernacular, and that agents will begin to recognize them, just as they recognize the semantics of many metadata formats, and many semantic extensions built on top of vanilla HTML. Another counter argument is that many custom elements will not have meaningful base elements whose semantics would be useful to build on.

As of this writing, none of these features are available for production use if you want solid cross-browser support. Mozilla has created a [custom tag polyfill][25] that you can experiment with today for non-production use. There is a small movement to create a library of GUI components using the current state of the web components specification and the Mozilla Web Components polyfill.

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

## Internationalization

Internationalization or localization is the process of converting text strings, numbers, dates, and currency into localized languages and representations. When you build a web application which grows to or near the saturation point in one country or culture, the easiest way to grow your user base is to expand to other cultures. One of the biggest hurdles to cross is obviously the translation of your app, but before you can even start down that road, you need to have some mechanisms in place to make that possible.

So when is the right time to get started? _Right now._ The biggest obstacle to translation is that you must plan for it, or budget a whole lot more time and energy to painstakingly go through the app, find all the strings that need translating, and then add support for it after the fact. By far, the easiest way to support internationalization is to plan for it up front, as soon as you start building templates. Any string that gets displayed to the user should be run through a translation function, first.

Instead of rolling your own translation solution, I recommend that you find a good library to handle it for you. Some good ones include [Moment.js][46] for dates, and [i18next][47] for strings.

Microsoft produced a very thorough solution that works with strings, numbers, currency, percentages, and dates. It's called [Globalize][48], and it was generously donated to the jQuery project, but it has since been made stand-alone and converted to work with AMD and node-style modules. You don't need to use jQuery to use Globalize.

One advantage of Globalize is that it uses a fairly comprehensive database of locale information that is hard to match in JavaScript. The only comparable solution that I'm aware of is the [Unicode Common Locale Data Repository (CLDR)][49]. CLDR is used by many of the top software development companies, including Apple, Google, IBM, and Adobe. Anybody can contribute to it. You could think of it is the Wikipedia of globalization data. There has even been talk of using CLDR data to generate culture files for Globalize, and that may become available in a future version.

If you don't want to wait, and you don't mind Twitter's choice of which locales to support, you may be able to use [twitter-cldr-js][50]. Twitter's CLDR package has a dependency on Ruby, so it may need to be shoe-horned into your Node project if you decide to go that route.

For now, I recommend Globalize.

Once you've chosen a solution, getting your translation data where it needs to be is your next challenge. It doesn't make sense to ship the whole catalog of translation data to the client if they only need a single locale set.

If you render all your templates on the client side, you can inject a default set of translations into the page data at page load time, or make an asynchronous call to fetch translations, or both.

There have been apps where the page renders, and then an alternate translation loads, and the page re-renders. Those cases can present an awkward user experience, so make sure that you resolve the preferred locale before you invoke your initial page render.

Once it has been determined that the user has a locale preference different from the default, it's important to store that information so that you can present them with their preferred locale experience at every subsequent page load. It's a good idea to set a cookie that contains this information. It's probably not a good idea to store that information in a server-side session, because if the user is not logged in, or the session is expired, the user might have trouble logging in and getting their preferred locale experience back.

### Locale Determination

Unfortunately, there is no easy way to get a user's true locale preference on the client side without asking them. However, the browser's request header can tell you the user's preference. There's a locale module for express that makes the best match easy to get:


    var locale = require('locale'),
      // ... other requires ...
      // Tell locale which locales you support.
      supportedLocales = ['en', 'en_US', 'ja'];

    // locale() returns a middleware function that pulls the
    // req.headers["accept-language"] attribute and runs the value
    // through locales.best(supported) to determine the closest
    // matching locale.
    app.use( locale(supportedLocales) );


* * *

[Prev][3]&nbsp;
&nbsp;
&nbsp;

Chapter&nbsp;5.&nbsp;Modules&nbsp;
[Home][51]
&nbsp;

© 2013, O’Reilly Media, Inc.

  * [Terms of Service][52]
  * [Privacy Policy][53]
  * Interested in [sponsoring content?][54]

   [1]: http://www.cssmagic.net/books/1234000000262
   [2]: http://www.cssmagic.net#
   [3]: http://www.cssmagic.net/ch05.html
   [4]: http://wordpress.org/
   [5]: http://microjs.com/
   [6]: http://addyosmani.github.com/todomvc/
   [7]: http://www.slideshare.net/nzakas/scalable-javascript-application-architecture
   [8]: http://addyosmani.com/largescalejavascript/
   [9]: http://tinyappjs.com/
   [10]: http://www.cssmagic.net/ch06.html#chanatuqi0000c2rw41l8on79
   [11]: http://www.cssmagic.net/ch06.html#chanb2cc80001c2rw22adtqek (Getting Started)
   [12]: https://developer.mozilla.org/en-US/docs/DOM
   [13]: http://robey.github.com/kestrel/
   [14]: http://aws.amazon.com/sqs/
   [15]: http://martinfowler.com/eaaDev/EventAggregator.html
   [16]: http://martinfowler.com/eaaDev/MediatedSynchronization.html
   [17]: http://martinfowler.com/eaaDev/EventSourcing.html
   [18]: http://www.cssmagic.net/ch05.html (Chapter&nbsp;5.&nbsp;Modules)
   [19]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0601.png
   [20]: http://www.cssmagic.net/ch04.html#chcss2i1z00025eilzn6ki8ds (Classical Inheritance is Obsolete)
   [21]: http://www.cssmagic.net/ch04.html#chcsrdou100015eilvj6l9inj (Prototypes)
   [22]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0602.png
   [23]: http://orm-chimera-prod.s3.amazonaws.com/1234000000262/figs/0603.png
   [24]: http://todomvc.com/
   [25]: http://github.com/mozilla/web-components
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
   [46]: http://momentjs.com/
   [47]: http://i18next.com/
   [48]: https://github.com/jquery/globalize
   [49]: http://cldr.unicode.org/
   [50]: https://github.com/twitter/twitter-cldr-js
   [51]: http://www.cssmagic.net/index.html
   [52]: http://oreilly.com/terms/
   [53]: http://oreilly.com/oreilly/privacy.csp
   [54]: mailto:scordesse%40oreilly.com
 

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
