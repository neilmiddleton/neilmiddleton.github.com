---
layout: post
title: The myths of cloud hosting
categories: Heroku
---
#The myths of cloud hosting

Day to day I see a lot of comments from people related to hosting on a cloud hosting environment.  It seems that if you're not a developer, that the idea of cloud hosting fills you with fear and uncertainty, and is something that should be trodden carefully around.

However, it's clear to me as a developer that this is the wrong way to look at it.  I've been using cloud hosting (namely [Heroku](http://www.heroku.com)) for a couple of years now, as well as tried out a few others (such as [EngineYard](http://www.engineyard.com)).  Heroku is the one that floated my boat, so that's the one I will primarily refer to here.

So what are the problems that people have?  Well, heres the most common in FAQ form:

---


## I don't want my sites to go down...

This is by far the most common thing I've heard, that somehow once you host in the cloud and are no longer able to actually SSH into your servers you're somehow compromising on your hosting.  This is simply not true.

Let me describe a couple of scenarios.  One is small business, say 20 or 30 people.  They manage a few servers for their clients, and the guys that look after them are typically the ones who puts themselves forward for the task, but primarily they are developers.  These guys work office hours.

The second is large company that only does hosting.  Their entire reputation is based on being able to provide good hosting, so their monitoring their servers 24/7 in more ways than you can think of, and employ nothing but experts to look after the servers.  These guys work all the time.

Now let's describe a common scenario.  It's five in the afternoon, and everything goes pop.  All of your sites go down.  Now, most people would feel happier starting to frantically SSH into stuff to try and see what the problem is, or randomly start rebooting servers.  

With cloud hosting, it's somewhat different.  You know that by the time you become aware of a problem, that it's already being looked at by a team of experts with some sort of structure (for instance, Heroku have Incident commanders, and people on call at different times) in a methodical way.  You also know that the chances are they would have fixed the problem before you were even aware.  A lot of people have trouble with the idea of sitting back and doing nothing when sites go down, but you're not doing it wrong, you're leaving it up to the people who know about these things the best.

Now, there is an exception to this, namely those events when your application goes down, but as that's not normally something that is affected by your hosting, I won't go into that here.

---


## I'm concerned about the privacy of my code and my data

OK - this is a simple one.  All hosting companies stake their reputation on two things.  Reliability, and confidentiality.  If any hosting shop is found out that they are peeking into / sharing customers code and data, they might as well pack up shop and go home.  This is simply something that a company such as Heroku or others would even consider risking.  What's more, most of these companies have very strict processes in place to ensure that they don't even chance privacy breaches.  For instance, GMail support engineers will simply not look at any email, even if you give them explicit permission.  The chances are that if you're looking at a well known cloud hosting firm, that they will take privacy /very/ seriously.

Now, think back to the shop that is generating this code / data.  What are the chances that:

- Everyone in the office has access to the code...
- A fair few people (but you're not sure who) have access to the production servers...
- A few people (but again, no idea who really) have access to your production data...

See?  You're probably better off with the people [who do this stuff for a living](http://policy.heroku.com/security).

---


## I'm concerned about the cost

Again, simple.

Let's say you want to host a server.  You buy a new shiny Dell (let's say a middle range one, about $2,000), you then pay one of your staff to set it up which takes them five or six hours (let's call that $600), and you then buy some rackspace from somebody for it (another $500/month maybe?).  Each week you need to spend a couple of hours looking at it ($500/month) and fix it when the stuff goes bang (let's average at $200/month).

So, even though these numbers are /extremely/ approximate, you're looking at $2,600 to get started, and a running cost of around $1,200/month to look after it in staffing costs.  That's $3,800 for your first year.  This will let you scale to, say, 5,000 active users.

Now let's put this into Heroku money.  Your $3,800 will get you some dyno hours.  At $0.05c/hour it will get you a LOT of hours.  In fact, it will get you 76,000 dyno hours, or enough to run a single dyno for about eight years.  Granted a single dyno won't give you the same grunt, so let's say you're running two concurrently - that's four years worth, and so on.  What's more, you also have two or three hours a week of extra billable hours being generated because you're not having to support your own hardware.

However, there will be a tipping point.  There is a point with any project where the usage will get high enough that cloud hosting costs don't make sense.  If you're Facebook or Twitter you won't be looking at outsourcing your hosting, but then again, do you really think that the cost of hosting is a major concern for them?  Chances are, by the time the size of your hosting becomes a problem, cost won't be.

---


## What backups are available?

See my previous comment about reputation.  What could be worse for a hosting company?  Hosing your website AND all your data, only to be able to say "sorry", or hosing your website, but being able to bring it up exactly how it was with very little pain on the customers side.  Whilst you may consider yourself paranoid, for the most part, your hosting company will be ten times more so.

For instance, read the [Heroku Postgres docs](http://devcenter.heroku.com/articles/heroku-postgres-documentation).  Can you reasonably think about implementing the following:

> All Heroku Postgres databases include Continuous Protection. This feature archives the PostgreSQL Write-Ahead-Log (WAL) to a geographically-distributed, highly durable storage service. In the event of a catastrophic failure, the WAL can be “replayed” to reconstruct a database.
> 
> Continuous Protection performs the WAL archival every 60 seconds or 16 MB of new data, whichever comes first. Therefore your data is protected by this feature once it has been committed for at least one minute. During times when the write-throughput on a database is extremely high, our service may favor data transaction volume over WAL archiving for short periods of time. This may result in a slightly longer interval for data to be protected.

Most people are happy with daily backups, the paranoid go for hourly.  Only the committed go further.  With cloud hosting it's not really something you need to worry about.

However, with backups, you should.  You should *always* worry about backups. If you're not [protecting yourself in at least some way](http://devcenter.heroku.com/articles/pgbackups), you're a fool.

---


So, overall, I (obviously) think cloud hosting is the way forward.  I genuinely believe that I, as a developer or as someone who owns a development shop, should not have to worry about servers and hosting, but should be able to just throw their code at a partner and leave them to worry about it.  There are so many downsides to running your own hardware that they offset any upsides you might think of.  Granted cloud hosting does not suit everyone, but you'd have to be treading outside of the ordinary to not make it worthwhile.

