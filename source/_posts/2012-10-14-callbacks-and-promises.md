---
layout: post
title: "Callbacks and Promises"
date: 2012-10-14 21:36
comments: true
categories: 
- Technical
---

Anyone who's written even a little bit of JavaScript is likely familiar with the
callback pattern. Even if you don't know the name, you've probably used it. We
will often pass around functions to other functions as a way for the other
function to let us know when something has happened that we care about -- to
give us a call back, as it were. In particular, callbacks are a fantastic
solution to responding to things that happen asynchrously.

For example, events are based completely on this pattern. We take a DOM node,
and provide it with a callback function to indicate that we want to be alerted
to our user interacting with it in some way.

<!-- more -->

``` javascript Simple document-based click handler
document.addEventListener("click", function(e){
  console.log("Document was clicked!");
}, false);
```

This sort of code is very commonplace, and is one of the more powerful features
of JavaScript. When using code that accepts callbacks, we don't usually think
too much of it.

However, more advanced applications may require us to start building our own
APIs. In this scenario, it may be of benefit to us to create some code that is
on the receiving end of a callback. It's quite easy to create a function that
accepts callbacks. Because JavaScript functions are [first-class objects][1],
you can receive them as arguments, and then simply invoke them.

I prefer [Dojo][] these days, but for simplicity's sake, I'll show an example
using [jQuery][], as I suspect that's what more people will be familiar with.

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

Notice that in this case, I'm not returning anything from the function. I'm
communicating with my user via the callback instead. This approach is very
effective, and people are generally comfortable with it, so it's a solid and
perfectly valid way to write code. It could also easily be extended to handle
not just a callback for success states, but another one for errors.

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

That being said, callbacks can lead to tightly coupled code.  Occasionally,
that's fine; when performing event handling, it makes sense that I am coupling
an event to an action. For most asynchronous code, though, it means that I have
to know up front how I want to process my information at the point of retrieving
it. It makes it more difficult to write reusable abstractions of the process
used to request data.

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
function getManagers(callback, errback){
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
      } else if (typeof errback == "function") {
        deferred.reject(new Error("No managers found"));
      }
    },
    error: function(jqXHR, textStatus, errorThrown) {
      deferred.reject(errorThrown);
    }
  });

  // One _key_ difference: We're actively providing a return value,
  // which we didn't before. We don't want to return the Deferred
  // instance itself, as that's our control mechanism, so we'll
  // return the promise that we're making.
  return deferred.promise();
}
```

You'll note that the above function almost feels synchronous. I create an object
at the top, and I return it at the bottom. The user doesn't give me a callback,
but I give the user a meaningful object.

This object that I'm giving back to them is the promise I'm making for the
eventual result of that Ajax request. The Deferred is the mechanism by which I
manage the promise, including resolution and rejection, but they don't get the
whole thing -- just the promise.

The promise gives your user a `then` method, which they can then use to attach a
callback and get access to the value, like so:

``` javascript Using the return value from getManagers as a promise
getManagers().then(function(managers){
  for (var i = 0, l = managers.length; i < l; i++) {
    // Do something with each manager here,
    // such as render a list item.
  }
});
```

There is still a callback in action here, but now it's being managed more in the
code that is generating the request, rather than in the code that is providing
information. Additionally, promises allow you to attach more than one callback!

``` javascript Reusing the return from getManagers
// Fetch our managers and save the promise we get back.
var managersPromise = getManagers();

// Process the managers in some meaningful way
// when they're available to us.
managersPromise.then(function(managers){ /* ... */ });

// Then, in code later on...
managersPromise.then(function(managers){
  // Do something else completely separate with the manager information.
  // We don't actually do a server trip this time - we're re-using the
  // information that was already retrieved!
});
```

This ability to get at the information via multiple callbacks can be very
powerful. On top of that, the way that promises work means that I can get the
data even if the asynchronous action is already completed. The promise
represents the value before the request is complete, and continues to do so once
resolved.

There's also something to be said about reading promise-based code. It feels
very natural. I take this action, `then` I take this other action. It can reduce
excessive amounts of indentation, especially when doing a lot of Ajax work at
once.

In fact, this approach is so powerful that both jQuery and Dojo already provide
a promise as the return value of their Ajax systems. Dojo's new `dojo/request`
module is explicitly promise-driven, so requests look like this:

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

Hopefully you'll consider using promises in your next application! Please check
out the links below for much more information on promises and Deferreds.

* [Promises/A specification][2]
* Dojo:
  * [Getting Started with Deferreds][3]
  * [Dojo Deferreds and Promises][4]
* jQuery:
  * [Deferred Object][5]

[1]: http://en.wikipedia.org/wiki/First-class_function#Higher-order_functions:_passing_functions_as_arguments
[2]: http://wiki.commonjs.org/wiki/Promises/A
[3]: http://dojotoolkit.org/documentation/tutorials/1.8/deferreds/
[4]: http://dojotoolkit.org/documentation/tutorials/1.8/promises/
[5]: http://api.jquery.com/category/deferred-object/
[Dojo]: http://dojotoolkit.org/
[jQuery]: http://jquery.com/
