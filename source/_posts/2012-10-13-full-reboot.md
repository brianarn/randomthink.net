---
layout: post
title: "Full Reboot"
date: 2012-10-13 22:18
comments: true
categories: 
- Personal
- Technical
---

So, I said that I was going to be using [WordPress][] and not using
[OctoPress][] but after I got things all set up back in WP, I found the
experience of writing to be kind of frustrating. I'd written some post drafts in
Markdown, and have really been enjoying that format. I also don't like not
having version control in play. I was able to use Markdown for my WP posts, but
having to edit in Vim and then copy/paste over and preview, it got kind of
annoying.

While OctoPress is definitely a bit more manual than something like WP, it turns
out that I'm okay with that. I've taken a little time in the evenings after my
brain is burnt out on other things and used it to poke and prod OctoPress into
doing what I want. This new site is the end result.

<!-- more -->

Additionally, I figured out how to adjust my nginx settings such that any
missing files are now going to redirect to a mirror of my old site, so I'm
basically making a clean break. For the first time in over a decade, the folder
that houses RandomThink.net is basically clean. Feels good, if a little scary
for some reason.

To provide a bit more detail, I'm using the 2.1 branch of Octopress. After some
discussions on Twitter, it sounds like 2.1 was basically good to go except for
migrations from 2.0. Since I'm not reusing the brianarn@github experiment, no
issues there.

I've found that OctoPress 2.1 offered some nice things, like the "Respectfully
Social" setting, wherein the Tweet / FB / G+ links at the bottom of each post
are really just basic links and not the full awful weight of their various APIs.
It also felt way easier to specify a few things, such as having a landing page
that isn't just the blog posts.

It was also fairly easy to set up where my feeds
and all should exist, and as such, the main links to individual blog posts, the
main blog page, and the RSS should all _just work_. I think.

While I'm still very obviously using the OctoPress theme, I've made some
customizations. I've taken the gradient out of the link bar above, and I really
like how clean that looks. I've shrunk the sidebar a bit. I've also completely
reset [my font choices][] (while still using the Google webfont service).

* Monospace: Source Code Pro. I've been using it in Vim and iTerm 2 for awhile,
  and I'm really quite a fan.
* Sans-serif: Varela Round. There was just something about it that I liked.
* Serif: Andada. Honestly, I picked this one because Google webfonts suggested
  it as a pairing with Varela Round. I tried some others, and this one was the
  best.

I ran the fonts by [Evan][] and he seemed to approve, and he's my font
authority, so I trust that it looks okay.

I've also brought my old [landing page][] over because I'm kind of attached to
that stupid mockup image now.

I'm now doing all my blogging through Vim and using git to version control it
all, and pushing [the full site source][] up to [GitHub][] as well. That means that all my everything for
the full site is going to be in source control. Feels good, man.

Now to finally finish that "Callbacks vs. Promises" post that I've been stewing
on for a month.

[WordPress]: http://www.wordpress.org/
[Octopress]: http://www.octopress.org/
[my font choices]: http://www.google.com/webfonts#UsePlace:use/Collection:Source+Code+Pro|Andada|Varela+Round
[Evan]: http://twitter.com/futuraprime
[landing page]: http://www.randomthink.net/
[the full site source]: https://github.com/brianarn/randomthink.net
[GitHub]: http://github.com/
