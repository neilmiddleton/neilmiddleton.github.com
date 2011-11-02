---
layout: post
title: Case Insensitive searching with PostgreSQL
---
#Case Insensitive searching with PostgreSQL

Earlier today (well, a few minutes ago), I had a requirement to make an
existing LIKE search case-insensitive with PostgreSQL.

Normally this would prove to be messy, by trying dirty tricks such as
downcase-ing content in the search (using UPPER and LOWER) - however,
it’s pretty straightforward.

For instance, the case-sensitive code would be something like:

{% highlight ruby %}
SELECT * FROM sometable WHERE textfield LIKE '%value%';
{% endhighlight %}

Whereas, to make it case-insensitive, try a bit of Borat:

{% highlight ruby %}
SELECT * FROM sometable WHERE textfield ILIKE '%value%';
{% endhighlight %}

Job done.
