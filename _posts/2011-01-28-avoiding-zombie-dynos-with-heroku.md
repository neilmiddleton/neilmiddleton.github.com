---
layout: post
title: Avoiding Zombie Dynos With Heroku
category: Heroku
---
#Avoiding Zombie Dynos With Heroku
I’ve been looking at some long running processes on Heroku of recent and
found there is a behaviour in place which is not quite what I expected.

The heroku docs state:

> *The [Heroku][] routing mesh will also detect a different kind of
> performance problem: long-running requests. If your dyno takes more
> than 30 seconds to respond to a request, the mesh will serve a
> “Request Timeout” page to the user.*

Now, initially, you’d think that this means that your dyno process is
killed off after 30 seconds. What is actually happening, is literally as
the docs say, the routing mesh is returning a page timeout to the user.
The dyno is actually still spinning away, working it’s merry little
heart out even though nobody’s listening any more. The primary problem
with this is that dyno is still busy and can’t accept any more requests
until it’s finished - which leads you to having a zombie’d dyno.

So, what can we do? Well, the support guys at Heroku pointed me at
[rack-timeout][], a gem which can be used to timeout all your
application requests within a set time limit (for instance, the 30
seconds that Heroku set). Gem is a dead simple install and
configuration, and solves this problem neatly and elegantly.


  [Heroku]: http://heroku.com/
  [rack-timeout]: https://github.com/kch/rack-timeout
