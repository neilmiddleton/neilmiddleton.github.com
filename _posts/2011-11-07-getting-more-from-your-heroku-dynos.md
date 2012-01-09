---
layout: post
title: Getting more from your Heroku dynos
category: Heroku
---

#Getting more from your Heroku dynos

If you're using [Heroku][] these days, you're probably having a good look at the new Celador Cedar stack that was introduced in Beta a few months ago.  This new stack gives you much more control over what processes are running and how they run.

Whilst this might not give you any immediate obvious benefits, this does give you the ability to change from the default choice of Thin as your Rack server, to something else such as Unicorn.

So, why should you bother?  Well, it's all about how the Dyno's on [Heroku][] work and how you pay for them.

In [Heroku][] a dyno is a single process that is self contained.  This dyno can do whatever you like, but has a soft limit of 512Mb of memory.  When using Thin, this dyno will hammer away quite happily serving one request at a time.

However, [Unicorn][] works slightly differently.  [Unicorn][] manages it's worker processes internally.  This means that you are able to have many Unicorn workers running within a single [Unicorn][] process, which is how the [Heroku][] dynos would see it.  Therefore, you could have four Unicorn processes running in one [Heroku][] dyno.

In testing on a current application that we're building we're seeing around double the throughput on the same number of dynos.  Note that this testing is only on a pre-production environment and not one with real users.  Once we have more users we might be able to give some more accurate figures.

So, how do we use this in a Rails app for instance?  Firstly, you need to add Unicorn to your Gemfile:

{% highlight ruby %}
gem 'unicorn'
{% endhighlight %}

You'll also need to create a unicorn config file in your app (for instance `config/unicorn.rb`):

{% highlight ruby %}
worker_processes 4
timeout 30
{% endhighlight %}

Note that you don't want to push the worker_processes too high or you'll start hitting the memory limits on the dyno.  Only by playing with the amount and testing will you find that happy place for your application.

Once you've done the above, you need to tell your Procfile how to spin up on Unicorn:

{% highlight ruby %}
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
{% endhighlight %}

Commit, deploy, push, and you should start seeing Unicorn running in your logs, and a higher throughput as a result.

##Testing

In order to test this I brought in the skills of Heroku and the
[Blitz](http://addons.heroku.com/blitz)
add-on, a simple load testing platform that allows you to do free tests
(as long as you don't want more than 250 concurrent users).

To start off with I created a [simple Rails 3.1.3 application](https://github.com/neilmiddleton/heroku_unicorn_test), which
does nothing more than contain a single action.  This action merely
sleeps for 100ms thus simulating a typical page execution time.  I then
deployed this [to Heroku](http://gentle-meadow-2939.herokuapp.com/base) using the defaults for everything.  Against this
I app I then ran a Blitz test of 15 users for 20 seconds.

Once the test was complete I then saw the following results:

![](/images/webrick_small.png)
[(Full size)](/images/webrick.png)

This shows that Heroku was able to serve 159 requests within a second,
and thus approximately 686,000 requests a day.

So what about Unicorn?  Well, to test this I simply carried about the
above instructions to change my application over to Unicorn, and then
re-ran the test suite as before:

![](/images/unicorn_small.png)
[(Full size)](/images/unicorn.png)

This resulted in an increase of 92 hits over the course of the test, or
an increase of approximately 60% - not too shabby you'll have to agree.

So, from this simple test, you can see that using the Rails defaults
you'll be serving 686K pages a day, whereas with Unicorn you can manage
just over a million, all for the same money.

[Heroku]: http://www.heroku.com
[Unicorn]: http://unicorn.bogomips.org/
