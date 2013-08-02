# [译] [PJA] [401] 传统的继承已经过时了

> * Original: [Classical Inheritance is Obsolete - Chapter 4. Objects - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch04.html#chcss2i1z00025eilzn6ki8ds)
> * Translated by: [cssmagic](https://github.com/cssmagic)

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

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
