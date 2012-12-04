---
layout: post
title: Describing time
---

One of the things that developers get all excited about when using tools
such as Rails is some of the cleverer helper functions that the
framework defines.

One of the ones that tickled me the most was some of the date/time
functions: `time_ago_in_words`, `distance_of_time_in_words_to_now` and
so on.  These functions were quite literally the shit as far as I was
concerned.  I now had the ability to rock out a very Web 2.0-y interface that
included timestamps such as '12 days ago' or 'six hours ago' without
really thinking about it too much.

My interfaces became cool.

However, these days I'm older and considerably more miserable.
Therefore, I don't get excited nearly as much as I used to, which is
where this post now descends into a rant.

For example, today I wanted to fill out a timesheet for a few weeks
back, so cracked open Pivotal Tracker\* to look at the project history to
see what I was working on that day.  However, all the entries in this
history were marked out in the 'cool' way of '18 days ago' and so on -
not useful at all.  I needed to know what *date* these things occurred
on, now how long ago they were.

So, next time you're designing your application interface have a think
about how people will be using it.  It might be nice to not confound
users with a timestamp for things that only happened a short time ago,
but don't put out messages such as '246 days ago!' when a timestamp can
be so much more useful.

\* Note here I'm not picking on Pivotal Tracker here, it's just the first
one I came across.  Lots of apps do this.
