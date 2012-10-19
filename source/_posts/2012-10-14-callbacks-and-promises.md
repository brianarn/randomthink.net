---
layout: post
title: "Callbacks and Promises"
date: 2012-10-14 21:36
comments: true
categories:
- Technical
---

Anyone who's written even a little bit of JavaScript is likely familiar with the
callback pattern. Even if you don't know it by that name, you've probably
written code using it. When calling a function, we'll pass it another function
as an argument, which will be used to let us know that something has happened.
In particular, callbacks are frequently used for responding to things that
happen asynchronously.

For example, event handling is based on this pattern. We take a DOM node and
provide it with a callback function so that when the associated event occurs on
that node, it has a way of letting us know what's going on.

<!-- more -->

``` javascript Simple document-based click handler
document.addEventListener("click", function(e){
  console.log("Document was clicked!");
}, false);
```

This sort of code is very commonplace, and is one of the more powerful features
of JavaScript. When using code that accepts callbacks, we don't usually think
too much of it.

As we create richer applications, we'll often need to create functions that pass
information back to our users. If the information we're passing back comes from
asynchronous sources, it may be beneficial to create functions that that accept
callbacks. Because JavaScript functions are [first-class objects][1], you can
receive them as arguments, and then simply invoke them.

I prefer [Dojo][] these days, but for simplicity's sake, I'll show an example
using [jQuery][], as I suspect that will be more familiar to most people.

``` javascript Simple function that accepts a callback
function getManagers(callback){
  // Kick off our Ajax request for information.
  $.ajax({
    url: "/company/allEmployees.json",
    dataType: "json",
    success: function(data) {
      // Using our callback, provide manager data to our user.
      if (data.managers && typeof callback == "function") {
        callback(data.managers);
      }
    }
  });
}
```

Notice that in this case, I'm not returning anything from the `getManagers`
function. I'm communicating with my user by invoking the callback they've
provided to me. This approach is very effective, and people are generally
comfortable with using callback-driven functions, so it's a solid and perfectly
valid way to write code. It could also be extended to handle not just a callback
for success states, but another one for errors.

``` javascript Improved version with callback/errback support
function getManagers(callback, errback){
  // Kick off our Ajax request for information
  $.ajax({
    url: "/company/allEmployees.json",
    dataType: "json",
    success: function(data) {
      // We're breaking the logic to check just for managers first,
      // in order to better facilitate error handling.
      if (data.managers) {
        // We have our data, so invoke our callback.
        if (typeof callback == "function") {
          callback(data.managers);
        }
      } else if (typeof errback == "function") {
        // We have no manager data, so if we have an errback,
        // give it something meaningful to indicate failure.
        errback(new Error("No managers found"));
      }
    },
    error: function(jqXHR, textStatus, errorThrown) {
      // If we have the errback, just give it the error we got.
      if (typeof errback == "function") {
        errback(errorThrown);
      }
    }
  });
}
```

Using this function is actually quite easy:

``` javascript Using getManagers
// Simply call it and provide your callback.
// In this case, I'm not going to worry about errors.
getManagers(function(managers){
  for (var i = 0, l = managers.length; i < l; i++) {
    // Do something with each manager here
  }
});
```

Writing code that accepts callbacks and uses them is really simple. That being
said, callbacks can lead to tightly coupled code. It means that I have to know
how I want to process my information at the point of retrieving it. It makes it
more difficult to write reusable abstractions of the process used to request
data.

Fortunately, there is another approach available to us. Modern JavaScript
libraries and toolkits offer us implementations of a concept known as promises.
In general terms, a promise is simply an object that represents the value of an
asynchronous action. It's kind of like an IOU for information. It also happens
to fit the above scenario quite well.

If you're using Dojo or jQuery, you already have promises available to you. You
can create a Deferred object, which is the mechanism for creating and managing
promises.

Working with Deferreds is quite easy. You simply create an instance of the
Deferred constructor, and then when your value is ready, you `resolve` the
instance with the value that it represents. If you hit an error state, you can
`reject` the instance. Let's take the above example and rework it to be using
promises instead of callbacks.

``` javascript Getting managers via Deferreds / promises
function getManagers(){
  // Create our Deferred instance
  var deferred = new $.Deferred();

  // Then, we'll perform our Ajax request like before.
  $.ajax({
    url: "/company/allEmployees.json",
    dataType: "json",
    success: function(data) {
      // This time though, we'll simply resolve or reject
      // our Deferred instances.
      if (data.managers) {
        deferred.resolve(data.managers);
      } else {
        deferred.reject(new Error("No managers found"));
      }
    },
    error: function(jqXHR, textStatus, errorThrown) {
      deferred.reject(errorThrown);
    }
  });

  // Give our user the promise object that represents
  // our managers.
  return deferred.promise();
}
```

One key difference above is that we're actively providing a return value now,
whereas in the callback-based version, we didn't. This object that we're
returning is the promise that we're giving to our user, and it provides them
with the ability to get the information we're promising, once that data is
available.

The Deferred is the mechanism by which I manage the promise. It gives me the
ability to create a promise that I provide to my end user, as well as the
ability to then fulfill that promise in a meaningful way.

This approach almost feels synchronous. I create an object at the top of my
function, and I return it at the bottom. The user doesn't give me a callback,
but I give the user a meaningful object.

The promise object gives your user a `then` method, which they can then use to
attach a callback and get access to the value, like so:

``` javascript Using the return value from getManagers as a promise
getManagers().then(function(managers){
  for (var i = 0, l = managers.length; i < l; i++) {
    // Do something with each manager here,
    // such as render a list item.
  }
});
```

There is still a callback in action here, but now it's being managed in the code
that is consuming the information, rather than in the code that is providing it.
We've effectively decoupled the process of retrieving information from the
process of consuming it, which is a huge win for creating reusable code.

There's also something to be said about reading promise-based code. It feels
very natural. I take this action, `then` I take this other action. It can reduce
excessive amounts of indentation, especially when doing a lot of Ajax work at
once.

In fact, this approach is so powerful that both jQuery and Dojo already provide
a promise as the return value of their Ajax systems. Dojo 1.8's new
`dojo/request` module is explicitly promise-driven, so requests look like this:

``` javascript Using dojo/request to perform a request
require(["dojo/request"], function(request){
  request.get("/company/allEmployees.json", {
    handleAs: "json"
  }).then(function(data){
    // Work with your data here
  });
});
```

When creating your own modules of code to abstract away the process of data
retrieval from data consumption, both callbacks and promises are valid --
there's not one right way to do it. However, using a promise means that you're
giving your users back a meaningful object, and how they deal with the
information is up to them. It leads to loosely coupled code, which in turn leads
to better reuse and modularization.

Hopefully you'll consider using promises in your next application! I've barely
scratched the surface of what makes them awesome. Check out the links below for
much more information on promises and Deferreds. Thanks for reading!

* Dojo:
  * [Getting Started with Deferreds][3]
  * [Dojo Deferreds and Promises][4]
* jQuery:
  * [Deferred Object][5]
* [Promises/A specification][2]

[1]: http://en.wikipedia.org/wiki/First-class_function#Higher-order_functions:_passing_functions_as_arguments
[2]: http://wiki.commonjs.org/wiki/Promises/A
[3]: http://dojotoolkit.org/documentation/tutorials/1.8/deferreds/
[4]: http://dojotoolkit.org/documentation/tutorials/1.8/promises/
[5]: http://api.jquery.com/category/deferred-object/
[Dojo]: http://dojotoolkit.org/
[jQuery]: http://jquery.com/
