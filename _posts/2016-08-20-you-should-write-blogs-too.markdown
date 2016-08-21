---
layout: post
title:  "You should write blogs, too..."
date:   2016-08-20 10:10:10 +0200
categories: hello
---
Or, at least, Steve Yegge <a href="https://sites.google.com/site/steveyegge2/you-should-write-blogs">said so</a> - before completely stopping to write blogs a couple months (years ?) ago (probably after the infamous <a href="http://code.danyork.com/2011/11/11/the-google-vs-amazon-platform-rant-a-must-read-from-steve-yegge/">"Amazon rant"</a> incident.

So here I am.

I used to have a stupid blog (in french) about the news called <a href="https://lasemainequi.wordpress.com">La semaine qui...</a>. For about three years, I wrote one article a week about the past events (usually during the week-end, with a varying publishing schedule.)

I stopped in October 2015, because :

<ol>
<li>reading the news is <a href="https://www.theguardian.com/media/2013/apr/12/news-is-bad-rolf-dobelli">bad for your health</a>.</li>
<li>reading about French news is <em>really</em> depressing.</li>
<li>making jokes about depressing stuff is what <em>real</em> comedians do (before they start visiting rehab centers), and I'm not a <em>real</em> comedian (I just play one on stage, in between my day-job as a Software Engineer.)</li>
<li>I yet had to write an hour of sketches to even consider myself an <em>unreal</em> comedian, so I needed the time.</li>
</ol>

So this blog will be about anything <em>but</em> the news.

Most probably, software and tech. (Did I mention I'm a <a href="https://github.com/phtrivier">software engineer</a> ?)

I've been missing "learning a new language every 6 months".

Hopefully, one of the next topic will be <a href="http://elm-lang.org">Elm</a>, the language with the most polite compiler :

{% highlight haskell %}
--- This is purely teasing
module Tailend exposing (..)

import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)
import String exposing (toInt)

-- MODEL

type ValidationError
    = NoAge
    | InvalidAge
    | NoExpected
    | InvalidExpected
    | InvalidRange

--- Did I mention I'm just teasing ;) ?
--- That, and testing Jekyll (poor) support for syntax highlighting.
{% endhighlight %}

<em>(Or maybe I'll just <del>waste</del> spend a few hours figuring how to get the syntax highlighter to work as I want.)</em>

Now go out, and write your own blog, too.

Steve said so.
