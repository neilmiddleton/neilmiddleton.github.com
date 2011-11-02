---
layout: post
title: Making Postgres STFU in Rails
---
# Making Postgres STFU in Rails

If you’re using [Heroku][] to host your Ruby Rack apps, then you’re
probably also using Postgres, which is the default choice for the
database stack.

However, one downside to this is that in development Postgres is a noisy
bastard telling you about everything that you never wanted to know, plus
the odd query of interest.

Well, luckily there is a couple of things you’re able to do to reduce
the noise. Firstly you can tell Postgres directly that you don’t want to
hear it’s warnings about table creation and the like:

{% highlight ruby %}
development:
  adapter: postgresql
  host: localhost
  min_messages: warning
  database: foo_development
{% endhighlight %}

If this isn’t enough for you - you can also tell Postgres to only give
you the queries that your app generates rather than everything that
postgres sends over the wire. This is all handled by the
[silent-postgres][] gem.

To add, simply whack this in your gemfile.

{% highlight ruby %}
gem "silent-postgres"
{% endhighlight %}

Bundle install, and you're good to go, and in peace.

[Heroku]: http://www.heroku.com/
[silent-postgres]: http://rubygems.org/gems/silent-postgres