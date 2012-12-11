---
layout: post
title: Deploying to Heroku with Travis CI
category: Heroku
---

A while back [I wrote about](/continuous-deployment-with-heroku/) how you can deploy to Heroku 'continuously',
i.e the practise of not having to manually deploy, but having the
deployment to an environment being a automated step that occurs whenever
your workflow pre-requisites are met.

The most common workflow here is that deploys occur when your test
environment has run a build on a particular branch of your source
repository and has passed all tests.

Back in my previous example I talked about using the CI service over at
[TDDium](http://www.tddium.com) and making use of their ability to set a Git URL that TDDium
pushes to when a build passes.

### Enter Travis, stage left

![Travis](/images/travis.png)

Since I wrote that post though, the guys over at [Travis
CI](http://travis-ci.org) have launched
a beta of their private repos version.  This version means that, for a
fee, you or I can push our private code to Travis and have it be tested
in exactly the same way as any other repo on Travis.  What's more, as
you're a paying customer you even get priority over the other builds,
you don't sit in the same queue.

If you've not checked out Travis I advise you to do so.  Even in it's
current beta form it's a very polished service and one that we will
definitely be sticking with in preference to Tddium.

### Add the Heroku sauce

So, how can we get this working with Heroku for getting our deployments
all automated and that?  Well, it's actually not all that hard.  Travis
lets you define a load of steps that it should execute both before and
after a deploy via the `travis.yml` which is how you configure Travis
normally.  All of these steps run in the context of your app as if you
were running the commands yourself from your terminal.  It's worth
noting at this point that the after tasks only run if everything is
tickety-boo with your test suite.

Therefore, taking this into account, you can probably start to see how
this stuff is starting to take shape.  We just need Travis to run the
push to Heroku for you.

Whilst this might sound simple, the biggest problem we need to overcome
here is that of authentication.  We can't have anyone pushing to our
application so we want to make sure that only Travis is able to push
into our application as an authenticated user.

First of all, we want to have a user to authenticate as.  You could use
your own, but you need to make your API key available to Travis so I
would recommend creating a user solely for deploying to this
application. User accounts cost nothing, and are easy to create.

So, now we have our user we need to know our API key for using with
Heroku:

{% highlight bash %}
$ heroku auth:token
44cd2f55f47d676b49be6d5d54365a1c5743b694
{% endhighlight %}

Take a note of this as you'll be needing it later on.  If you're not
logged into the Heroku toolbelt as you're deployer user you'll need to
change this first:

{% highlight bash %}
$ heroku auth:logout
$ heroku auth:login
{% endhighlight %}

OK, so now we've got our API token and we're ready to roll

### Putting it all together

So, now we just need to configure Travis to do the magic when we're
done.  First up, let's encrypt our API token just to add some more
security:

{% highlight bash %}
$ gem install travis
$ travis encrypt your_github_username/your_github_repo \
  HEROKU_API_KEY=<your_heroku_key>
{% endhighlight %}

So, now we have our encrypted key, let's tell Travis about it by adding
it to our `.travis.yml`:

{% highlight yaml %}
env:
  global:
    - secure: <your_really_long_encrypted_key>
{% endhighlight %}

So you know, Travis takes this long key at runtime and applies the
contents to the environment, hence why this works.

## Doing the deploy

At this point, we have a choice as there is two ways of acheiving this
next step.  Which you decide to use is up to you but both have their
benefits.  One is the more 'standard' route of using Git to push into
Heroku, the other is to upload a pre-compiled slug instead.

### Pushing your application with Git

So, we've decided to use Git which means that we need to deal with SSH
and some other fun.  By adding the following to our `.travis.yml` we can
acheive this:

{% highlight yaml %}
after_script:
  - wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
  - git remote add heroku git@heroku.com:YOUR_HEROKU_APP.git
  - echo "Host heroku.com" >> ~/.ssh/config
  - echo "   StrictHostKeyChecking no" >> ~/.ssh/config
  - echo "   CheckHostIP no" >> ~/.ssh/config
  - echo "   UserKnownHostsFile=/dev/null" >> ~/.ssh/config
  - heroku keys:clear
  - yes | heroku keys:add
  - yes | git push heroku master
{% endhighlight %}

So, what does this do?  Well, first off we're telling Travis which app
we're talking about and which key we're looking to use.  We're then
having to tell Heroku itself about the SSH config we have locally on
Travis and make sure we're OK with the SSH hosts.

We're doing this on every deploy as these things can change and
it just makes things easier to debug.  Lastly, we're kicking off the
push into master on Heroku.  It's worth making sure that you've got some
sort of notifications system setup at this time so you can spot that
things are working as they should.  Tools such as
[Campfire](http://campfirenow.com/) are ideal for
this sort of thing and are easily added (for
[Travis](http://about.travis-ci.org/docs/user/notifications/), and
[Heroku](https://addons.heroku.com/deployhooks)).

### Pushing compiled slugs up to Heroku

This method takes care of the pre-compilation of your application on
Travis rather than letting the Heroku Git system take care of it.  By
using this method we don't need to worry so much about SSH as we're
authenticating with our API key that we set up earlier.

In order to make this possible however, we are going to use a third
party library called [Heroku
Anvil](https://github.com/ddollar/heroku-anvil) by [David
Dollar](http://david.dollar.io/), who works at
Heroku.  This isn't a Heroku supported library as such, but is the next
best thing.  What Anvil does is provide an alternate build process to
the one that you get with Git, and lets you carry out a load of tasks
locally as opposed to on the Heroku stack itself.

In order to get this working we need an after script like this:

{% highlight yaml %}
after_script:
  - wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
  - heroku plugins:install https://github.com/ddollar/heroku-anvil
  - heroku build -r <YOUR_HEROKU_APP_NAME>  -b
    https://github.com/heroku/heroku-buildpack-ruby.git
{% endhighlight %}

Now what this code does is relatively simple.  First it installs the
Heroku toolbelt, installs Anvil into that as a plugin and then fires off
the build process.  At this point we're telling Anvil where our live
application is so that the push goes to the right place.

Note we're having to declare the buildpack here.  I found in testing
that this was a required option.  Buildpacks are Heroku's way of
identifying your code for execution, so you'll need to locate the right
buildpack for your project and add it to this script.  All buildpacks
are found in the [Heroku Github account](http://www.github.com/heroku)

### Wrapping it up

Now, neither of these examples are exhaustive representations of what
you can with these scripts.  For instance,
you may have multiple environments and branches in play and want to
setup continuous deployment for each and every branch with a matching
environment.  You'll need to consider excluding CI builds carried out
against Pull requests, or other Rubies for instance.  Well, Travis provides a selection of environment variables
that you can use to enhance the above script and make intelligent
changes to things like your Heroku app name, or where your code [should
be pushing to](/deploying-topic-branches-to-heroku/):

* `TRAVIS_BRANCH`: The name of the branch currently being built.
* `TRAVIS_BUILD_ID`: The id of the current build that Travis uses
internally.
* `TRAVIS_BUILD_NUMBER`: The number of the current build (for example, "4").
* `TRAVIS_COMMIT`: The commit that the current build is testing.
* `TRAVIS_COMMIT_RANGE`: The range of commits that were included in the push
or pull request.
* `TRAVIS_JOB_ID`: The id of the current job that Travis uses internally.
* `TRAVIS_JOB_NUMBER`: The number of the current job (for example, "4.1").
* `TRAVIS_PULL_REQUEST`: True if the current build is for a pull request.
* `TRAVIS_SECURE_ENV_VARS` : Whether or not secure environment vars are being
used. This value is either "true" or "false".

Therefore we can now do clever things such as:

{% highlight yaml %}
after_script:
  - gem install heroku
  - if [[ "$TRAVIS_PULL_REQUEST" == "true" ]]; then echo "This is a
      pull request. No deployment will be done."; exit 0; fi
  - if [[ "$TRAVIS_BRANCH" == "master" ]]; then git remote add heroku
      git@heroku.com:YOUR_PRODUCTION_HEROKU_APP.git; fi
  - if [[ "$TRAVIS_BRANCH" == "staging" ]]; then git remote add heroku
      git@heroku.com:YOUR_STAGING_HEROKU_APP.git; fi
  ...
{% endhighlight %}
and so on.

Bear in mind that you might also want to add this to this set of
commands.  For instance, you may want to run database migrations, or do
some asset precompilation and so on.  However, now you're authenticated
and firing on all cylinders adding these tasks should be pretty academic
a task.

So, we've got everything setup and we should have a well oiled
continuous deployment machine centered around Travis CI and Heroku. What's more, now it's setup and working, we can pretty much forget about
ever having to deploy manually again.

*The 'Heroku' book, published by O'Reilly, is [available for
pre-order on
Amazon](http://www.amazon.co.uk/gp/product/144934139X/ref=as_li_tf_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=144934139X&linkCode=as2&tag=neilmidd-21)
now*
