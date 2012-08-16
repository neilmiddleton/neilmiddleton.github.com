---
layout: post
title: Results of the Rails hosting survey 2012
category: Heroku
---

Today, over on the [ Planet Argon ](http://blog.planetargon.com/entries/2012/8/14/rails-hosting-survey-2012-results-are-in) blog the [ results ](https://planetargon-blog.s3.amazonaws.com/images/infographic-2012-survey.png) of their 2012 Rails hosting survery were released.

![](/images/relational_databases_2012survey.png)

Taken from a sample of 1,306 developers the survey covered items such as which tools are used for development, through to how people monitor their applications once they are in production.

There's a few interesting points which I thought were worth mentioning:

* 94% of developers are using Git over Subversion / Mecurial and so on.
* 60% of developers now say that they prefer to use PostgreSQL over MySQL ( a 43% climb! )
* 59% of developers are monitoring their application, mostly ( 91% ) using the excellent service over at [ NewRelic ](http://newrelic.com).
* 46% of developers were **not** being notified when they're site when down, which is verging on astonishing.  There are so many simple tools out there such as [ Pingdom ](http://www.pingdom.com) or your hosts [ Status site ](http://status.heroku.com) that this is a no-brainer.
* Unicorn is growing big time - now bigger than Mongrel and FastCGI put together
* Over a quarter of all developers prefer to host in the cloud via services such as [ Heroku ](http://heroku.com) or [ EngineYard ](http://engineyard.com).  Another 21% can't be bothered with the details of application deployment and just want somewhere to stick their code.

So, the general consensus seems to be that if you're running on a stack using Git and PostgreSQL, and are making use of automation and having a library of add-ons that you can plug into your application for monitoring, then you're in the sweet spot.
