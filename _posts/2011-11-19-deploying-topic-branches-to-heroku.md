---
layout: post
title: Deploying topic branches to Heroku
category: Heroku
wip: true
---
#Deploying topic branches to Heroku

With Heroku it's super easy to create and deploy applications, almost to the point where hosted production applications are nothing more than a commodity.

Due to this, it's really easy to deploy your production environment, and then create sibling test environments for trying out various things such as deploys, load tests, or for deploying topic branches.

However, something worth noting is that Heroku ONLY deploy the master branch for a given git repository.  So how do you manage this?  Well, Git allows you to push one branch into another.  For instance, lets say you want to push your staging branch onto Heroku's master branch of your staging application, which you've already setup.  Simply run:

{% highlight bash %}
git push staging staging:master
{% endhighlight %}

This will push your staging changes into the Heroku master branch, and thus deploy it.

Now, if this is a common occurence you might want to shortcut this and make it happen by default.  This can be done by telling Git to always push from one branch to another.  For instance, for the same example as above.

{% highlight bash %}
git config remote.staging.push staging:master
{% endhighlight %}

replacing `remote.staging.push` with `remote.your_remote.push` and `staging` with your topic branch.  Once you've done this, you can simply push to the appropriate remote:

{% highlight bash %}
git push staging
{% endhighlight %}

which will yield output confirming the branch has correctly deployed:

{% highlight bash %}
To git@heroku.com:myapp.git
   16c73af..b1a78b4  staging -> master
{% endhighlight %}
