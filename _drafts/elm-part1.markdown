---
layout: post
title:  "Elm and tail end"
date:   2016-08-21 16:14:17 +0200
tags: elm waitbutwhy
---
## Wait But Why's "tail-end" vizualisation

[Wait But Why](http://waitbutwhy.com) is probably one of the greatest
blogs currently written [^1].  It is funny, it deals with a wide range
of topics, and the posts are often long and greatly documented, so
*PLEASE DON'T FOLLOW THE LINK RIGHT NOW* - or you'll start reading the
Tesla/Musk series, and you'll never come back to read my own blog, and
I'll be sad.

For the purpose of this blog, though, I need you read to read *one* post : [The tail end](http://waitbutwhy.com/2015/12/the-tail-end.html).

Go read it.

Done ?

Fine. If you are now a bit sad, and wish to call your parents / siblings /
long friends instead of reading this, go ahead, I won't judge.

Back ?

Okay, so the article has a bunch of images dramatically displaying how
little time you still have on earth, and how much we should all enjoy
our parents (if possible), our tacos (if edible), and our Superbowls
(if Americans.)

![Superbowls image from waitbutwhy.com. Reproduced in good faith.](http://28oa9i1t08037ue3m1l0i861.wpengine.netdna-cdn.com/wp-content/uploads/2015/12/Superbowls-600x563.png
"How many Superbowls left ?")

This series of blog post will be about creating a single-page application to generate such morbid graphs for you enjoyment.

I'll build it with [Elm](http://www.elm-lang.org), a front-end application language[^2] that has the most polite compiler ever.

## Setting up Elm


{% highlight haskell %}
-- Pouet
{% endhighlight %}

---

[^1]: _Although, sadly, it's a bit debatable whether the blog is
    actually being *written* nowadays, because the writer is a master
    [procrastinator](http://waitbutwhy.com/2016/05/post-progress-meter.html)_

[^2]: _This is the moment I would normally amaze you with an explanation
    of how Elm is an ML-derivative language with a syntax akin to
    Haskell, and a runtime based on concepts of Functionnal Reactive
    Programming, and that's exactly when you would leave to watch
    reruns of Friends out of boredom, so I won't do that._
