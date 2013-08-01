# [译] [PJA] [403] 原型

> * Original: [Prototypes](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html#chcsrdou100015eilvj6l9inj)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Prototypes

A _prototype_ is an object intended to model other objects after. It is similar to a class in that you can use it to construct any number of object instances, but different in the sense that it is an object itself. There are two ways that prototypes can be used: You can delegate access to a single, shared prototype object (called a delegate), or you can make clones of the prototype.

### The Prototype Property

All objects automatically inherit a `prototype` property from the object's constructor function. Object literals inherit the `Object` prototype. Alternatively, you can specify a prototype to set when you create an object via the `Object.create()` method. `Object.create()` was added with JavaScript 1.5, so you may need to polyfill it. Here's the `Object.create()` polyfill from the Mozilla Developer Network documentation:

    if (!Object.create) {
      Object.create = function (o) {
        if (arguments.length &gt; 1) {
          throw new Error('Object.create implementation'
          %2B ' only accepts the first parameter.');
        }
        function F() {}
        F.prototype = o;
        return new F();
      };
    }

As you can see, this `Object.create()` polyfill is simply a shortcut that creates a new constructor function, sets the object you pass in as the constructor's `prototype`, and then returns a new object with that `prototype`. Here's how you use it:

    var switchProto = {
        isOn: function isOn() {
          return this.state;
        },

        toggle: function toggle() {
          this.state = !this.state;
          return this;
        },

        state: false
      },
      switch1 = Object.create(switchProto),
      switch2 = Object.create(switchProto);

    test('Object.create', function () {
      ok(switch1.toggle().isOn(),
        '.toggle() works.'
      );

      ok(!switch2.isOn(),
        'instance safe.'
      );
    });

Simply pass any object into `Object.create()`, and it will be be set as the `prototype` for the newly created object. As you can see, the `prototype` property has some special behavior:

Notice that `state` is on the prototype, but changing state on `switch1` did not change `state` on `switch2`. Properties on the prototype act like defaults. When you set them on the instance, the instance value overrides the value for that instance, only.

It's important to note though that if you mutate an object or array property on the prototype, that mutation will be shared on the prototype. If you replace the whole property, the change is only reflected on that instance:

    var switchProto = {
        isOn: function isOn() {
          return this.state;
        },

        toggle: function toggle() {
          this.state = !this.state;
          return this;
        },

        meta: {
          name: 'Light switch'
        },

        state: false
      },
      switch1 = Object.create(switchProto),
      switch2 = Object.create(switchProto);

    test('Prototype mutations.', function () {
      switch2.meta.name = 'Breaker switch';

      equal(switch1.meta.name, 'Breaker switch',
        'Object and array mutations are shared.'
      );

      switch2.meta = { name: 'Power switch' };

      equal(switch1.meta.name, 'Breaker switch',
        'Property replacement is instance-specific.'
      );
    });

In the code above, `switchProto` has a property called `meta`. When you change a sub-property of meta, it mutates the object attached to the `prototype`, but when you replace the whole `meta` object with a new object, it overrides the property for that instance, only.

Sharing state (non-method data) on a prototype property is commonly considered an anti-pattern in the JavaScript community, because accidental mutations of shared properties are a common source of bugs when you do it.

### Prototype Cloning

Sometimes you don't want to share data on a prototype property. Instead, you want each instance to have its own unique copy of the prototype's properties. Popular JavaScript libraries have been shipping with a suitable method for cloning prototypes for several years. The cloning method is usually called `.extend()`. You pass in an object to extend, followed by any number of objects to extend from.

Unfortunately, `extend()` is not included in the JavaScript specification yet (`Object.extend()` may be included in the next version of JavaScript), but it is included in both jQuery and Underscore, and its implementation is simple. Here is the source from Underscore:

    _.extend = function(obj) {
      each(slice.call(arguments, 1), function(source) {
        for (var prop in source) {
          obj[prop] = source[prop];
        }
      });
      return obj;
    };

As you can see, it takes the first argument as a target (`obj`), iterates through remaining `source` arguments, and copies all of the public properties from each `source` to the target `obj`. The last object you pass in takes precedence if there are any property collisions.

Here's how you use it to clone a prototype object:

    var switchProto = {
          isOn: function isOn() {
            return this.state;
          },

          toggle: function toggle() {
            this.state = !this.state;
            return this;
          },

          meta: {
            name: 'Light switch'
          },

          state: false
        },

        switch1 = extend({}, switchProto),
        switch2 = extend({}, switchProto);

      test('Prototype clones.', function () {

        switch1.isOn.isShared = true;

        ok(!switch2.isShared,
          'Methods are copied for each instance, not shared.'
        );

        ok(switch1.toggle().isOn(),
          '.toggle() works.'
        );

        ok(!switch2.isOn(),
          'instance safe.'
        );

        switch2.meta.name = 'Breaker switch';

        equal(switch1.meta.name, 'Breaker switch',
          'Object and array mutations are shared.'
        );

        switch2.meta = { name: 'Power switch' };

        equal(switch1.meta.name, 'Breaker switch',
          'Property replacement is instance-specific.'
        );
      });

In the `extend()` calls, you pass the new, empty object `{}` as the destination object, followed by the prototype, `switchProto` as the source object.

The primary difference between the clone method and the `prototype` property method is that the clone method will copy the value of each property for every object instance, whereas the `prototype` property method is more memory efficient. It stores only one copy of each default property setting, until you need to override properties at the instance level.

As it turns out, it's often a good idea to employ a mix of both techniques -- using the `prototype` property to share public methods between objects, and the clone method for data that can't be safely shared between instances.

### The Flyweight Pattern

The _flyweight pattern_ conserves system resources by storing all reusable properties and methods on a delegate object, as opposed to storing copies of them on every instance. This can save a lot of memory and improve system performance dramatically if there are lots of objects of the same type.

In other languages, you have to jump through some hoops to set up a delegate object, and defer all method calls and access to it. In JavaScript, the `prototype` property serves as a built-in flyweight delegate. You don't need to worry about wiring it up yourself.

Imagine you're programming a video game, and there is a common enemy that has dozens or hundreds of copies in the game world. Each copy stores the enemy's base stats, such as strength and speed, along with methods for all of its attacks and defenses. It also stores the enemy's position in the game world and current health. You can optimize these objects in JavaScript by storing all of that data on the enemy `prototype`. When there is a change to the enemy's health or position, those changes are made to the enemy instance, while all the other data and methods are delegated to the `prototype`.

    var enemyPrototype = {
        name: 'Wolf',
        position: { // Override this with setPosition
          x: 0,
          y: 0
        },
        setPosition: function setPosition (x, y) {
          this.position = {
            x: x,
            y: y
          };
          return this;
        },
        health: 20, // Overrides automatically on change
        bite: function bite() {
        },
        evade: function evade() {
        }
      },

      spawnEnemy = function () {
        return Object.create(enemyPrototype);
      };

    test('Flyweight pattern.', function () {
      var wolf1 = spawnEnemy(),
        wolf2 = spawnEnemy();

      wolf1.health = 5;
      ok(wolf2.health = 20,
        'Primitives override automatically.');

      ok(wolf1.setPosition(10, 10)
          .position.x === 10, 'Object override works.');
      equal(wolf2.position.x, 0,
          'The prototype should remain unchanged.');
    });

Because moving data to the `prototype` delegate is so easy in JavaScript, it's common to employ the flyweight pattern for nearly all object methods. That's what's going on when you see lines like:

    MyConstructor.prototype.myMethod = function () {
      // A method to be shared...
    };

It's less common to see the prototype employed for member data, but it's a perfectly reasonable place to store default values that are commonly reused. Just be mindful that you'll need to replace member objects and arrays rather than mutate them in-place if you want your changes to be instance safe.

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
