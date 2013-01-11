---
layout: post
title: Precompiling non-default assets with Rails 3
wip: true
---
#Precompiling non-default assets with Rails 3
I've just been playing with an interesting problem whereby Rails 3 wasn't precompiling assets as I would expect.

I have an application which contains two main javascript 'bundles'.  One for the common man, and one for the administrators only.  Now there's no point in serving a whole load of admin stuff to regular users hence the seperation.

However, when precompiling my assets I noticed that Rails was only doing the default application.js, and ignoring my admin.js.

Well, it turns out you need to manually tell Rails that you've added that additional resource.  Luckily this is nice and easy:

{% highlight ruby %}
config.assets.precompile += ['admin.js']
{% endhighlight %}

Job jobbed.
