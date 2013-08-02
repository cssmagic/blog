# [译] [PJA] [406] 创建原型对象的类库

> * Original: [A Prototypal Object Creation Library - Chapter 4. Objects - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html#a_prototypal_object_creation_library)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## A Prototypal Object Creation Library

JavaScript's object capabilities are really flexible, but `Object.create()` isn't the easiest way to create a fully featured object. There are still quite a few hoops to jump through just to create a collection of flyweight objects that support data privacy.

Lots of libraries provide mechanisms to mimic classical inheritance, but there are no widespread libraries that simplify best-practice prototypal object creation. That doesn't have to be the case. You can create your own with about 100 lines of code.

### Object Creation

Start by creating a generic function that will return a new object. It should take two objects and a function as parameters. The first object will be the prototype for the new object. The second will be instance properties for the new object, and the third will be a function that you can use to facilitate data privacy and expose privileged methods. The signature looks like this:

    o(sharedProperties, instanceProperties, initFunction)

Here's a more detailed look at how you'd use it to create an object from scratch:

    var testObj = o(
      {
        sharedProp: 'shared property'
      },

      {
        instanceProp: 'instance property'
      },

      function () {
        var privateProp = 'private property';

        this.plugin('getPrivate', function getPrivate() {
          return privateProp;
        });

        return this;
      }
    );

Or, you could pass in a named parameters object, instead:

    o(options)

Which looks something like this:

    var testObj2 = o({
      sharedProperties: {
        sharedProp: 'shared property 2'
      },

      instanceProperties: {
        instanceProp: 'instance property 2'
      },

      initFunction: function () {
        var privateProp = 'private property 2';

        this.plugin('getPrivate', function getPrivate() {
          return privateProp;
        });

        return this;
      }
    });

The third parameter is an initialization function. When the generic factory runs, the last line calls this function using the current object as "this" and returns whatever you return from `initFunction()`.

The primary purpose is so that you can use it to wrap private data and set up privileged methods (using the as yet undefined `.plugin()` method). If you don't pass in a function it will use `defaultInit()`:

    defaultInit = function init() {
      return this;
    };

Here's the implementation:

    o = function o(sharedProperties, instanceProperties, initFunction) {
      var optionNames = 'sharedProperties, instanceProperties, initFunction',
        config,
        fn,
        obj;

      config = getConfig(optionNames, sharedProperties, instanceProperties,
          initFunction);
      config.initFunction = config.initFunction || defaultInit;
      fn = config.sharedProperties;

      bless(fn);

      obj = extend(Object.create(fn), {fn: fn},
        config.instanceProperties);

      return config.initFunction.call(obj);
    };

The `getConfig()` function just makes the call signature more flexible. If the user passes in an options object, it's used to populate the `config` object. Otherwise, `config` is constructed from the formal parameters. Either way, if any parameters are missing, empty default values are used, instead:

    getConfig = function getConfig(optionNames) {
      var config = {}, // New config object
        // Comma separated string to Array
        optionNames = optionNames.split(','),
        // Turn arguments into array, starting at index 1
        args = [].slice.call(arguments, 1);

      optionNames.forEach(function (optionName, index) {
        // Strip whitespace
        optionName = optionName.trim();
        // Use first argument as params object if it exists...
        config[optionName] = args[0][optionName]
          || args[index]; // or grab the formal parameter.
      });

      return config;
    };

After the configuration is returned from `getConfig()`, the prototype gets some extra functionality from the `bless()` function:

    var plugins = {},
      plugin = function plugin(name, fn) {
        this.fn[name] = fn;
      },

      bless = function bless(fn) {
        fn.plugin = plugin;

        extend(fn, plugins);

        return fn;
      };

In the future, it will be possible to configure exactly what gets tacked on with `bless()`, but for now, assume that `plugins` is an empty object. The `.plugin()` method will allow you to extend the object's prototype at run time.

The new object gets created with `Object.create()` using `sharedProperties` as the prototype (if you're a jQuery plugin developer, you'll recognize the prototype alias, `fn`). All prototype properties are shared among all instances of the object. If you change a prototype property at runtime, the value change is reflected on every instance of the object.

The object is then extended with instance properties, and passed into `initFunction()`, which returns `this` after it has run whatever initialization procedure you pass in.

Some examples and tests will demonstrate the behavior more clearly:

    var testObj = o({
          sharedProp: 'shared property'
        },

        {
          instanceProp: 'instance property'
        },

        function () {
          var privateProp = 'private property';

          this.plugin('getPrivate', function getPrivate() {
            return privateProp;
          });

          return this;
        }
      ),

      testObj2 = o({
        sharedProperties: {
          sharedProp: 'shared property 2'
        },
        instanceProperties: {
          instanceProp: 'instance property 2'
        },
        initFunction: function () {
          var privateProp = 'private property 2';

          this.plugin('getPrivate', function getPrivate() {
            return privateProp;
          });
          return this;
        }
      });

    test('o with arguments creates valid objects', function (){

      ok(testObj.hasOwnProperty('instanceProp'),
        'Instance property should be on instance.');

      equal(testObj.instanceProp, 'instance property',
        'Instance property should be "instance property".');

      ok(!testObj.hasOwnProperty('sharedProp'),
        'Shared prop should NOT be on instance.');

      equal(testObj.sharedProp, 'shared property',
        'Shared property should be "shared property"');

      ok(!testObj.privateProp,
        'Private property should be private.');

      ok(!testObj.hasOwnProperty('getPrivate'),
        '.plugin() should NOT add methods to instance API.');

      equal(testObj.getPrivate(), 'private property',
        'Private property should be "private property".');
    });

A similar set of tests will pass for `testObj2`. The only real difference is that `testObj2` sets the properties by name, rather than relying on the argument order. You might find that if you write your code with named parameters, it will be more self-descriptive.

### Factory Creation

If you plan to create a lot of similar objects, it might be more useful to create a specialized factory to produce them. Doing so makes it easy to create collections of objects that all share the same prototype and initialization. The `factory()` method could take a nearly identical signature -- essentially creating a prototype and default instance properties that you can customize further when you initialize new instances.

Here's the source:

    factory: function factory(sharedProperties, defaultProperties,
        initFunction) {
      var optionNames = 'sharedProperties, defaultProperties, initFunction',
        config;

      config = getConfig(optionNames, sharedProperties, defaultProperties,
          initFunction);
      config.initFunction = config.initFunction || defaultInit;

      return bless(function (options) {
        var defaultProperties = config.defaultProperties || {},
          sharedProperties = config.sharedProperties || {},
          instance = extend(defaultProperties, options),
          obj = extend(o(sharedProperties, instance,
            config.initFunction));
        return config.initFunction.call(obj);
      });
    },

The `factory()` method shares the same `getConfig()` method for the flexible call signature. It stores the configuration in the closure and returns a new function that you'll use to initialize individual instances - a good example of partial application discussed in the Functions chapter.

You can recreate the car factory examples from before with this new tool:

    var car = o.factory({
        sharedProperties: {
          gas: function gas(amount) {
            amount = amount || 10;
            this.mph %2B= amount;
            return this;
          },

          brake: function brake(amount) {
            amount = amount || 10;
            this.mph = ((this.mph - amount) &lt; 0)
              ? 0
              : this.mph - amount;
            return this;
          }
        },

        instanceProperties: {
          color: 'pink',
          direction: 0,
          mph: 0
        },

        initFunction: function init() {
          var isParkingBrakeOn = false;

          this.toggleParkingBrake = function
                toggleParkingBrake() {
            isParkingBrakeOn = !isParkingBrakeOn;
          };

          return this;
        }
      }),

      myCar = car({
        color: 'orange',
        mph: 5
      });

    test('odotjs factory with private variable.', function () {
      ok(myCar.color, 'Has a color');

      equal(myCar.gas().mph, 15,
        '.gas() should add 10mph.');

      equal(myCar.brake(5).mph, 10,
        '.brake(5) should subtract 5mph.');

      ok(myCar.toggleParkingBrake,
        '.toggleParkingBrake works.');
    });

The full library source is about 140 lines, including comments:

    (function (exports) {
      'use strict';
      var name = 'odotjs',
        // Adapted from Underscore.
        extend = function extend(obj) {
          var args = [].slice.call(arguments, 1);
          args.forEach(function(source) {
            for (var prop in source) {
              if (source[prop] !== void 0) obj[prop] = source[prop];
            }
          });
          return obj;
        },
        plugins = {},
        // Add to the global plugin collection.
        addPlugins = function (newPlugins) {
          extend(plugins, newPlugins);
        },
        // Add to the current object prototype.
        plugin = function plugin(name, fn) {
          this.fn[name] = fn;
        },
        // Pass the global plugins to the object
        // prototype.
        bless = function bless(fn) {
          fn.plugin = plugin;

          extend(fn, plugins);

          return fn;
        },
        o,
        api,
        defaultInit = function init() {
          return this;
        },

        /**
         * The user can pass in the formal parameters, or a named
         * parameters. Either way, we need to initialize the
         * variables to the expected values.
         *
         * @param {String} optionNames Parameter names.
         *
         * @return {object} New configuration object.
         */
        getConfig = function getConfig(optionNames) {
          var config = {}, // New config object
            // Comma separated string to Array
            optionNames = optionNames.split(','),
            // Turn arguments into array, starting at index 1
            args = [].slice.call(arguments, 1);

          optionNames.forEach(function (optionName, index) {
            // Strip whitespace
            optionName = optionName.trim();
            // Use first argument as params object...
            config[optionName] = args[0][optionName]
              || args[index]; // or grab the formal parameter.
          });

          return config;
        };

      /**
       * Create a new, blessed object with public properties,
       * shared properties (on prototype), and support for
       * privacy (via initFunction).
       *
       * @param {object} sharedProperties Prototype
       * @param {object} instanceProperties Instance safe
       * @param {function} initFunction Init and privacy
       *
       * @return {object}
       */
      o = function o(sharedProperties, instanceProperties,
          initFunction) {
        var optionNames = 'sharedProperties, instanceProperties,'
            %2B ' initFunction',
          config,
          fn,
          obj;

        config = getConfig(optionNames, sharedProperties,
          instanceProperties, initFunction);
        config.initFunction = config.initFunction || defaultInit;
        fn = config.sharedProperties;

        bless(fn);

        obj = extend(Object.create(fn), {fn: fn},
          config.instanceProperties);

        return config.initFunction.call(obj);
      };

      bless(o);

      extend(o, {
        /**
         * Returns an object factory that stamps out objects
         * using a specified shared prototype and init.
         *
         * @param {object} sharedProperties Prototype
         * @param {object} defaultProperties Instance safe
         * @param {function} initFunction Init and privacy
         *
         * @return {function} A new object factory.
         */
        factory: function factory(sharedProperties, defaultProperties,
            initFunction) {
          var optionNames = 'sharedProperties, defaultProperties,'
              %2B ' initFunction',
            config;

          config = getConfig(optionNames, sharedProperties,
            defaultProperties, initFunction);
          config.initFunction = config.initFunction || defaultInit;

          return bless(function (options) {
            var defaultProperties = config.defaultProperties || {},
              sharedProperties = config.sharedProperties || {},
              instance = extend(defaultProperties, options),
              obj = extend(o(sharedProperties, instance,
                config.initFunction));
            return config.initFunction.call(obj);
          });
        },
        addPlugins: addPlugins
      });

      api = {
        getConfig: getConfig
      };
      api[name] = o;

      extend(exports, api);
    }((typeof exports === 'undefined')
        ? this
        : exports));

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
