---
layout: post
title: Update To Heroku San Sanity
category: Heroku
---
#Update To Heroku San Sanity

![](/images/warning.png "Warning")

Today, I pushed an update to my heroku_san sibling gem heroku_san_sanity.  

In short, the gem now provides a platform health check against Heroku prior to any deployments and lets you know if there's an issue asking you if you if you want to continue or not.  

This change was brought about after I found myself in a deploy limbo where deploy itself has occurred, but the DB migration rake tasks weren't functioning, causing my deployment to be hung halfway until Heroku fixed the issue (which luckily was only a staging deploy)
To install, simply add heroku_san_sanity to your gemfile, and you're cooking.
