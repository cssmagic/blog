# [译] [PJA] [000] 序言

> * Original: [Preface](http://chimera.labs.oreilly.com/books/1234000000262/pr01.html)
> * Translated by: [cssmagic](https://github.com/cssmagic)

# Preface

## Conventions Used in This Book

The following typographical conventions are used in this book:

_Italic_

Indicates new terms, URLs, email addresses, filenames, and file extensions.

`Constant width`

Used for program listings, as well as within paragraphs to refer to program elements such as variable or function names, databases, data types, environment variables, statements, and keywords.

**`Constant width bold`**

Shows commands or other text that should be typed literally by the user.

_`Constant width italic`_

Shows text that should be replaced with user-supplied values or by values determined by context.

> `[$]` This icon signifies a tip, suggestion, or general note.

> `[!]` This icon indicates a warning or caution.

## Using Code Examples

This book is here to help you get your job done. In general, you may use the code in this book in your programs and documentation. You do not need to contact us for permission unless you’re reproducing a significant portion of the code. For example, writing a program that uses several chunks of code from this book does not require permission. Selling or distributing a CD-ROM of examples from O'Reilly books does require permission. Answering a question by citing this book and quoting example code does not require permission. Incorporating a significant amount of example code from this book into your product’s documentation does require permission.

We appreciate, but do not require, attribution. An attribution usually includes the title, author, publisher, and ISBN. For example: “_Programming JavaScript Applications_ by Eric Elliott (O'Reilly). Copyright 2013 Eric Elliott, 978-1-4493-2094-2.”

If you feel your use of code examples falls outside fair use or the permission given above, feel free to contact us at <permissions@oreilly.com>.

## Safari&reg; Books Online

> Safari Books Online is an on-demand digital library that lets you easily search over 7,500 technology and creative reference books and videos to find the answers you need quickly.

With a subscription, you can read any page and watch any video from our library online. Read books on your cell phone and mobile devices. Access new titles before they are available for print, and get exclusive access to manuscripts in development and post feedback for the authors. Copy and paste code samples, organize your favorites, download chapters, bookmark key sections, create notes, print out pages, and benefit from tons of other time-saving features.

O'Reilly Media has uploaded this book to the Safari Books Online service. To have full digital access to this book and others on similar topics from O'Reilly and other publishers, sign up for free at [http://my.safaribooksonline.com/][6].

## How to Contact Us

Please address comments and questions concerning this book to the publisher:

> O'Reilly Media, Inc.  
> 1005 Gravenstein Highway North  
> Sebastopol, CA 95472  
> 800-998-9938 (in the United States or Canada)  
> 707-829-0515 (international or local)  
> 707-829-0104 (fax)

We have a web page for this book, where we list errata, examples, and any additional information. You can access this page at:

<http://shop.oreilly.com/product/0636920024231.do>

To comment or ask technical questions about this book, send email to:

<bookquestions@oreilly.com>

For more information about our books, courses, conferences, and news, see our website at <http://www.oreilly.com/>.

Find us on Facebook: <http://facebook.com/oreilly>

Follow us on Twitter: <http://twitter.com/oreillymedia>

Watch us on YouTube: <http://www.youtube.com/oreillymedia>

## Introduction

There are many books on the web technologies covered in this book. However, there are precious few JavaScript books that can be recommended to somebody who wants to learn how to build a complete JavaScript application from the ground up. Meanwhile, almost every new tech startup needs knowledgeable JavaScript application developers on staff. This book exists for one purpose: To help you gain the knowledge you need to build complete JavaScript applications that are easy to extend and maintain.

This book is not intended to teach you the basics of JavaScript. Instead, it’s designed to build on your existing knowledge and discuss JavaScript features and techniques that will make your code easier to work with over time. Normally, as an application grows, it becomes increasingly difficult to add new features and fix bugs. Your code becomes too rigid and fragile, and even a small change could necessitate a lengthy refactor. If you follow the patterns outlined in this book, your code will remain flexible and resilient. Changes to one piece of code won't negatively impact another.

The book will focus primarily on client-side architecture, although it will cover server-side topics such as basic RESTful APIs and Node. The trend is that a great deal of the application logic is getting pushed to the client. It was once the case that the server environment would handle things like templating and communication with vendor services. Now it’s common to deal with both of those jobs inside the browser.

In fact, a modern JavaScript application does almost everything a traditional desktop app would do completely in the browser. Of course, servers are still handy. Server roles frequently include serving static content and dynamically loaded modules, data persistence, action logging, and interfacing with third party APIs.

We'll cover:

  * JavaScript features and best practices for application developers

  * Code organization, modularity, and reuse

  * Separation of concerns on the client side (MVC, etc...)

  * Communicating with servers and APIs

  * Designing and programming RESTful APIs with Node.js

  * Build, test, collaboration, deployment, and scaling

  * Expanding reach via platform targets and internationalization

## Who this Book is For

You have some experience with JavaScript; at least a year or two working frequently with the language, but you want to learn more about how you can apply it specifically to developing robust web scale or enterprise applications.

You know a thing or two about programming, but you have an insatiable thirst to learn more. In particular, you'd like to learn more about how to apply the powerful features that distinguish JavaScript from other languages, such as closures, functional programming, and prototypal inheritance (even if this is the first you've heard of them).

Perhaps you'd also like to learn about how to apply Test Driven Development (TDD) techniques to your next JavaScript challenge. This book uses tests throughout the code examples. By the time you reach the end, you should be in the habit of thinking about how you'll test the code you write.

## Who this Book is Not For

This book covers a lot of ground quickly. It was not written with the beginner in mind, but if you need clarification, you might find it in "JavaScript: The Good Parts" by Douglas Crockford, "JavaScript: The Definitive Guide", by David Flannagan, or for help with software design patterns, the famous Gang of Four book (GoF), "Design Patterns: Elements of Reusable Object-Oriented Software" by Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides.

Google and Wikipedia can be handy guides to help you through as well. Wikipedia is a fairly good reference for software design patterns.

If this is your first exposure to JavaScript, you would do well to study some introductory texts and tutorials before you attempt to tackle this book. My favorite is ["Eloquent JavaScript" by Marijn Haverbeke][8]. Be sure to follow that up with "JavaScript: The Good Parts" mentioned above, and pay special attention to the style guide in the beginning of this book so that you can learn from the mistakes of more experienced JavaScript developers.

## About the Author

Eric Elliott is a veteran of JavaScript application development. He is currently a member of the Creative Cloud team at Adobe. Previous roles include JavaScript Lead at Tout (social video), Senior JavaScript Rockstar at BandPage (an industry leading music app), head of client side architecture at Zumba Fitness (the leading global fitness brand), and several years as a UX and viral application consultant. He lives in the San Francisco bay area with the most beautiful woman in the world.

[6]: http://my.safaribooksonline.com/?portal=oreilly
[8]: http://eloquentjavascript.net/

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
