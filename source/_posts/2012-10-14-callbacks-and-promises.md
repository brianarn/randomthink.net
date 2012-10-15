---
layout: post
title: "Callbacks and Promises"
date: 2012-10-14 21:36
comments: true
categories: 
- Technical
---

Summary: Blog post draft detailing the distinctions between callbacks and
promises as a pattern

## Outline:

1. Introduce callbacks briefly
	1. Show jQuery event style
	2. Show jQuery / Dojo XHR
	3. Link to detailed write-up somewhere?  1.Note the value of callbacks is
	   for asynchronous code 1.Show example of writing a method to accept a
	   callback
	4. Simple function that accepts a success and error
2. Abstract example into simple module
3. Discuss coupled nature of this approach
4. Introduce promises / Deferreds
5. Rework module into promise based setup
6. No one right way, these are two patterns

## Draft:

Anyone who's written even a little bit of JavaScript is likely familiar with the
callback pattern. Even if you don't know the name, you've probably used it. We
will often pass around functions to other functions as a way for the other
function to let us know when something has happened that we care about -- to
give us a call back, as it were. In particular, callbacks are a fantastic
solution to responding to things that happen asynchrously.

For example, events are based completely on this pattern. We take a DOM node,
and provide it with a callback function to indicate that we want to be alerted
to our user interacting with it in some way. 

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
you can receive them as arguments, and then simply invoke them, like so:

``` javascript Simple function that accepts callbacks
function doSomethingAsync(callback, errback){
	// We'll use this variable as the end result of this function
	var result;

	try {
		// Perform some asynchronous action that may
		// potentially result in an error, like an Ajax request.
		// Eventually, assign a value to `result` here.
	} catch (err) {
		// Something went wrong, so if we have the errback,
		// hand out error back to the user.
		if (typeof errback == 'function') {
			errback(err);
		}
	}

	// If I've hit this point in the code, it was a success,
	// so hand back my result via callback.
	if (typeof callback == 'function') {
		callback(result);
	}
}
```

Notice that in this case, I'm not returning anything from the function. I'm
communicating with user via the callback, rather than through returning
something to them. This approach is very effective, and people are generally
comfortable with it, so it's a solid and perfectly valid way to write code.

That being said, this approach can sometimes feel tightly coupled.
Sometimes this is fine; when performing event handling, it makes sense that I am
coupling an event to an action. However, in some code, particularly Ajax
requests, it means that I have to know up front how I want to process my
information at the point of retrieving it. For small things, that may be okay,
but it makes it a bit more difficult to write reusable abstractions of the
process used to request data.

Additionally, asynchronous coding can be confusing at first because people are
generally used to a more synchronous model, wherein one calls a function, and
they get back some meaningful value from it that can be used immediately.

Fortunately, there is another way these days of dealing with this scenario. If
you're using writing non-trivial JS, you're probably using some sort of library
or toolkit, and that means you likely have access to an implementation of
promises. In computer science terms, a promise is simply an object that
represents the value of an asynchronous action -  which happens to be the exact
scenario we're discussing here.

Most of the people I know and work with will be using either jQuery or Dojo,
	 both of which have implementations of promises. You can create a Deferred,
	 which is the mechanism by which you make a promise to someone.

Working with Deferreds is quite easy. You simply create an instance of one, and
then when your value is ready, you `resolve` the Deferred with the value that it
represents. If you hit an error state, you can `reject` the Deferred.

Rework above function to use Deferreds here

You'll note that the above function almost feels synchronous. I create an object
at the top, and I return it at the bottom. The user doesn't give me a callback,
   but I give the user a meaningful object.

This object that I'm giving back to them is the promise. The Deferred is the
mechanism by which I manage the promise, but they don't get the whole thing --
just the promise.

The promise gives them a `then` method, which they can then use to attach a
callback and get access to the value, like so:

Example of working with returned promise goes here

There is still a callback in action here, but now it's being managed more in the
code that is generating the request, rather than in the code that is providing
information. Additionally, promises allow you to attach more than one callback!

Show multiple callbacks via then here

This ability to get at the information via multiple callbacks can be very
powerful. On top of that, the way that promises work means that I can get the
data even if the async action is already completed. The promise represents the
value before the request is complete, and continues to do so once resolved.

For doing something like this without writing your own request logic,
	fortunately both Dojo and jQuery provide a sort of promise as the return
	value of their various Ajax methods! That means that if you needed to
	abstract away the process of making a request, it's easier than you might
	think. That means you can use this promise approach even if you haven't
	abstracted away your Ajax logic.

Example of then-based request here

When creating your own modules to abstract away the process of data retrieval
from data consumption, either of these approaches is valid. However, using a
promise means that you're giving your users back a meaningful object, and how
the deal with the information is up to them. It feels a bit more loosely coupled
as well, and leads to better reuse and modularization, which is always a nice
bonus. Hopefully you'll consider using promises in your next application.

[1]: http://en.wikipedia.org/wiki/First-class_function#Higher-order_functions:_passing_functions_as_arguments
