---
layout: post
title: "Breakpoint Actions in JavaScript"
date: 2012-11-28 22:48
comments: true
categories:
- Technical
---

Earlier today, I filled out the [Chrome DevTools Survey & Feedback][survey]
(which you should too, if you're a web developer), and near the bottom of the
survey, there was a question:

> Are there any tools or features you think are missing that would better help
> you develop your webapps?

My initial answer was No, there wasn't anything I felt was missing. DevTools are
awesome, and there hasn't really been anything I've felt like I needed.

Later in the day, I ran into a very common scenario: I was debugging something
where I wanted to observe a value at a particular line of code. There are
a couple of ways to approach it:

* Drop in a `debugger` statement or set a breakpoint to stop execution and use
the console and/or watch statements to observe the value
* Inject some `console.log` statements to write the value out to the console

In this case, the `console.log` approach would be ideal. However, I didn't have
access to modify the code directly, and while I could have used the live
editing features, it was also minified code, meaning that live edit was an ugly
option.

Thinking back to the survey question, I now had an answer: Wouldn't it be nice
if there was some way to have some sort of action kicked off, breakpoint-style,
where I'm not editing the code directly?

I asked some friends, and it turns out, other development environments have a
similar concept. Xcode has [Breakpoint Actions][] (as do some other IDEs) which
look to be exactly what I wanted. While bouncing this idea around, I realized
that I could actually fake it in the browser, right now.

Chrome supports conditional breakpoints. Adding them is easy. First, find the
line you want to set a breakpoint on, and click the line number to apply a
breakpoint like normal:

{% img /images/blog/2012-11-28-breakpoint-actions/breakpoints-1.png [Set a breakpoint] %}

Then you right-click on it, and select "Edit Breakpoint":

{% img /images/blog/2012-11-28-breakpoint-actions/breakpoints-2.png [Edit Breakpoint] %}

You're presented with your cursor in a field like so, where you can define an
expression. If the expression evaluates to a truthy value, execution will break
here.

{% img /images/blog/2012-11-28-breakpoint-actions/breakpoints-3.png [Set Condition] %}

It's not uncommon to set simple expressions like `someObj.id === '1'`, but I
realized that _any_ valid JS expression would be evaluated any time this line
was hit -- including the `console.log` statement I wanted to add!

{% img /images/blog/2012-11-28-breakpoint-actions/breakpoints-4.png [console.log dirtiness] %}

This approach works quite well. Looking over at the console after moving my
mouse around a bit on this page (which happens to be the [Chrome DevTools
Breakpoints page][breakpoints]), you can clearly see the added
`console.log` firing:

{% img /images/blog/2012-11-28-breakpoint-actions/breakpoints-5.png [console.log is working!] %}

Since `console.log` doesn't have a meaningful return value, the `undefined`
means the conditional breakpoint won't pause execution, and the code moves on.
It's just like a coded `console.log` statement, without having to actually
modify the code.

Using a conditional breakpoint like this feels horribly hacky, and yet, I love
this idea. It achieves the exact goal that I wanted. It's not elegant, it's not
by design, but it gets the job done, like so many other things in web
development.

As an added note, Firebug supports conditional breakpoints in much the same
way, and this approach totally works there as well. In both cases, it's working
simply because you're just evaluating the expression, and only pausing
execution if it evaluates to a truthy value. 

Hopefully you'll find this useful at some point! I already have.

[survey]: https://docs.google.com/forms/d/1hhZwGQmtNeiRhPwxLurPaVElB0h_E6h_qjnzpxAXdtI/viewform
[Breakpoint Actions]: http://useyourloaf.com/blog/2011/02/21/xcode-breakpoint-actions.html
[breakpoints]: https://developers.google.com/chrome-developer-tools/docs/scripts-breakpoints
