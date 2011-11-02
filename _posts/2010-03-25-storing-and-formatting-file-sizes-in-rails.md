---
layout: post
title: Storing And Formatting File Sizes In Rails
---
#Storing And Formatting File Sizes In Rails

I’ve just been looking at a problem relating to file sizes in a Rails
application. Essentially, I needed to store a number of different file
sizes in a config file, and show them to the user in as nice a way as
possible. It would seem that this is something that Rails does out of
the box, and here’s how. Rails provides a number of little functions in
[ActiveSupport::CoreExtensions::Numeric::Bytes][] that mean you can
declare sizes really easy:

{% highlight ruby %}
    10.megabytes
    50.gigabytes
    1.terabyte
{% endhighlight %}

and so on. Whilst this is a nice thing to type, Rails will then think
about these items in bytes. This means that when you come to display
these sizes you don’t get an exactly user friendly description:

{% highlight ruby %}
    10485760
    53687091200
    1099511627776
{% endhighlight %}

Not ideal. But never fear, for [ActionView::Helpers::NumberHelper][] is
here, with number\_to\_human\_size. Which pretty much does exactly what
you want it to:

{% highlight ruby %}
    number_to_human_size(10.megabytes) = "10 MB"
    number_to_human_size(50.gigabytes) = "50 GB "
    number_to_human_size(1.terabyte) = "1 TB "
{% endhighlight %}

Rails makes shit easy.

  [ActiveSupport::CoreExtensions::Numeric::Bytes]: http://railsapi.com/doc/rails-v2.3.5/classes/ActiveSupport/CoreExtensions/Numeric/Bytes.html#M006336
  [ActionView::Helpers::NumberHelper]: http://railsapi.com/doc/rails-v2.3.5/classes/ActionView/Helpers/NumberHelper.html#M005712
