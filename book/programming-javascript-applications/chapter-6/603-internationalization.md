# [译] [PJA] [600] 国际化工程

> * Original: [Internationalization - Chapter 6. Separation of Concerns - Programming JavaScript Applications](http://chimera.labs.oreilly.com/books/1234000000262/ch06.html#internationalization)
> * Translated by: [cssmagic](https://github.com/cssmagic)

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

[46]: http://momentjs.com/
[47]: http://i18next.com/
[48]: https://github.com/jquery/globalize
[49]: http://cldr.unicode.org/
[50]: https://github.com/twitter/twitter-cldr-js

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/XXXXXXXXXX)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
