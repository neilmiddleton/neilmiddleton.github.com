---
layout: post
title: Read-only filesystems are a good thing
category: Heroku
---
#Read-only filesystems are a good thing

Something that seems to pop up lot from users of Heroku are comments
about the read-only filesystem and the problems it then causes them.
Judging by some of the [Stackoverflow](http://stackoverflow.com/questions/tagged/heroku) questions [I answer](http://stackoverflow.com/users/4787/neil-middleton) there is a
generally held belief that the read-only filesystem is a really bad
architectural decision.

Well, that's just not cricket.

First, some background.

In the world of one server the use case is pretty simple.  An
application wants to create a file, and so it generates it and writes it
to the local filesystem.  This file is then ready to be served up later
on if so needed.

But then traffic starts to climb, and you need two servers.  Your
application wants to create a file and so it generates it and writes it
to the local filesystem.  However, the other server cannot see this file
as it's not on it's own filesystem, so you need to start thinking about
how to share files between servers.  This leads you to some sort of
network attached storage.

A few months later things are going well and you have 500 servers, all receiving files and
writing them, but because you're now running against a non-local
filesystem you've not got a problem.

Now think about Heroku, and think of a web process (or dyno) as a
server.  One dyno on its own could potentially run on the local
filesystem, but once you scale you have the exact same problem that's
described above.  However, in the case of Heroku your attached storage
is generally going to be S3, a file storage system that works better and
at a much larger scale than anything Heroku could offer in the short
term.  Therefore S3 is the recommended place to dump files.

However, this isn't the only option.  You can still use the filesystem -
to a point:

On the Aspen and Bamboo stacks, you can write to `./tmp` and `./log`,
whereas on Cedar you can write pretty much anywhere.  However, it's
worth remembering that should you require these files on a subsequent
request, there's absolutely no guarantee they'll be there.  Your dyno
might have moved or been restarted, thus reverting it back to its slug
state.

More information can be found here: [http://devcenter.heroku.com/articles/read-only-filesystem](http://devcenter.heroku.com/articles/read-only-filesystem)

At the end of the day, the read-only filesystem is something thats
required in todays modern application architecture.  Writing to local
filesystems for shared files just isn't something that fits in the
modern scaling world.

As [Adam from Heroku](http://adam.heroku.com/past/2008/7/2/readonly_source_trees/) puts it:

> As we continue to explore the next generation of application deployment, I think we’re going to bump into a number of ways to structure apps differently in order to make them scalable. There will be some transitionary pain with these changes, because structure implies restrictions. Many PHP developers coming to Rails have complained about not being able to access sessions from models, or write SQL in your view. MVC creates restrictions, yes, but those very restrictions are what provides the structure. Coming from an unstructured environment, those restrictions may seem cumbersome or arbitrary; but once you’re in the habit, you come to appreciate the structure they create.

