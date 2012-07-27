---
layout: post
title: Multi-provider PaaSes - what exactly do they offer?
category: Heroku
---
#Multi-provider PaaSes - what exactly do they offer?

Recently there has been a vast array of Platform-as-a-service providers popping up all over the place offering a vast array of feature sets, and an even wider array of pricing strategies.

One thing which has become prominent is that these new PaaS providers are using the number of different infrastructure choices that they offer as a selling point.  For instance AppFog will proudly tell you that you can use them to deploy your application to HP Cloud, Rackspace, AWS US East, Ireland, Singapore, and more….  In fact, they are so proud of this fact, it's front and center on their homepage.

One provider that doesn't do this though is Heroku, effectively the pioneer of this business model.  Heroku only provide sevices in the US-East region of AWS.  There's no option to have it elsewhere, there's not even really any mention of this anywhere in the documentation.  As far as a developer is concerned, Heroku is essentially a thing, out there somewhere, in the cloud.

So, why does Heroku not offer multiple places to deploy your application?  Lots of people will argue that being able to deploy to multiple sources is a good thing and can only be good for uptime.  If U-East goes down, then your app does too, right?

To start off with, generally speaking, most of the PaaS providers that let you deploy to multiple locations will only let you put your app in one particular place.  I'm not aware of anyone who will let you throw up your application in more than one location / more than one platform without jumping through some hoops manually.  So, given this fact, you're no better off - if your selected region goes pop, so does your application regardless of where it is.

Next, you could maybe get Heroku to potentially spread your application across multiple regions (say US-East and US-West).

Whilst this may sound nice in simple in reality, it's all too easy to forget that there is over 2,500 miles between these two locations, which means latency.  This latency might only be 50ms or so, but when you're querying a dataset that happens to be based in the other region, that's going to hurt your performance significantly.

Then there's bandwidth. Whilst platforms like AWS do not charge for bandwidth within a given zone, they do charge for bandwidth cross country which is going to affect the pricing that you'd need to pay to cover costs.

So, how do you get around this problem?  Well, if you're building an application that is mission-critical, and just CANNOT afford to go down at any time, even for just a few seconds, then maybe using just one provider isn't for you.  Deploying your application to more than one provider on different platforms, and using one as a hot redundant instance could be a way forward (Heroku could potentially do this for you, but someone needs to bear the cost somewhere).

Alternatively, the good old fashioned do-it-yourself trick might make more sense, but even with this you need to consider deploying to more than one datacenter to mitigate localised problems such as power outages and so on, which then leads you back in the direction of a PaaS.

The key is that no two applications are the same, so the hosting can't be either.  You need to think about your application, it's uptime requirements, and also how much you think you can spend to be able to achieve that.

The main thing to remember is that uptime costs. There are many ways of increasing uptime past the 'standard' that a PaaS such as Heroku might provide, but these come at a very high cost, and an even higher complexity.  You will commonly have more than one application deployment to worry about, DNS failover for when things go wrong, latency issues to think about, the list goes on.  99% of people just can't justify the extra time that this work entails.

Personally, I'm happy with having my stuff only in US-East on Heroku.  I know that it's going to be up far more than I could manage on my own, and that someone else is looking after it for me.  What's more, I also realise that to get that additional 9 on my uptime figures will be a cost that my clients just won't enjoy bearing, and will have difficultly justifying.

Saying that though, everyone's needs are different, so your mileage may vary.
