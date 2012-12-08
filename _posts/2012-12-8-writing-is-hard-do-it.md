---
layout: post
title: Writing is hard, so do it.
---

Over the last few months I've been working on something that I wouldn't have seen myself doing at any point in my life, and that's the task of writing a book.  I'm not talking about a 'Janet & John' novel, or some sort of 'Fifty Shades of Gray' sort of affair, but a proper printed dead tree, trip over it kinda technical book.

It all started back in May/June time when I was approached by a good friend at [Heroku](http://www.heroku.com), who had been approached in turn by [O'Reilly](http://www.oreilly.com), to produce a book about the platform and some rough ideas for what sort of book might be relevant and saleable.  At the end of a fairly lengthy process it ended up that I was the author, had a contract in my hand and a schedule to try and stick to.

Shit.

## Getting started

The first problem I found when starting out on this project was the getting started itself.  A technical book is such a strange beast - there's no real start to a subject such as Heroku, nor any end, so how do you approach it?  If I were writing some sort of grotty novel then there's a clear approach with introducing characters, developing them and then climaxing with a, er, climax - so how do I approach this with a technical book?

At this point I should probably talk about targeting.  If I were writing a book about a subject such as Ruby, I would be starting in the same way as any other programming book would. A spot of "Hello World" and "Fizzbuzz",  moving on in a  very predictable fashion.  However, these books by people who have decided 'Hey!  I want to learn Ruby!'.

That's not so much the case with a book about a product, which is essentially what Heroku is.  People buying this book are more likely to have used the Heroku stack before, and deployed some stuff to it, and liked it so much that they are now willing to spunk $20 on a book about it in order to learn more…

So, where to start?

Well, with this sort of book you end up with a massive list of excerpts that you think should be in.  You want to talk about X, Y and Z, but with a bit of A, B and C thrown in for good measure.  Only by dumping everything on paper are you able to get down to the business of making sense of it all and the writing itself.  There are so may corners to a complex topic such as this that structure is the only place to start thinking about what to write and how.  Only by having the skeleton of what you are writing can you move forwards - much like writing code.

## Toolchains

So, what do I write with?  Pen and paper? Some sort of ye olde clicky clack typewriter and a pipe, so I can discard pages like that [guy at the end of the A-Team](http://youtu.be/4ohE2e7le-Y)?. Or Vim, which I use each and every day to write code?  Actually, I settled for [IA Writer](http://www.iawriter.com/) - a no-nonsense, zero fluff text editor that lets you think purely about the content at hand.  

No crap, no formatting, no flashy bits - just words.

Which at this point leads me onto formatting and structure, which all books need to make any sense.  In the print industry, and especially O'Reilly, books are generally written in some sort of internal toolchain supported markup.  This means that a publisher can drop a load of markup into one end of a pipe and get a formatted printed book / PDF / EPub / Mobi book out the other end.  This obviously saves some significant effort on one side but can introduce some pain for the author at the other end.

Therefore, you're limited to writing in a certain way.  Markdown (which I prefer, and what this very article is written in) is not generally supported as it's not as feature complete as you need for most of the conventions in a full-blown book.  This leaves you with a couple of common options:- [Asciidoc](http://www.methods.co.nz/asciidoc/), a sort of [Markdown](http://daringfireball.net/projects/markdown/) on steroids, and [Docbook](http://www.docbook.org/), writing in pure XML.

Obviously I went with Asciidoc.

Pushing this all into Git let's me push and publish out a new PDF to see how my text looks, and means that my editor can get down to looking at content as it arrives.  Without the familiarity of Git, I'd be lost.

### Changes

So, we were writing, and chapters were starting to take shape and things were going well.

Then I broke my leg.  Well, someone broke it for me by knocking me off my motorcycle at 60mph, but there you go.  Shit happens.

Now, with a major injury and X-Rays leaving you looking like Wolverine, it's gets kinda hard to focus on the concept of writing, and as such I had to take a three month break from writing the book.  

This is where bringing in a co-author (Richard Schneeman from Heroku) is a massive boon, and frankly, a stroke of genius by whoever suggested it.  You have two pairs of eyes checking over the content, two people writing the content chapter by chapter and two pairs of ears to listen to the feedback that people give you.  

Having a co-author is, in my eyes, essential when writing a technical book.

### But why write a book, it sounds hard?

First off, you should never write a book for the money.  I've no idea how much money I might make from this work, or even how many copies I might sell, but I do know that even with a thousand copies sold the per rate hour is appalling.  

For me, the reason to write is a simple one.  

It's a challenge.  

It's a challenge to whip up fifty thousand words about a topic that you think you know inside out, but it's more of a challenge to write a chunk of that on content that previously you never knew, nor cared that much about.  

Every topic has corners that people will avoid, either because they don't need to know it, or they don't want to know it.  Writing a book enforces you to not only look at these things, but also look at them enough that you're able to write well about them, and pass on your new-found knowledge.  What's more, you also end up having to hunt out the bits that you haven't mentioned and make sure you've covered everything that you need to cover.  

You may start a project thinking that you're proficient in any given subject, but I'll tell you now, writing a book about it will make you feel stupid in comparison with how much knowledge you'll need to cover off in order to produce a topic that people will be willing to spend real money on.

### So, get writing

The act of writing itself is not hard.

Writing is easy, anyone can do it.  Anyone is able to impart information to the page, but the key comes from being able to write and edit in a way that makes sense and this is where the challenge comes.  You will constantly improve with practise but only by pushing yourself into areas that you feel stretched.  If I were to compare my writing with that of a year ago I would say it's night and day.  

Whether other people agree with me is a different matter.

So, as a developer, I will strongly recommend that writing is something that you need to do.  I'm not saying go write a book, I'm saying write anything, anything at all.  If you don't have a personal site, set one up, and start dumping in content that you feel you know about and others will find interesting and learn from.  

Once you've done this, do it more and more. 

Eventually you'll get to the point where the writing comes easy, but more importantly, you're learning as much as your readers are.

Developers need to learn constantly.  If you fail to keep learning you will be left behind quicker than in any other industry.  By writing you are learning, but also helping others to learn things that they don't already know.

Everyone benefits from writing, but most of all the author.  So do it.  

Write something - it's challenging, but rewarding.


*Incidentally, if you're interested in reading the end product, you can pre-order it now on [Amazon](http://www.amazon.co.uk/gp/product/144934139X/ref=as_li_tf_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=144934139X&linkCode=as2&tag=neilmidd-21)*
