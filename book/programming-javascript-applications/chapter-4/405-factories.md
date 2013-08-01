# [译] [PJA] [405] 工厂函数

> * Original: [Factories](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html#factories)
> * Translated by: [cssmagic](https://github.com/cssmagic)

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

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
