---
layout: post
title: Remember to timeout your tasks
category: Heroku
---
#Remember to timeout your tasks

Time for a just-learned-from-experience post.

On my employers website, kyan.com, we have a number of counters which can be found in the footer of every page.  These numbers include straight-forward things like the number of people that have registered on webmeetguildford.co.uk to more odd things such as the number of pixels that our designers have pushed.

Whilst you may think that these numbers are false, they are all actually based on real world counters (which may or may not be then massaged into something more entertaining).  For instance, the pixels counter has a look at how many design hours have been logged in our intranet and goes from there.

But enough of this, what does this have to do with Heroku and tasks? Well, all of these stats are pulled from various internal APIs as a background rake task.

That's simple and just fine, right?

Well no.

This is normally fine, unless you couple this with a flakey internet connection (which we've been suffering from recently).  It appears that if you lose your connection, the API calls in your task won't complete and the task then runs on and on, never reaching any deterministic point in which to die.  Ultimately this ends up costing you money.

So, how do we get around this?  Well, simple (with Ruby 1.9.2).  Timeout!

{% highlight ruby %}
require 'timeout'

Timeout::timeout(10) {
  ... thing that should not last forever
}
{% endhighlight %}

This will apply a 10 second time limit to the included code, and stop it with a `Timeout::Error` should it overrun.  

By applying this to your background tasks you can be sure that you're never sitting there paying for processes which are quite happily spinning their little hearts out in silence.