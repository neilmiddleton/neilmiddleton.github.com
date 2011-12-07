---
layout: post
title: Introducing Heroku_san_sanity
category: Heroku
---

#Introducing Heroku_san_sanity
Over the past few months, I’ve been using [heroku\_san][] for all my Heroku deployments. In short, this gem wraps up the heroku git tasks into a neat little format that supports multiple environments (.e.g production, staging etc). 

However, there’s not much difference between a staging deploy and production one, so the risk of muscle memory kicks in.

Which is why I’ve created the [heroku\_san\_sanity][] gem. Adding this
gem to your Gemfile, will ensure that you’re sure you want to do a
production deployment. Before deploys to any production deployment the
following will be presented to you: 

{% highlight ruby %}
! WARNING: Potentially Destructive Action 
! This command will affect the environment: production 
! Toproceed, type “production” 
{% endhighlight %}

Only by typing in ‘production’ will the deploy continue, forcing you to mentally double check before stomping over your live application. If you don’t, nothing happens. The [code’s on Github,][] so feel free to fork and improve!

  [heroku\_san]: https://rubygems.org/gems/heroku_san
  [heroku\_san\_sanity]: https://rubygems.org/gems/heroku_san_sanity
  [code’s on Github,]: https://github.com/neilmiddleton/heroku_san_sanity
