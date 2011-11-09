---
layout: post
title: Using webfonts in the asset pipeline
---
#Using webfonts in the asset pipeline

Now that we have the asset pipeline, you need to include your fonts in a slightly different way.

All assets inside /app/assets fonts, images, audios, javascripts and stylesheets are included, but to get the appropriate links in your CSS etc you need to use 

{% highlight sass %}
asset-url("my-webfont.eot", font);
{% endhighlight %}

However, before this will work, you'll need sass-rails in your Gemfile:

{% highlight ruby %}
gem 'sass-rails'
{% endhighlight %}

Once this is done, you should see proper url() declarations appearing in your CSS.

For more info on the asset pipeline, read the [Rails Guide][].


[Rails Guide]: http://guides.rubyonrails.org/asset_pipeline.html