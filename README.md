hotswap-js
==========

Ideas about [hot swapping](http://en.wikipedia.org/wiki/Hot_swapping) javascript code.

Initial braindump

## References

http://rauchg.com/2014/7-principles-of-rich-web-applications/#push-code-updates
http://tomdale.net/2012/01/amd-is-not-the-answer/

## The main idea

The original idea was inspired by an [article](http://rauchg.com/2014/7-principles-of-rich-web-applications/#push-code-updates) by [rauchg](https://twitter.com/rauchg) that proposed the concept of pushing javascript updates to a user's browser.

Users have an increasing reliance on websites in their daily lives. It's common for a person to keep one or several sites open at all times (Facebook, gmail) without reloading them. Especially for single page sites, this means that changes to client side javascript may not reach users for days or even weeks. Gmail, [youtube](http://stackoverflow.com/questions/15824909/curious-about-the-new-way-youtube-is-loading-pages), and sites using frameworks like Backbone.js and Angularjs rarely perform real refreshes.

[rauchg](https://twitter.com/rauchg) proposes pushing updated javascript code to clients, rather than waiting for them to fetch it themselves.

There are a number of ways to implement server side push, including websockets (socket.io), [SSE](http://en.wikipedia.org/wiki/Server-sent_events), and ajax polling.

Google Reader used to display a small notification asking users to manually refresh to recieve the latest version of the app.

## Issues

There are a lot of issues.

### Modules

In order to actually swap javascript, it must be modular in some fashion. Code, and eventual objects in runtime, must be identifiable in some way before they can be "switched".

[AMD](http://requirejs.org/docs/whyamd.html) could be the answer, as it provides well defined entry points.

Angular's [dependancy injection](https://docs.angularjs.org/guide/di) is another pattern that could facilitate hot-swapping.

Any "live" item in scope can be switched, but only a few need to be. Creating some sort of well-defined protocol for the code in charge of switching is needed to reduce complexity. This could be as simple as a hash table (object) pointing to each "module". Each module could be as simple as a function.

### State

State will increase complexity. (I may be using state in the wrong way here.)

At any point in execution, more unique state in runtime (active variables, closures, generators, etc) could prevent hot swapping from happening. For example, if a new version of code factors out some variable or adds new ones, the values of the new are undefined and the value of the old is lost.

This increases the value of a modular coding style.

### Imperative vs Functional

Imperative programming style increases the amount of potential state in a program. Any section of code written with heavy use of imperativism **cannot** (maybe) be swapped out most of the time. At some point, perhaps when it reaches an equivalent state to the start of program execution (halting problem!) swapping code may be possible.

A purely functional programming style might be difficult because fewer handles are available for a swapper to hook code into.

Closures are important to a hot-swapping environment, but need to be careful. A heirarchy must be introduced to define which pieces can be swapped in. A closure can be swapped only if the new component doesn't depend on a change to outside scope.

### Examples

[Pre-compiled templates](http://stackoverflow.com/questions/13536262/what-is-javascript-template-precompiling) are an example of a type of code that could be much easier to swap. It may be essentially the way any code should be swapped. Since the resulting object has a single handle and is self contained, it's very simple to deal with. Just a pointer change.
