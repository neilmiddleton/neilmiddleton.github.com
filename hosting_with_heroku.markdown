---
layout: page
title: Hosting with Heroku
---

#Hosting with Heroku


[Heroku][heroku] is a hosting platform based in the United States which I now use as the default choice for hosting all of my Rails and Rack applications wherever possible.

This document is designed to help you get the most from Heroku as a hosting platform.  Due to the particulary good [Heroku documentation][heroku_docs] this is NOT designed to be a starters guide to Heroku, the Heroku documentation does much better than that.  This is designed for people who are already happy using Heroku and understand the basics and want to understand more or learn how to get more from the platform.

This document only deals with the [Celadon Cedar][cedar] stack, and primarily Rails and Rack, although a lot of it may well apply to the other technologies hosted by Heroku.

Note, that most of this information is taken either from the [Heroku documentation][heroku_docs] or through trial and error from the platform.  Therefore, please be careful when using advice given here, and always ensure you test first.

##Understanding the stack

The Heroku platform consists of a number of varied elements, all responsible for individual things, a diagram of which can be seen [here](http://www.heroku.com/how/connect).

At the front of the queue is the routing mesh.  This is responsible for a couple of things.  Primarily it is responsible for taking a users request, and then passing that request onto another process (a dyno) that can handle it.  Once a response is received it will return the request back to the user (the time for this is seen in the logs as the `service` time).  The routing mesh also handles a couple of other tasks.  Firstly, it queues requests up for the dynos when they're busy (see `queue` in the logs), and protects the user from hung dynos by ensuring that requests complete within 30 seconds.  It a dyno doesn't respond within that 30 second window, the routing mesh will return an error.

After the request has been routed the dynos kick in.  Dynos, which are essentially web processes in the dyno manifold, handle all the web requests that your application may receive.  These are directly similar to other concepts such as Passenger or Mongrels, but fully automated.  Dynos are moving around all the time, being idled and restarted, and you never seen any of it.  This is one of the primary reasons Heroku has it's read-only filesystem.  Aside from web requests the dyno manifold also runs your applications background processing, such as delayed_job workers, resque queues or scheduled tasks.

Below the dynos is the logplex.  This is where all the stdout output from the dyno manifold arrives and is viewable with `heroku logs`.

Outside of this platform are all the ancillaries that you can use.  For instance, Heroku run a series of shared database servers for your application to use, plus all the add-ons that you can use from various other providers.

##Performance & Scalability

Performance and Heroku are one of the most commonly misunderstood areas of the platform and therefore one of the ones that is mis-managed more than others.

For starters, let's clarify the terms:

**Performance** is a measure of how fast you can serve a single request to a single user.

**Scalability** is a measure of how many users you can serve satisfactorily with your given infrastructure.

For starters, let's expel most of the myths in one go:

**The performance of your codebase is fixed.**

What does this mean?  Well, assuming a constant infrastructure, environment and codebase, a request using Heroku will always take the same amount of processing time. 

It is a common misconception that adding dynos to your application will cause it to increase in performance and serve users quicker.  This is wrong.

For instance, take a single dyno (the unit of work on Heroku).  A dyno is essentially a single threaded process handler.  It takes a request, serves the request, and then takes another from the queue.  A dyno only does one thing at a time.  

Now, given that your dynos will run the code as fast as possible (given the hardware at Heroku), increasing dynos will only serve to increase the number of requests that you can serve in any given amount of time.  If you're looking for increased performance you need to take a closer look at your code.  For instance, is your code itself as efficient as possible?  Are you using more external dependencies than strictly necessary?  Are you making your dynos work harder than they need to with static asset serving for instance?  Do your dependencies have a high latency when requested from your application?

All of these points need addressing before performance of your application will change.

##Deployment

Coming soon...

##Configuration

Coming soon...

##Monitoring

Coming soon...

#Contributing

This document is licensed under Creative Commons.  Feel free to re-use non-commercially as long as you include the contributors list.  For commercial use, please contact me first.

If you spot any inaccuracies, or feel that something should be added/edited, please feel free to [fork the content on Github][] and send me a pull request.  I will then re-publish here on a needs basis.  I will be happy to add your name to the contributors list.


--- 
![](http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png "Creative Commons License")

This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-nc-sa/3.0/)


  [heroku]: http://www.heroku.com
  [heroku_docs]: http://devcenter.heroku.com/
  [cedar]: http://devcenter.heroku.com/articles/cedar
  [fork the content on Github]: https://github.com/neilmiddleton/neilmiddleton.github.com