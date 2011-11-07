---
layout: post
title: Getting more from your Heroku dynos
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
worker_processes 4
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
{% endhighlight %}

Commit, deploy, push, and you should start seeing Unicorn running in your logs, and a higher throughput as a result.

[Heroku]: http://www.heroku.com
[Unicorn]: http://unicorn.bogomips.org/