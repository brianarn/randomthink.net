---
layout: post
title: "Git Flow Solo"
date: 2012-10-02
comments: true
categories: 
- Technical
---
The other day on Twitter, I was discussing workflow with my friend [Geoff][],
and I mentioned that I use [Git Flow][] for some of the work that I do. He
mentioned that he's a solo developer on most of his projects, and so he's more
of a [GitHub Flow][] kind of guy. I've read Scott Chacon's post on GitHub Flow
in the past, and it's a nice and light system. However, I find that Git Flow
gives me some great power, and it seems worth discussing how it helps me as a
solo developer.

<!-- more -->

I work for [SitePen][] by day, but in the evenings and on weekends I have a
side project for [UNM's College of Education][COE]. While I do have a project
lead who does most of the styling work and sets direction for the project, I'm
doing the full stack of development. It's a pretty straight-forward LAMP stack
application that, over the past four years, has moved from being a basic
forms-driven site to a very rich and powerful application. It's built up using
[jQuery][] and a couple of plugins for things like validation and grids and
whatnot, and while I prefer [Dojo][] these days, it's not an option here due
to legacy. Anyhow, I manage this application's source with [Git Flow][].

Our source code (which is not public, unfortunately) has several branches
influenced by the design of Git Flow:

* `master`: This branch is production-ready. Nothing goes on `master` without
acceptance from the project lead.
* `develop`: This branch houses code that has been accepted, but not deployed
into production just yet.
* `feature/*`: There are typically one or more branches that start with
`feature/`, and these are where I am actively doing 80% of my work.
* `hotfix/`: There is often one or two of these showing up, and it accounts
for the other 20% of work performed.
* `qa`: This is a non-Git-Flow branch that I use to track the current state of
the testing server.

We've used a variety of project management tools in the past (currently using
[Trello][]) and at any one time, I typically have two to three feature
requests on my plate, and these features take anywhere from one week to one
month to implement. The workflow typically goes like so:

* Create a branch off of `develop`: `git flow feature start some-feature`
* Cook until golden brown and flaky, committing changes both small and large
as I go
* Once ready for QA, merge into the `qa` branch and deploy to the dev server
* Once accepted, push changes back into `develop`: `git flow feature finish
some-feature`

Aside: I'm able to use `git flow` as a command thanks to the wonderful
[git-flow][] project, which I can install with [Homebrew][] very trivially:
`brew install git-flow` -- Git Flow is a nice process on its own, but this
addition to command-line git makes it much, much easier to follow. There's
even bash completion if you're into that sort of things.

Completed features don't always necessarily get pushed into production, so
sometimes they'll queue up a little on `develop`. Once we're ready to do a
deploy of a new feature, it's as simple as `git flow release start YYYYMMDD`
(we don't have discrete version numbers so I've taken to using year, month,
day as an easy means of tracking release). At that point, I can open up my SQL
folder where I have any necessary migrations to apply to production, move the
applied productions into a `deployed` folder, and complete the release, which
merges into `master`. From there it's a simple push to production.

While this is all well and good, and our development cycles are relatively
fast, it's an academic environment. For those who haven't experienced
development in such environments, it's not uncommon for there to be fires.
Sometimes it turns out that the feature in development needs to wait because
we need a new report or some element in the user interface is confusing to the
new person and we need to address it right now or numbers aren't adding up
somewhere and OMG something is wrong FIX IT NOW. This is where Git Flow's
hotfix branches are wonderful. Anything that needs a quick turnaround winds up
going in as a hotfix. That workflow is quite similar to feature development,
although the branching is a little different.

* Create a branch off of `master`: `git flow hotfix start report-fix`
* Make adjustments as quickly as I can, introducing no new bugs of course
* Merge into `qa` and deploy to dev server, and notify my project lead
immediately so he can test things
* Once accepted, push back into `master`: `git flow hotfix finish report-fix`

Features for development, hotfixes for production. It's a really nice duality
that isn't readily visible when you review the Git Flow process the first few
times.

This means that I can very easily be working on multiple features at a time
(occasionally one gets blocked by need of clearer requirements or whatnot)
while also managing the fast and furious hot requests without totally losing
my mind of what branch is what and where did I create it and did I do it from
`master` or `develop` etc.

Git Flow does mention version numbers and whatnot, and if you have a product
with version numbers, I'm sure it's handy, but we're in a fairly perpetual
state of upgrade, so it's not so relevant. Using Git Flow gives me a very
manageable process for handling a lot of requests from a variety of sources.
Given that I'm a one-man shop there, it's nice to have a clean and easy-to-use
workflow like this. It's really not bad, especially if you install
[git-flow][].

If you're not doing _anything_ for branch management, try out one of the
flow-type processes I've linked. It's really been a significant change to how
I work, much for the better.

[Geoff]: http://geoffpetrie.com/
[Git Flow]: http://nvie.com/posts/a-successful-git-branching-model/
[GitHub Flow]: http://scottchacon.com/2011/08/31/github-flow.html
[SitePen]: http://www.sitepen.com/
[COE]: http://coe.unm.edu/
[jQuery]: http://jquery.com/
[Dojo]: http://www.dojotoolkit.org/
[Trello]: https://trello.com/
[git-flow]: https://github.com/nvie/gitflow
[Homebrew]: http://mxcl.github.com/homebrew/
