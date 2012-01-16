---
layout: post
title: The Procfile is your friend
category: Heroku
---
#The Procfile is your friend

With the new Heroku Cedar stack there is a change in the way that the
Heroku platform views processes.  In the 'old' days of Bamboo and Aspen,
you had dynos (your web processes) and workers (your background job
processes).  Both of these limited you in a few ways as to what you
could run on the platform inside your application.

These days, the cedar stack doesn't see dyno's or workers, it just sees
processes which is more inline with the Unix design philosophy that the
entire stack has been designed against.

> Write programs that do one thing and do it well. Write programs to work together. Write programs to handle text streams, because that is a universal interface.

In order to declare these processes, and scale them individually, we
need to be able to tell Heroku what these processes are.

Enter the Procfile, stage left.

The Procfile is a simple [YAML](http://en.wikipedia.org/wiki/YAML)-esque* file which sits in the root of your
application code and is pushed to your application when you deploy.
This file contains a definition of every process you require in your
application, and how that process should be started.  An example of this
would be something like this:

{% highlight bash %}
web:  bundle exec rails server -p $PORT
worker: bundle exec rake jobs:work
{% endhighlight %}

This example is pretty typical of a Rails application.  It contains the
web process for the front end, and a worker for [delayed_job](https://github.com/collectiveidea/delayed_job), a
background job processor.  

Note the explicit mention of $PORT.  This is
required because the Heroku stack will explicitly define the port that
it wants your process to connect to, and publish this as an environment
variable.  Other environment variables are also available, so something
such as the following is also perfectly valid:

{% highlight bash %}
web:   bundle exec thin start -p $PORT -e $RACK_ENV
{% endhighlight %}

So, what about scaling?  Well, this is simple via the [Heroku command
line interface](http://devcenter.heroku.com/articles/scaling):

{% highlight bash %}
$ heroku ps:scale web=10
$ heroku ps:scale worker+1
{% endhighlight %}

and so on.

By declaring each process, and giving it a name, you're able to scale
them individually from each other and even turn them off altogether. You
may have traditional web processes, but you may also have workers, clock
processes or more.  Anything goes here, it's purely up to you.

##Development

In development though, your Procfile is not a wasted resource.  By using
the [foreman](https://github.com/ddollar/foreman) gem, you're able to utilise your Procfile and use it to spin
up your development environment in the exact same way.  Foreman is dead
simple to use too:

{% highlight bash %}
$ gem install foreman
$ foreman start

18:06:23 web.1     | started with pid 47219
18:06:23 worker.1  | started with pid 47220
18:06:25 worker.1  | (in /Users/foo/myapp)
18:06:27 web.1     | => Booting Mongrel
...
{% endhighlight %}

Now, an interesting thing to mention here is that Heroku only
auto-launches the web process in your Procfile when deploying, whereas
Foreman launches everything.  Therefore you could use a Procfile such as this:

{% highlight bash %}
web: bundle exec rails server -p $PORT
worker: bundle exec rake jobs:work
redis: redis-server
{% endhighlight %}

On Heroku, this will only auto-launch the Rails process, and require you to scale the worker processes.  However, with Foreman, it will also
launch Redis for you should your application require it.

##Apps without an user interface

Where it gets really interesting is when you consider that you might not
even want a web process at all.  For instance, you might need a Resque
worker running somewhere processing incoming jobs and spitting out
results to another output (file or API etc).  This is a valid example of
this:

{% highlight bash %}
worker: QUEUE=* bundle exec rake resque:work
{% endhighlight %}

as are these:

{% highlight bash %}
redis: echo "port $PORT" | bin/redis-server -
{% endhighlight %}

or

{% highlight bash %}
pg: bin/postmaster -p $PORT
{% endhighlight %}

So, as you can see, the Procfile is a very powerful thing, and something
that provides you with an immense amount of control over what your
applications are doing and how. It also gives you a massive amount of
flexibility with how you want to scale your application and processes.

Not something to be sniffed at.

\* If it had three dashes at the top it would be YAML
