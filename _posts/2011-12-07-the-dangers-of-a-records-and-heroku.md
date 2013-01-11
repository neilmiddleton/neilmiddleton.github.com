---
layout: post
title: The dangers of A-records and Heroku
categories: Heroku
wip: true
---
#The dangers of A-records and Heroku

Over the weekend, Heroku suffered, as all large hosting companies tend to, from a very large scale distributed denial of service (DDoS) attack. Luckily for us though, as Heroku is a fully managed platform this isn't something that us mere developers would normally need to worry about as Heroku are on it and trying to resolve the problem before you would normally even know about it. (Big thanks to [Mark Imbriaco](https://twitter.com/#!/markimbriaco) and his daughter required for this one.)

So, why the blog post?

Well, in the post event report Heroku raised an important point about DNS which I will re-raise again here in the hope that more people are aware of the problem and work around it.

Essentially the problem lies with DNS and the A-record.  Typically, for a top level (or apex) domain such as `neilmiddleton.com`, I have to set an A-record to resolve that to an IP address.  Therefore `neilmiddleton.com` must always resolve to an IP address.  However, with subdomains such as `www.neilmiddleton.com` I can set this to be a CNAME (or alias) of another domain.  Therefore when requesting the location of `www.neilmiddleton.com`, the DNS seems that this is just an alias of `neilmiddleton.github.com` which in itself has an IP address, so that's the one thats used.

Now, coming back to the DDoS attack over the weekend.  In this situation, there's a flood of traffic coming into the Heroku IPs.  Normally, one response to this would be to direct traffic via another means temporarily, but due to the fact that everyone has their A records pointing at those IP's the scaling options are more limited.

With CNAME's however, these can be moved around almost at will, meaning that companies like Heroku are able to scale horizontally, and change where their own domains are resolving to thus allowing them to move traffic around.

So, not using A records appears to be the best answer here.  Always use CNAMEs where possible which means you can leave Heroku etc to worry about where your applications are sitting at any one time.  However, A records don't allow you to do this, but there are a couple of ways around it.  One approach is that taken by [Zerigo](http://zerigo.com), who let you redirect one DNS record to another (which happens purely in their system).  This means you can setup `neilmiddleton.com` to redirect to `www.neilmiddleton.com` at a DNS level.  This unfortunately though means that you can't host anything on the top level domain, which is where you need something like [DNSimples](https://dnsimple.com/) ALIAS setup, where they allow you to CNAME an apex domain, again something that's purely handled internally to them.  The net result is the outside world see a regular A record pointing at an IP, whereas internally it's a CNAME.

Both these solutions are clever, but something that isn't part of DNS itself so you're limited on where these things can be done.  The core thing here is that ideally you shouldn't be pointing your DNS at ANY IP addresses when hosting on platforms such as Heroku, but instead CNAMEing everything so that Heroku can handle it when they need to.

(Note, it's worth adding that this is probably a common concept for most cloud hosting,  the core differentiator being whether or not they expose your application via a CNAMEable domain)
