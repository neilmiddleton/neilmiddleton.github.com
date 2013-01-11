---
layout: post
title: Heroku / Asset Pipeline FAQ
category: Heroku
wip: true
---
From having a look around on Stackoverflow, it seems that a fair chunk of the questions on there all relate to the new Rails 3.1 Asset Pipeline, a part of Rails which has attracted a relatively large amount of comment and problems.

For starters this article won't tell you how to get the Asset pipeline working perfectly locally - there's a bunch of [other](http://guides.rubyonrails.org/asset_pipeline.html) [documentation](http://guides.rubyonrails.org/asset_pipeline.html) out there which is much better than I anything I might try to do, so if you're having problems getting the Asset pipeline working in development, take a look there first.

Additionally, Heroku also have a stack of documentation dedicated to deploying Rails 3.1 to the platform and a lot of common issues are also described there:

* [Rails 3.1+ Asset Pipeline on Heroku Cedar](https://devcenter.heroku.com/articles/rails3x-asset-pipeline-cedar)
* [Using a CDN Asset Host with Rails 3.1](https://devcenter.heroku.com/articles/cdn-asset-host-rails31)
* [Getting Started with Rails 3.x on Heroku/Cedar](https://devcenter.heroku.com/articles/rails3)

If these don't help, then read on.

###So, what are the basic settings for deploying to Heroku?

There are numerous ways to configure the Asset pipeline for Heroku, but here's a way that I know to work just fine:

{% highlight ruby %}
# Heroku REQUIRES this to be false
config.assets.initialize_on_precompile = false

# Include any files that you want to link to directly from your templates directly.
config.assets.precompile += ['mobile.css', 'mobile.js']

# Not required, but a good best-practice for performance.
# This setting will compress your assets as much as possible using YUI and Uglifier by default
config.assets.compress = true

# Allow fingerprinting of asset filenames - good for caching.
config.assets.digest = true

# Configure the sendfile headers for Heroku.  "X-Accel-Redirect" is also a good value for this since Heroku use Nginx.
config.action_dispatch.x_sendfile_header = nil
{% endhighlight %}

If you're doing anything out of the ordinary this config might need some tweaking.  See the Rails guide for more information.

###What do I put into source control?

Within your project you should have your raw assets (in `app/assets`) in source control.  When you locally precompile your assets this will generate a load of new asset files, and a `manifest.yml` file.  Do not put these into source control - Heroku does not need them.

When you deploy, Heroku will take your application and identify that there is a requirement for the asset pipeline.  Heroku will then run `rake assets:precompile` within the context of your application and add the precompiled assets to your application slug.  Once this has happened, and the other compilation steps have finished, your slug and its assets will be pushed out to the dyno grid.

If you push your own compiled assets into source control Heroku will recognise this and not try another precompile.  Therefore, I generally find that letting Heroku do the compile reduces the scope for bad things to happen and saves you a step pre-deploy.

Not adding pre-compiled assets to Git is especially important if you're using something like [Asset Sync](https://rubygems.org/gems/asset_sync).

###My JavaScript / CSS isn't there

This could be caused by a couple of things.  Firstly, you might be using an asset host (check your config) that doesn't resolve, or something broke during the asset precompile in deployment.  To debug, manually run a

{% highlight ruby %}
heroku run rake assets:precompile
{% endhighlight %}

and see if your assets appear.  If so, then check your deploy output for messages about asset precompilation etc.  The most common reason for failure is that your application is trying to initialise a database connection or similar.  As the Heroku slug compiler isn't aware of your environment this will fail.  There are a couple of ways round this.  One is to fix your application so that this doesn't happen, or add the `user_env_compile` [addon](https://devcenter.heroku.com/articles/labs-user-env-compile) to your application which will make your application aware of its environment.

If you're still not seeing any assets, check your logs for clues on what might be going wrong.

###Heroku is telling me file X isn't precompiled...

By default, Rails will not let you serve every asset as a standalone file. Therefore, if you're wanting to link to a specific JS file or similar, you need to add it to your `config.assets.precompile` array.  See [here for more information](/precompiling-non-default-assets-with-rails-3/).

###How can I get my assets onto a CDN such as S3 / CloudFront?

The easy answer here is [`asset_sync`](https://github.com/rumblelabs/asset_sync) a gem available from [Rumble Labs](http://rumblelabs.com/) which deals with syncing your assets to S3 during the deploy process on Heroku.  This simple extends the regular assets precompilation with a sync of your assets.

By using this, you can then hook up CloudFront to add a proper CDN to your S3 bucket.  Either way, you'll need to tell your Rails application where the new assets location is.  This is done with a simple config setting in your relevant environment:

{% highlight ruby %}
config.action_controller.asset_host = ENV["YOUR_S3_BUCKET"]
config.action_mailer.asset_host = ENV["YOUR_S3_BUCKET"]
{% endhighlight %}

Note, the mailer settings are for when you are pushing out email containing images and so on.  Those emails need your assets too!

####Updates

This is a living article.  Over time I will add further information as I see the need.  In the meantime feel free to ask questions or add more information, or better still, [fork this document](https://github.com/neilmiddleton/neilmiddleton.github.com) and send me a pull request.
