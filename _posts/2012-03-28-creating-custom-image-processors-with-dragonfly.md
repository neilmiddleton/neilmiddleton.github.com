---
layout: post
title: Creating custom image processors with Dragonfly
---
Every now and then you come across a project which does things a little different.  The other day I was toying with solving one particular problem, and that is the one of heavily stylised images.  In this particular case, the application I was working on needed to take a user uploaded image, and present it at a pre-defined size, but with a bottletop like shroud around it, essentially making the image round.

![](/images/the_shame.png "Before")
![](/images/the_shame_transformed.png "After")

After some head scratching I came across the ability to add processors to Dragonfly which is the library I'd chosen to use image management.  Dragonfly is a great little gem that allows you to receive uploaded images, and then present them back to the user in a variety of ways on the fly.  For instance:

{% highlight ruby %}
image_tag current_user.profile_image.thumb("75x75#").url
image_tag current_user.profile_image.thumb("100").process(:greyscale).url
{% endhighlight %}

and so on…

So, firstly I determined that this bottletop effect could be done by making an overlay PNG that could be applied to a resized image, and then flattened down into a new PNG.  Deagonfly uses ImageMagick internally when plugged into a Rails application, so it seemed that simply creating a Dragonly processor that issued the correct command would get the job done.

Sure enough, that's all you need to do:

_lib/image_processor.rb_
{% highlight ruby %}
class ImageProcessor
  def convert_to_small_bottletop(temp_object)
    tempfile = new_tempfile
    args = " -compose atop -geometry +0+0 -resize 75x73 lib/bottletop_overlay.png #{temp_object.path} #{tempfile.path}"
    full_command = "composite #{args}"
    result = `#{full_command}`
    tempfile
  end

  private

  def new_tempfile(ext=nil)
    tempfile = ext ? Tempfile.new(['dragonfly', ".#{ext}"]) : Tempfile.new('dragonfly')
    tempfile.binmode
    tempfile.close
    tempfile
  end
end
{% endhighlight %}

What this code does is take a given object passed in my Dragonfly, and then creates a new tempfile from it.  This tempfile then has an ImageMagick `composite` transaction applied to it, and the file is then returned to Dragonfly to continue on with.  In theory, you can apply any transformation to an image in this manner, so it makes it widely extendable.

Before this will work however, you need to tell Dragonfly about your new processor:

_config/initializers/dragonfly.rb_
{% highlight ruby %}
require 'image_processor'

app = Dragonfly[:images]
app.processor.register(ImageProcessor)
{% endhighlight %}

Now that this is done, we can call our processor as per any other built in Dragonfly processor:

{% highlight ruby %}
image_tag current_user.profile_image.process(:convert_to_small_bottletop).url
{% endhighlight %}

And there you go.  Debugging these processors can be quite tricky, and Rack/Cache also does quite a good job of making your life difficult, but aside from this, you have a large amount of power at your fingertips.
