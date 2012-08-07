---
layout: post
title: Continuous deployment with Heroku
category: Heroku
---
One of the current trends in the agile world is that of deploy soon, deploy fast and deploy
often.  The idea here is that code should not be sitting still for long.
Your developers should be coding a feature, and getting it out to the
world as soon as possible to you're able to gain feedback on that
feature and iterate as you feel the need to.  There's a number of things
that developers can do in order to make this process smoother, ranging
from the simple things such as testing to more the more advanced such as
setting up continuous integration systems and automating as much of the
workflow as possible.

One of the current steps that a development team can make, that seems to
generate the most debate in the office is that of continuous deployment.
Continuous deployment is the practice of automating your software
deployment into a particular environment, be it staging or production,
and thus removing the need for someone to have to step in and carry out
what should be a pretty much 100% automatable task.

###Are you insane?

But what are the benefits of continous deployment?  Well, for starters
as a developer, you know that if the commit you are about to make is
going to be live to the world in only a few short minutes that you will
probably want to double check everything you have written and make sure
that nothing will be broken by adding your change to the codebase.  This
forces you, as the developer, to sense check what they are doing and think about how
features should make their way into the codebase.

Another potential benefit is that of deploying features piecemeal bit by
bit as they are developed (as your coding and deploying continuously you
have to develop features this way, unless you want to not commit and
integrate for ages).  This leads you to develop features in a more of an
iterable fashion, but also allows you to get feedback on the parts you
have deployed before you've finished building the rest.

Some people
would argue that feature branches are the order of the day here, taking
the code to one side and developing a feature whole before merging it
back into master in one big chunk when complete.  Whilst this is fine,
you are disconnecting your feature development from everything else that
is going on in your codebase and potentially introducing risk. Something
you need to consider later on down the line.

So, how can we manage this with [Heroku](http://heroku.com)?  Well, luckily there's a very
simple way.

###Continuous deployment with Git

In order to host your application on Heroku, Heroku require you to
provide your codebase via the Git source control system.  Anything that
is pushed into the `master` branch is deployed and live, whilst all the
other branches sit there minding their own business.

Now, I wouldn't advocate using Heroku as your main source store as there
are much better alternatives out there such as
[Github](http://www.github.com), but it does demonstrate that your code
can be live with a simple `git push heroku master` automagically fired
from your workflow when appropriate.

But hang on, lets backtrack for a minute.  Earlier I talked about the double
checking developer committing his code into a tree that immediately goes
live.  Doesn't this introduce some risk?  Well, yes it does. Your
developer is still able to push broken code into production, and the
ramifications are significantly more than just pushing it into Git only.
So how to we mitigate this risk?  How can we ensure that the code we are
automatically deploying passes all of our carefully thought out tests
without issue?

###Tddium

Enter the Heroku addon, [Tddium](https://addons.heroku.com/tddium) stage left. Tddium is
essentially the continuous integration version of Heroku's hosting
platform.  By simply [setting up
tddium](https://devcenter.heroku.com/articles/tddium), setting up your
test suite and pushing the code, you have an automated way of knowing
that whatever is in your chosen branch is passing tests or not, and whats
more you're doing it in an environment that's detached from your local
workstation, so no deep down configuration or software installation that might
make your suite pass locally are around to mess things up.

Once you have this up and running you know your codebase is golden,
how do we deploy?  Well, there's a couple of ways.  Firstly, Tddium will
let you setup a git URL that will be pushed to on a successful suite
pass.  By entering your Heroku Git URL here Tddium will automatically
run a deploy whenever tests pass, thus achieveing the goal of continuous
integration with a good level of sense checking.  Another way, if you're
a little more adventurous is to write your own HTTP endpoint that
a service like Github can hit and get Tddium to push your Git code there
instead.

<iframe width="560" height="315"
src="http://www.youtube.com/embed/NiMa4Qhv3QE" frameborder="0"
allowfullscreen></iframe>

###Smoooth

At the end of setting this up, you're now able to develop code, testing
it locally, and push that code into your Git repo, knowing that there is
nothing left for you to do.  Your code will be on it's way to whichever
environment you have configured (remember, you can start with staging
only) and there's nothing more you have to do.

Whilst continuous deployment initially might sound quite scary there are
numerous benefits to be had.  You need to take more care with the
commits you make, and you have to consider how you develop your code.
You also need to consider the resilience of your deploy process and what
happens should deploys not go as cleanly as desired.

But that's all good right?


