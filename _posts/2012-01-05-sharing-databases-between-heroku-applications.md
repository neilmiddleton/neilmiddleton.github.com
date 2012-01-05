---
layout: post
title: Sharing databases between Heroku applications
category: Heroku
---
#Sharing databases between Heroku applications

When you deploy an application to Heroku, you'll probably know that all
applications receive a free 5mb database on their shared PostgreSQL
database service.  Whilst these are on shared servers at Heroku, the
performance is still pretty good, and the cost of a size upgrade is
cheap too ($15/mo for 20Gb).

However, sometimes we want our applications to share our data, and by
default this isn't immediately obvious as to how this works.

Well, it's possible, but let's look into the background first.

Every Heroku application is able to have the `shared-database:5mb`
add-on, and some deployments such as Rails, get it by default.  Once
this add-on is added to an application two things happen.  Firstly,
Heroku creates the database for you, and secondly, each time you deploy
your application Heroku will overwrite your database configuration where
possible (so for instance, with Rails, it will overwrite
`config/database.yml' with their own version).

Essentially what this does it connects your application to an
environment variable driven connection.  You can see this by checking
your configuration:

{% highlight bash %}
heroku config
{% endhighlight %}

This will yield a whole load of settings, but in amongst them you'll
see something like:

{% highlight bash %}
DATABASE_URL => postgres://sakldjsad:kajshdsa@ec2-107-20-155-141.compute-1.amazonaws.com/sakldjsad
{% endhighlight %}

This is the database connection string that your application will be
using.

So, how do we connect two applications together (or even replace the
Heroku database with some other connection?)

Well, there's two ways.  Firstly, you could simply overwrite the
DATABASE_URL config variable:

{% highlight bash %}
heroku config:add DATABASE_URL=your_new_connection_string
{% endhighlight %}

an alternative is to use `pg:promote` which essentially does the same
thing:

{% highlight bash %}
heroku pg:promote your_new_connection_string
{% endhighlight %}

By using this technique you can string your applications together in
loads of different ways, sharing free databases, or even connecting lots
of applications in a single [Heroku Postgres](http://postgres.heroku.com/) instance.

For those Rails developers interested in how Heroku overwrites your
`config/database.yml`, you can see the code below:

{% highlight ruby %}
<%

require 'cgi'
require 'uri'

begin
  uri = URI.parse(ENV["DATABASE_URL"])
rescue URI::InvalidURIError
  raise "Invalid DATABASE_URL"
end

raise "No RACK_ENV or RAILS_ENV found" unless ENV["RAILS_ENV"] || ENV["RACK_ENV"]

def attribute(name, value)
  value ? "#{name}: #{value}" : ""
end

adapter = uri.scheme
adapter = "postgresql" if adapter == "postgres"

database = (uri.path || "").split("/")[1]

username = uri.user
password = uri.password

host = uri.host
port = uri.port

params = CGI.parse(uri.query || "")

%>

<%= ENV["RAILS_ENV"] || ENV["RACK_ENV"] %>:
  <%= attribute "adapter",  adapter %>
  <%= attribute "database", database %>
  <%= attribute "username", username %>
  <%= attribute "password", password %>
  <%= attribute "host",     host %>
  <%= attribute "port",     port %>

<% params.each do |key, value| %>
  <%= key %>: <%= value.first %>
<% end %>
{% endhighlight %}
