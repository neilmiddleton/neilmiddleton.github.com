---
layout: post
title: Developing with an environment
category: Heroku
---
#Developing with an environment

One of the best practises for developing scalable applications these days is
that of seperating your configuration from your application.

To quote [12factor.net](http://www.12factor.net):
> The twelve-factor app stores config in environment variables (often shortened
> to env vars or env). Env vars are easy to change between deploys without
> changing any code; unlike config files, there is little chance of them being
> checked into the code repo accidentally; and unlike custom config files, or
> other config mechanisms such as Java System Properties, they are a language- and
> OS-agnostic standard.

This is all very well, but how can we carry this forward into development.
Typically (and I'll use Rails as an example here), when we start a Rails process
locally it'll use the environment on the machine in question.  However, this
isn't much fun if you're developing more than one application, or happen to have
environment variables in your app that conflict with those that you want to use
locally.

So, how do we tell our Rails process what environment variables we want to use?
Well, the simple way is to pass them into the command in the usual way:

{% highlight bash %}
$ FOO=BAR ONE=TWO rails server
{% endhighlight %}

Running this will start the Rails process with the environment variable `FOO`
set to `BAR`, and `ONE` set to `TWO`

However, this isn't overly helpful when you have a multitude of environment
variables, or need to start and stop processes a lot.  In these instances it
would be helpful to have a file containing all the variables we need and somehow
load this when we want to.

### Enter Foreman

Whilst [foreman](http://ddollar.github.com/foreman/) is very handy for running
lots of processes together from a `Procfile` (see
[here](/the-procfile-is-your-friend/) for more information on
that) it is also a useful tool for running processes within a given environment.

On startup of a process, foreman will look for a .env file in the root of your
project and parse that, loading the contents into the environment for the
process.  For instance, to duplicate the above example we could do something
like this:

*`.env`*
{% highlight bash %}
FOO=BAR
ONE=TWO
{% endhighlight %}

So, now we have a process with our desired environment, and what's more, as it's
in a file we can more or less forget about those variables until we need to
change them.

So this is great for the processes in our Procfile, but what if we want to spin
up something else not in that file on an adhoc basis, such as a `rails console`?
Well, luckily foreman lets you do that too, including the environment as before.

For instance,

{% highlight bash %}
$ foreman run rails console
{% endhighlight %}

With this, we now have a way of running anything we want within the context of
not only our application, but also our desired environment
