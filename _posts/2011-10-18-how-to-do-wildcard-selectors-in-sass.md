---
layout: post
title: How to do wildcard selectors with Sass
wip: true
---
#How to do wildcard selectors with Sass

To produce:
{% highlight css %}
.blah[class*='span']  {
  text-align: center;
}
{% endhighlight %}

use:

{% highlight css %}
.blah
  &[class*='span']
    text-align: center
{% endhighlight %}
