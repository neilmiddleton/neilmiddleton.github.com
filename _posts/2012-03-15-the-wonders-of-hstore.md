---
layout: post
title: The wonders of hStore
category: Heroku
---
#The wonders of hStore

[As of yesterday](https://postgres.heroku.com/blog/past/2012/3/14/introducing_keyvalue_data_storage_in_heroku_postgres/), Heroku now provide support for Postgres' new fangled and very shiny [hStore](http://www.postgresql.org/docs/9.1/static/hstore.html) storage across all the Heroku Postgres 9.1 instances, which is the [current default](https://postgres.heroku.com/blog/past/2012/3/1/postgresql_91_now_default/) if you setup a Heroku Postgres database.

So, what the hell is hStore?  Well, it's flipping awesome, that's what it is.

Let's imagine for a second or two, that we're a large electrical retailer who's having bit of an identity crisis, and that we've spent way too much money on Star Wars based advertising.  We have a shed load of products that we need to store and have a PG database to put them in.

Most of the time we would reach for a products table, and then some sort of storage for the product attributes.  We could do this via adding a load of attribute columns to our products table (even though we don't really know what all the attributes are), or we could create some sort of related attributes table to store stuff in and then have to muck about adding and deleting attributes to products.

…or we could just use hStore.

### What is hStore?

hStore is essentially a No-SQL style storage method that sits within Postgres SQL databases.  It allows you to store hash style data in a single column and then do some wonderous things with it.

For instance, consider this:

We have a products table, and it has some attributes:

{% highlight sql %}
CREATE EXTENSION hstore;

CREATE TABLE products (
     id serial PRIMARY KEY,
     name varchar,
     attributes hstore
   );
{% endhighlight %}

and we have a few products to put in there:

{% highlight sql %}
INSERT INTO products (name, attributes)
    VALUES (
      'Leica M9',
      'manufacturer  => Leica,
       type          => camera,
       megapixels    => 18,
       sensor        => "full-frame 35mm"'
    ),
    ( 'MacBook Air 11',
      'manufacturer  => Apple,
       type          => computer,
       ram           => 4GB,
       storage       => 256GB,
       processor     => "1.8 ghz Intel i7 dual core",
       weight        => 2.38lbs'
    ),
    ( 'Bosch WAE28166GB Washing Machine',
      'manufacturer  => Bosch,
       type          => washing_machine,
       capacity      => 6kg,
       spinspeed     => 1400rpm,
       energyrating  => A+'
    );
{% endhighlight %}

Now, what we have here is interesting.

We have our products table, as normal, but with this attributes column of the hStore type.  This contains products as you would expect, but what we also have is three separate and very different products stored in them, with some very different attributes.  The cool thing here is that this is all in one table mashed together, but is still perfectly queryable from a conventional SQL interface.

For instance, find all products that have a 6kg capacity:

{% highlight sql %}
SELECT name, attributes
FROM products
WHERE attributes->'capacity' = '6kg'
{% endhighlight %}

or find me all products that are cameras with 18 megapixels:

{% highlight sql %}
SELECT name, attributes
FROM products
WHERE attributes->'type' = 'camera'
AND attributes->'megapixels' = '18'
{% endhighlight %}

Pretty powerful, I think you'll agree.  What's more, you can still index this attributes, and also join them onto other tables for more complex querying.  Best of all, it's all native in the database so is nice and snappy too.

This fills an interesting gap in data modelling such as the scenario raised above.  Whats more, as it's so simple to implement it much more enticing to use rather than the alternatives (such as setting up a NoSQL solution just for one model).

### Using it from Rails

But what about using something like Rails to play with this stuff?  Well, you're in luck.  Rails 4 has support for this baked in, or you can add it to Rails 3 now by using the [activerecord-postgres-hstore](https://github.com/softa/activerecord-postgres-hstore) gem that has been made available. 

You've now got the ability to do the above quite happily:

{% highlight ruby %}
Product.create(	:name => "Bosch WAE28166GB Washing Machine", 
  				:attributes => {'manufacturer' 	=> 'Bosch', 
								'type' 			=> washing_machine, 
								'capacity' 		=> '6kg',
								'spinspeed'     => '1400rpm',
       							'energyrating'  => 'A'+
							   })

Product.where("attributes -> 'capacity' = '6kg'")

Product.where("attributes -> 'type' = 'camera'")
       .where("attributes -> 'megapixels' = '18'")
{% endhighlight %}

Simple.

### Awesome, so how can I use it now?

OK - so lets say you love this so much you want to use this right now.  Simple option is to get a Heroku Postgres instance up and running and try this stuff out there however, this can cost, so if you're on a budget create a blank Rails app, push it to Heroku, and then use the new shared [PG9 add-on](http://devcenter.heroku.com/articles/heroku-shared-postgresql) that has been made available.  Within a couple of seconds you've got everything you need to try out all of the above and play with it yourself.  With both types of database you'll be able to connect to them with tools such as PGAdmin and start poking and prodding to your hearts content.

For more information on either hStore, or Heroku Postgres, simply see the links above.