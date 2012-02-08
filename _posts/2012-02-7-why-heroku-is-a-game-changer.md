---
layout: post
title: Why Heroku is a game changer
category: Heroku
---
#Why Heroku is a game changer

Today, I had what I intially thought to be a very simple task to carry
out.  The task was this:

* Take an existing written and tested codebase
* Deploy it to a cloud server somewhere to act as failover for a Heroku
  instance of the same codebase.

All in all I thought this would be pretty simple.  I've setup a
multitude of applications on production servers in the past, ranging
from ye olde cgi-bin on UNIX boxes, ranging through various guises of
FTP, up to the current automation levels of tools such as [Capistrano](https://github.com/capistrano/capistrano).

As such, I'm not new to it.  Whilst I may be a developer at heart, like
most I have an affinity with *nix and understand how it thinks.  I'm
therefore not completely alien to the whole process.

So, how hard can it be?

Well, at the end of the day I was still going, and still have some more
to complete tomorrow. I wrote up a todo list of all the things I had to
do before I started, and these included:

* Create a server somewhere
* Get access to the server using SSH
* Create firewall rules for the server
* Ensure that the server has Ruby installed and other ancillaries
* Write the capistrano recipe for the deployment itself
* Create an init script for the web process so that I could control it
  neatly
* Install and configure monit to make sure things didn't die quietly and
  without any help
* Setup logrotate so that the disk didn't fill up with crap loads of
  archived logfiles
* Setup syslog so that the logs were directed to our [Papertrail](https://papertrailapp.com/) account
* Deploy the code
* Test

There's a few other minor thing missing, but this list should look
pretty familiar to anyone who's setup a Unix web server.

So, anyway.  Due to some unforseen problems (a few compatability issues
and so on) the total time required to get the server built was ranging
on a few hours.  OK, I'm no sys admin, but like I've said above, I've
setup a few servers before and I'm a developer, not someone who worries
about the plumbing.

Now, here's the kicker.  All this time is billable, so this would have
cost someone a few hours of time, which can easily range into a few
hundred dollars without even thinking about it.

Great for the bottom line, not great for the client (or my own sanity).

Now, cast your mind back to the initial requirements.  This server build
was purely a redundant server in case the one that was on Heroku was
unavailable for some reason, be it maintenance, connectivity issues, or
whatever.

I also setup that application.

What's more I setup that application in around five minutes.  In fact I
can recall the entire process start to finish:

* `heroku create -s cedar`
* `git push heroku master`
* `heroku config:add ...a few config vars`
* `heroku addons:add redistogo:nano`

Job done.

The primary difference here is that this only took me a few minutes and
is completely repeatable without any complex config or trying to work
out the best approach.  More than that, now it's done, I can pretty much
forget about the details unless I need to make changes.

I'm a developer you see, not a plumber.

Now, having done both, you'll probably get to the same realisation that
I had a couple of years ago.  Hosting with a platform such as Heroku
isn't about the cost savings over some other host.  It's not about the
addon library, it's not about the massively scalable architecture (which
are all obviously nice to have) - it's about the fact that I, as a
developer, with little in the way of real sysadmin knowledge can get
applications up and running with little or no fuss and get back to doing
what I do best as soon as possible.

What's more I can do it in a way that is better than if I had done it
myself.  My application is [scalable](http://www.12factor.net/concurrency), it's processes are [isolated](http://www.12factor.net/processes) and
[configurable](http://www.12factor.net/config) via an environment, and it's external resources are merely
[attached](http://www.12factor.net/backing-services) rather than baked in hard.

Another benefit of this approach is that I don't really need to know the
details.  I don't need to learn how to connect my thin process to the
outside world.  I don't even need to know how to install Ruby, I just
push my code to Heroku and it's golden.  

This really makes a difference when I'm deploying something that I might 
not be that familiar with. For instance, I might be deploying something 
a little different such as [Play!](http://www.playframework.org/).  Luckily as long as Heroku
knows the difference and how to run it I don't need to know anymore.
Now that Heroku buildpacks are on the scene this gets even better.  It
only needs one person to create a buildpack and you've got another
runtime that can be deployed to Heroku - no need to wait for official
support from Heroku.

So therefore, if you are running your own servers, or have looked at
Heroku and are put off by the cost, take a serious look at your own
costs and headaches that you have.  Factor in your time setting up
servers and troubleshooting.  Factor in those beeper calls at weekends
when something in your network has gone pop.  Factor in the necessary
updates and patches you need to carry out on a regular basis.

Then compare them again.
