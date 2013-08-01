# [译] [PJA] [201] 测试示例

> * Original: [Example Tests](http://chimera.labs.oreilly.com/books/1234000000262/ch02.html#example_tests)
> * Translated by: [cssmagic](https://github.com/cssmagic)

## Example Tests

This book is about developing real world applications, and in the real world, applications are fragile. When you change them, things often break, and you need an easy way to detect those broken things so you can fix them before your users are impacted.

Manual quality assurance is an expensive, time consuming, and tedious process, and the larger your code base grows, the bigger the job gets. One commonly accepted solution to this problem is to write tests that assure your code does what you expect.

Tests can aid in debugging as well. Frequently when a large number of components interact (often indirectly), it's difficult to tell which component a bug is hiding in. If you have a test suite, and one component breaks, you'll be alerted immediately which component isn't behaving the way it was designed to behave.

Throughout this book you'll see [QUnit][5] tests in examples to alert you to expected and actual behaviors. QUnit is a JavaScript unit test framework that generates clear, readable test output on whatever page you include it in. It's the test suite used by [jQuery][6].

It is safe to assume that all tests pass, unless you see a comment that says otherwise. For example:

    var highPass = function highPass(number, cutoff) {
        if (number &gt;= cutoff) {
          return true;
        } else {
          return false;
        }
      },

      lowPass = function lowPass(number, cutoff) {
        if (number &gt;= cutoff) {
          return true;
        } else {
          return false;
        }
      };

    module('Filter Examples');

    test('highPass', function () {
      ok(!highPass(2, 5), 'Lower values should not pass.');
      ok(highPass(8, 5), 'Higher values should pass.');
    });

    test('lowPass', function () {
      ok(lowPass(2, 5), 'Lower values should pass.'); // Fails
      ok(!lowPass(8, 5),
        'Higher values should not pass.'); // Fails
    });

The first test passes the tests as written. The second test set fails because the logic needs to be inverted in `lowPass()`.

### QUnit Primer

In this book, we'll be using several QUnit functions:

  * `module()`

  * `test()`

  * `ok()`

  * `equal()`

The `module()` function allows you to group related functionality in your test output. The `name` parameter can be any string. Usage:

`module(_`'Name'`_);`

The `test()` function lets you name and define individual module tests. All assertions go inside tests. Usage:

    test(_'Name'_, function () {
        _// Your assertions here..._
    });

You can test the truthiness of an expression with `ok():`

    ok(_true_, _'Description'_);

The first parameter can be any expression you wish to evaluate. If the expression is truthy, the assertion will pass. You can do anything you want with the Description, but I like to write them like: _`Subject`_ should _`verb`_. For e.g., "User should exist.", "Valid input should pass", etc... If you find yourself writing "_`Subject`_ should _be_...", consider using `equal()`, instead.

[5]: http://docs.jquery.com/QUnit
[6]: http://jquery.com/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
