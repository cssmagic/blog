# [译] 博文标题

> * Original: [XXXXXXXXXXXXXXXXXXXX](xxxxxxxxxxxxxxxxxxx)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Chapter&nbsp;4.&nbsp;Objects

With the combination of prototypal inheritance, the ability to add properties to objects dynamically, and closures, JavaScript has one of the most flexible and expressive object systems available in any popular programing language.

In JavaScript, all types of functions, arrays, key/value pairs, and data structures in general are really objects. Even primitive types get the object treatment when you refer to them with the property access notations. They get automatically wrapped with an object so that you can call their prototype methods. For example:


    'tonya@example.com'.split('@')[1]; // =&gt; example.com

Primitive types behave like objects when you use the property access notations, but you can't assign new properties to them. Primitives get wrapped with an object temporarily, and then that object is immediately thrown away. Any attempt to assign values to properties will seem to succeed, but subsequent attempts to access that new property will fail.

JavaScript's object system is so powerful and expressive that most of the complexity in common OO patterns melts away when you reproduce them in JavaScript. You simply don't need all of the common cruft to accomplish the stated goals. For instance, because JavaScript is classless, and it's possible to create an object on-demand at the precise moment it's needed (lazy instantiation), the singleton is reduced to an object literal:


    var highlander = {
        name: 'McLeod',
        catchphrase: 'There can be only one.'
      };

As you continue through this chapter, you'll see that a lot of the overhead associated with several other GoF design patterns melts away when you understand how to take advantage of JavaScript's native object capabilities.

You may be aware that JavaScript is not a classical OO language. It's a prototypal language. However, most JavaScript training materials ignore some of the implications of that paradigm shift. It's time to get a firmer handle on exactly what _prototypal_ means, and how you can take advantage of it to write better, faster, more readable, and more efficient code. It might help to get a better sense of the shortcomings of classical inheritance, first.

## Classical Inheritance is Obsolete

&nbsp;

"Those who are unaware they are walking in darkness will never seek the light."

&nbsp;

&nbsp;
\--Bruce Lee

In "Design Patterns: Elements of Reusable Object Oriented Software", the Gang of Four opened the book with two foundational principles of object oriented design:

  1. Program to an interface, not an implementation.

  2. _Favor object composition over class inheritance._

In a sense, the second principle could follow from the first, because inheritance exposes the parent class to all child classes. The child classes are all programming to an implementation, not an interface. Classical inheritance breaks the principle of encapsulation, and tightly couples the child class to its ancestors.

Think of it this way: _Classical inheritance is like Ikea furniture_. You have a bunch of pieces that are designed to fit together in a very specific way. If everything goes exactly according to plan, chances are high that you'll come out with a usable piece of furniture, but if anything at all goes wrong or deviates from the preplanned specification, there is little room for adjustment or flexibility. Here's where the analogy (and the furniture, and the software) breaks down: The design is in a constant state of change.

_Composition is more like Lego blocks_. The various pieces aren't designed to fit with any specific piece. Instead, they are all designed to fit together with any other piece, with few exceptions.

When you design for classical inheritance, you design a child class to inherit from a specific parent class. The specific parent class name is usually hard coded right in the child class, with no mechanism to override it. Right from the start, you're boxing yourself in -- limiting the ways that you can reuse your code without rethinking its design at a fundamental level.

When you design for composition, the sky is the limit. As long as you can successfully avoid colliding with properties from other source objects, objects can be composed and reused virtually any way you see fit. Once you get the hang of it, composition affords a tremendous amount of freedom compared to classical inheritance. For people who have been immersed in classical inheritance for years, and learn how to take real advantage of composition (specifically using prototypal techniques), it is literally like walking out of a dark tunnel into the light and seeing a whole new world of possibilities open up for you.

Back to "Design Patterns". Why is the seminal work on Object Oriented design so distinctly anti-inheritance? Because inheritance causes several problems:

  1. _Tight coupling._ Inheritance is the tightest coupling available in OO design. Descendant classes have an intimate knowledge of their ancestor classes.

  2. _Inflexible hierarchies._ Single parent hierarchies are rarely capable of describing all possible use cases. Eventually, all hierarchies are "wrong" for new uses -- a problem that necessitates code duplication.

  3. _Multiple inheritance is complicated._ It's often desirable to inherit from more than one parent. That process is inordinately complex and its implementation is inconsistent with the process for single inheritance, which makes it harder to read and understand.

  4. _Brittle architecture._ Because of tight coupling, it's often difficult to refactor a class with the "wrong" design, because much existing functionality depends on the existing design.

  5. _The Gorilla / Banana problem._ Often there are parts of the parent that you don't want to inherit. Subclassing allows you to override properties from the parent, but it doesn't allow you to select which properties you want to inherit.

These problems are summed up nicely by Joe Armstrong in "Coders at Work", by Peter Siebel:

&nbsp;

“The problem with object-oriented languages is they've got all this implicit environment that they carry around with them. You wanted a banana but what you got was a gorilla holding the banana and the entire jungle.”

&nbsp;

&nbsp;
\--Joe Armstrong

Inheritance works beautifully for a short time, but eventually the app architecture becomes arthritic. When you've built up your entire app on a foundation of classical inheritance, the dependencies on ancestors run so deep that even reusing or changing trivial amounts of code can turn into a gigantic refactor. Deep inheritance trees are brittle, inflexible, and difficult to extend.

More often than not, what you wind up with in a mature classical OO application is a range of possible ancestors to inherit from, all with slightly different but often similar configurations. Figuring out which to use is not straightforward, and you soon have a haphazard collection of similar objects with unexpectedly divergent properties. Around this time, people start throwing around the word "rewrite" as if it's an easier undertaking than refactoring the current mess.

Many of the patterns in the GoF book were designed specifically to address these well-known problems. In many ways, the book itself can be read as a critique of the shortcomings of most classical OO languages, along with the accompanying lengthy work-arounds. In short, patterns point out deficiencies in the language. You can reproduce all of the GoF patterns in JavaScript, but before you start using them as blueprints for your JavaScript code, you'll want to get a good handle on JavaScript's prototypal and functional capabilities.

For a long time, many people were confused about whether JavaScript is truly object oriented, because they felt that it lacked features from other OO languages. Setting aside the fact that JavaScript handles classical inheritance with less code than most class-based languages, coming to JavaScript and asking how to do classical inheritance is like picking up a touch screen mobile phone and asking where the rotary dial is. Of course people will be amused when the next thing out of your mouth is, "if it doesn't have a rotary dial, it's not a telephone!"

JavaScript can do most of the OO things you're accustomed to in other languages, such as inheritance, data privacy, polymorphism, and so on. However, JavaScript has many native capabilities that make some classical OO features and patterns obsolete. It's better to stop asking "how do I do classical inheritance in JavaScript?", and start asking, "what cool new things does JavaScript enable me to do?"

I wish I could tell you that you'll never have to deal with classical inheritance in JavaScript. Unfortunately, because classical inheritance is easy to mimic in JavaScript, and many people come from class-based programming backgrounds, there are several popular libraries which feature classical inheritance prominently, including Backbone.js, which you'll have a chance to explore soon. When you do encounter situations in which you're forced to subclass by other programmers, keep in mind that inheritance hierarchies should be kept as small as possible. Avoid subclassing subclasses, remember that you can mix and match different code reuse styles, and things will go more smoothly.

## Fluent Style JavaScript

&nbsp;

I have not invented a "new style," composite, modified or otherwise that is set within distinct form as apart from "this" method or "that" method. On the contrary, I hope to free my followers from clinging to styles, patterns, or molds.

...The extraordinary part of it lies in its simplicity.

&nbsp;

&nbsp;
\--Bruce Lee

_Programmers with a background in other languages are like immigrants to JavaScript_. They often code with an accent -- preconceived notions about how to solve problems. For example, programmers with a background in classical OO tend to have a hard time letting constructors go. Using constructor functions is a clear and strong _accent_, because they are completely unnecessary in JavaScript. They are a waste of time and energy.

Unfortunately, most JavaScript learning materials will teach you that you build objects using constructor functions.

There are serious limitations to this way of doing things. The first is that you must always call a constructor using the `new` keyword. Failing to do so will pass the global object in as `this`. Your properties and methods will clobber the global namespace. If you instantiate more than one object, they will not be instance safe. In strict mode, the situation when you forget `new` is a little different: `this` is useless, and trying to assign properties to it will throw an error.

There's a simple workaround that requires a boilerplate check inside every constructor function, but since any function can return an object in JavaScript, you end up using `new` sometimes, but not all the time. A convention has sprung up in JavaScript to help you remember when to use `new` and when not to. Constructor functions always begin with a capital letter. If you're calling a function that begins with a capital letter, use `new`. Unfortunately, lots of library developers uppercase the first letter of their library namespace, regardless of whether or not it's a constructor function, so even that solution is less than ideal. And that's not even the real problem.

The real problem is that using constructors will almost always get you stuck thinking in classical OO mode. The constructor becomes analogous to a class. You might start to think, "I want to subclass x..." and that's where you get into trouble. You're ignoring two of the best features of JavaScript: Dynamic object extension (you can add properties to any object in JavaScript after instantiation) and prototypal inheritance. The two combined are a much more powerful and flexible way to reuse code than classical inheritance.

Programmers who learned JavaScript as a first language are far less likely to be attached to classical OO and inheritance, because it's harder to implement in JavaScript than the simpler, _native_ alternatives. You can learn a lot about JavaScript by reading the code of experienced JavaScript natives who have not been influenced by classical inheritance.

Most languages have many different accents and grammars. In fact, almost all disciplines spin off into different schools of thought. Martial arts has kick boxing, jui jitsu, boxing, karate, kung fu, and so on. Each emphasize different philosophies, mechanics and core techniques. In his famous book, "The Tao of Jeet Kun Do", Bruce Lee advised that you develop your own style. Study and take in all the good things about different styles, pick what you like and discard the rest to create your own unique style. It's good advice that lies at the heart of Mixed Martial Arts - a discipline that combines the best features of several fighting styles.

The same is true with programming languages. JavaScript itself is a fusion of the best ideas from Scheme (lambda), Self (prototypal inheritance), and Java (syntax).

To coin a phrase, _fluent style_ simply discards inefficient constructs and takes advantage of JavaScript's strengths and efficiencies:

  * _Lambdas and closures_ are a language unto themselves. Lambda expressions are simple, elegant, and powerful. Literally anything that can be expressed as a computable algorithm can be expressed through lambdas.

  * _Object literal notation_ is the fastest route to a working object instance from scratch. Also take advantage of its sibling, _array literal notation_.

  * _Dynamic object extension_ allows you to easily use mixins, composition, and aggregation for code reuse, even after object instantiation. Subclassing requires a lot of extra typing, and gains you nothing. It makes your code brittle and inflexible.

  * _Prototypes_ allow you to clone instances of existing objects to create new ones, and share generic methods on the `prototype` property.

  * _Factories_ are a more flexible and less verbose alternative to constructor functions in JavaScript. You don't need constructors to create new objects. Any function in JavaScript can return a new object, and with less code than a constructor requires. Unlike constructors, factories hide instantiation details from the caller, and there's no need to use the awkward and superfluous `new` keyword when invoking a factory. Factories make it easy to combine any of the above techniques, and even change implementations at run-time without changing the way that objects are instantiated from outside the factory.

  * _Fluent APIs_. (Not to be confused with fluent style JavaScript). Prototypes make it easy to build fluent APIs. A fluent API is an interface that reads a bit like natural language. Fluent APIs are usually chainable, but don't need to be chained to be fluent. The defining characteristic is that each method returns an object which has additional methods on it which make sense as a next step. In this way, methods can be strung together in short sentences, each method acting on the return value of the last. jQuery and Jasmine are good examples of popular fluent APIs in JavaScript.

Some of the techniques used in fluent style JavaScript were popularized by the Prototype and jQuery libraries, but fluent style wasn't invented by anybody in particular. There isn't anything new or unique about it that hasn't been said a thousand times before. It's simply the natural evolution of a language with this particular blend of features at its core. It is not a style in itself so much as the shedding of unnecessary styles and techniques which are often habits left over from other languages.

Even giving "fluent style" a name seems a bit silly. It's necessary only to distinguish it from the cumbersome styles taught in most JavaScript books and tutorials. In short, fluent style isn't really a particular formal style -- _It's just what fluent JavaScripter's do_.

As time marches on, it's natural to assume that fluent style JavaScript will and should evolve beyond this definition, as new efficiencies are discovered and popularized.

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

## Object Creation

Objects in JavaScript are sometimes created with constructor functions. For example:


    function Car(color, direction, mph) {
      this.color = color || 'pink';
      this.direction = direction || 0; // 0 = Straight ahead
      this.mph = mph || 0;

      this.gas = function gas(amount) {
        amount = amount || 10;
        this.mph %2B= amount;
        return this;
      };

      this.brake = function brake(amount) {
        amount = amount || 10;
        this.mph = ((this.mph - amount) &lt; 0)? 0
          : this.mph - amount;
        return this;
      };
    }

    var myCar = new Car();

    test('Constructor', function () {
      ok(myCar.color, 'Has a color');

      equal(myCar.gas().mph, 10,
        '.accelerate() should add 10mph.'
      );

      equal(myCar.brake(5).mph, 5,
        '.brake(5) should subtract 5mph.'
      );
    });

You can get data encapsulation by using private variables:


    function Car(color, direction, mph) {
      var isParkingBrakeOn = false;
      this.color = color || 'pink';
      this.direction = direction || 0; // 0 = Straight ahead
      this.mph = mph || 0;

      this.gas = function gas(amount) {
        amount = amount || 10;
        this.mph %2B= amount;
        return this;
      };
      this.brake = function brake(amount) {
        amount = amount || 10;
        this.mph = ((this.mph - amount) &lt; 0)? 0
          : this.mph - amount;
        return this;
      };
      this.toggleParkingBrake = function toggleParkingBrake() {
        return !!isParkingBrakeOn;
      };
    }

    var myCar = new Car();

    test('Constructor with private property.', function () {
      ok(myCar.color, 'Has a color');
      equal(myCar.gas().mph, 10,
        '.accelerate() should add 10mph.'
      );
      equal(myCar.brake(5).mph, 5,
        '.brake(5) should subtract 5mph.'
      );
      ok(myCar.toggleParkingBrake,
        '.toggleParkingBrake works.'
      );
    });

You can shave a bit of syntax using the object literal form:


    var myCar = {
      color: 'pink',
      direction: 0,
      mph: 0,

      gas: function gas(amount) {
        amount = amount || 10;
        this.mph %2B= amount;
        return this;
      },

      brake: function brake(amount) {
        amount = amount || 10;
        this.mph = ((this.mph - amount) &lt; 0)? 0
          : this.mph - amount;
        return this;
      }
    };

    test('Object literal notation.', function () {
      ok(myCar.color, 'Has a color');

      equal(myCar.gas().mph, 10,
        '.accelerate() should add 10mph.'
      );

      equal(myCar.brake(5).mph, 5,
        '.brake(5) should subtract 5mph.'
      );
    });

Notice that because the object literal form doesn't use a function, we've lost the ability to do encapsulation.

## Factories

Object literals have great advantages, but they offer no way to create data privacy. Encapsulation is useful because it hides implementation details from the client. Remember the first principle of OO Design from the Gang of Four: "Program to an interface, not an implementation." Encapsulation allows you to enforce that principle in your code, hiding implementation details from the user.

But you know already that constructor functions come with some drawbacks that are best avoided. A better solution is to use a factory method.

A _factory_ is a method used to create other objects. Its purpose is to abstract the details of object creation from object use. In object oriented design, factories are commonly used where a simple class is not enough.

Returning to the singleton example; sometimes it is useful to abstract the singleton reference behind a method call. You can make the singleton instance a private variable, and return the reference from a closure:


    function factory() {
      var highlander = {
          name: 'MacLeod'
        };

      return {
        get: function get() {
          return highlander;
        }
      };
    }

    test('Singleton', function () {
      var singleton = factory();
        hero = singleton.get(),
        hero2 = singleton.get();

      hero.sword = 'Katana';

      // Since hero2.sword exists, you know it's the same
      // object.
      ok(hero2.sword, 'There can be only one.');
    });

You can use the same closure technique to add the parking break functionality to the car:


    var car = function car(color, direction, mph) {
      var isParkingBrakeOn = false;

      return {
        color: color || 'pink',
        direction: direction || 0,
        mph: mph || 0,

        gas: function gas(amount) {
          amount = amount || 10;
          this.mph %2B= amount;
          return this;
        },

        brake: function brake(amount) {
          amount = amount || 10;
          this.mph = ((this.mph - amount) &lt; 0)? 0
            : this.mph - amount;
          return this;
        },

        toggleParkingBrake: function toggleParkingBrake() {
          return isParkingBrakeOn = !isParkingBrakeOn;
        }
      };
    },

    myCar = car();

    test('Factory with private variable.', function () {
      ok(myCar.toggleParkingBrake,
        '.toggleParkingBrake works.'
      );
    });

As with the constructor function, you get data privacy by encapsulating private data inside the closure, so the only way to manipulate the state of the parking brake is through the privileged method, `toggleParkingBrake`.

Unlike the constructor function, you don't have to invoke a factory with the `new` keyword (or worry about forgetting `new`, or guarding against clobbering the global object inside the function).

Of course, you can also take advantage of the `prototype` property to make the whole thing more efficient:


    var carPrototype = {
        gas: function gas(amount) {
          amount = amount || 10;
          this.mph %2B= amount;
          return this;
        },
        brake: function brake(amount) {
          amount = amount || 10;
          this.mph = ((this.mph - amount) &lt; 0)? 0
            : this.mph - amount;
          return this;
        },
        color: 'pink',
        direction: 0,
        mph: 0
      },

      car = function car(options) {
        return extend(Object.create(carPrototype), options);
      },

      myCar = car({
        color: 'red'
      });

    test('Flyweight factory with cloning', function () {
      ok(myCar.__proto__.gas,
        'Prototype methods are shared.'
      );
    });

Notice that the factory method itself is now reduced to a one-liner. The arguments list is replaced with an options hash that allows you to specify exactly which values you want to override.

If you take advantage of the prototype feature, you can replace as much or as little of the prototype as you want at run-time. Using the same setup code above:


    test('Flyweight factory with cloning', function () {
      // Swap out some prototype defaults:
      extend(carPrototype, {
        name: 'Porche',
        color: 'black',
        mph: 220
      });

      equal(myCar.name, 'Porche',
        'Instance inherits the new name.'
      );

      equal(myCar.color, 'red',
        'No instance properties will be impacted.'
      );
    });

You should avoid sharing objects and arrays on the prototype if they need to be instance safe. Instead, you can create new copies of the child object / array in the factory.

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



   [1]: http://chimera.labs.oreilly.com/books/1234000000262
   [2]: http://chimera.labs.oreilly.com#
   [3]: http://chimera.labs.oreilly.com/ch03.html
   [4]: http://chimera.labs.oreilly.com/ch05.html
   [5]: http://chimera.labs.oreilly.com/index.html
   [6]: http://oreilly.com/terms/
   [7]: http://oreilly.com/oreilly/privacy.csp
   [8]: mailto:scordesse%40oreilly.com
  

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
