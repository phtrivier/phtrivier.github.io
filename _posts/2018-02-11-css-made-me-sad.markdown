---
layout: post
title:  "CSS makes me sad (sometimes.)"
date:   2018-02-11 19:46:00 +0000
tags: css
---
#TL;DR

_CSS makes me sad (sometimes), but [Chris Coyier]() is the proof that nice human beings exist, and can also understand CSS._

#Enough about you, let's talk about me

I've been writing web applications for a while, now, and always got a bit bitten by CSS.

What is weird is that "being bitten" by a technology has happened to me before, will happen to me again, and is part of trying to do anything remotely technological.

However, CSS has always kind of _particular_ in that case. Notably, in
the sense that failing to do what I need in CSS has a tendency of
causing _feelings_ well before the usual anger.

Normally, when failing to do something with a (new or not) language / framework / technology / whatever, the train of though is :

* Okay, I've to do this, that should be easy.
* Hum, wait, does not work, why ?
* Hum, let's try that...
* What the... ?
* WHAT THE .... ?
* WAAAAAAAAAAAAAAT ?
* Ok, [c'est de la merde](www.cestdelamerde.com) _(Yes, I'm french, I curse in French. Ask Google Translate.)
* (Reading docs, reading forums, cursing, coffee, more cursing, more docs, more coffree)
* Oh, of course, I get it now
* Move on to something else

However, CSS has this big peculiarity that I never leave the WAT part.
(Or, actually, the "CDLM" part, but that's my problem)

Now, I get that if you want to have a red text, you use `color:
red`. That's not my problems. My problem is "I want this box to go
next to this box and have a scrollbar here when the content of the box
there is bigger than the height of this box that is almost as high as
the screen but not exactly because there is a fixed header and it has
to work in Firefox too."

After doing for a while, I _kinda_ managed to sidestep the problem,
mostly by letting other people with a better right-brain deal with
it. (Frantz, if you're reading this, Hi, and thanks for all the cakes
!)

#That fateful night

At some point, it was a thurdsay, maybe, I had spent what felt like a
whole day fighting a layout to make a line of HTML scrollable (so that
my UI support more than 2 items, never mind.)

After much bitching and moaning and feeling like a miserable failure,
I went back home. As any sane person would have done, I spent the
night browsing the web for a potential answer instead of, you know, *resting* and getting my sanity back.

Which brought me to sending a desperate 1AM message in the contact form of [CSS-Tricks](), the amazing blog of [Chris Coyier](), aka "The alternate life form who understands CSS."

Needless to say, the following day, after a good night sleep, I found
a way to "fix" my problem (which the strong gut feeling that my fix
would not work in the long run.).

SPOILER ALERT: it did *NOT* work in the long run, and I was back to searching for the proper spell to fix the same issue three weeks later.
Then I was about to bitch and moan on twitter, when I found *this*

# Practice

"I think it is a matter of practice": the natural gut-feeling is to disagree with that (of course I feel like I spent *waaay too much* time learning and practicing CSS already ;) ) .

But you're right on one thing: I definitely never found a way to systematically and efficiently train for CSS - the way I managed to train for all the other technos I know (not to brag, but I've had to learn a lot.)

CSS definitely feels like the one "outlier" here.

(I wonder if it's a shared feeling, I could not find data.)

Even Meyer's Book hardly helped (in the old days of "floating" madness...)

Most literature I find (even, as far as I can tell, most of the great guides your right) is tactical and focused on one use case.

How do you systematically carve a "feeling" for the general and unintuitive rules ? How to you train your intuition to forget about trying to center things by using things called "center" ? How do you "practice" layouting when hardly any tools give you hints about "I give this size to this box because of xxxx " ? When using "border: 1px solid red" to make things more visible can *actually* break your design ?

To sum up: where is the "learn css the hard way" guide ? (but, please, not as hard as reading a W3C spec ;) )

The first element of sadness: (probably "learned") helplessness.

# Expectations:

> "If it's any consolation, there are loads of developers out there who feel exactly the opposite. Designing, styling, and doing web layout are easy to them compared to architecting data."

I have met exactly *one* colleague in my career who seemed to have a slightly proper intuition of CSS positioning (we still struggled a lot, but at some point he would have a flash of intuition and imagine a terrible hack that sort-of worked.)

On a possibly related note, he was slightly crazy (in a good way !) and definitely more "right-brainish" than any other person in the room.

Strangely enough, I expect, and accept that "other" people will find it easier than me to *design* and *style* a page.
(I would never claim to be good at "creating" a design, in the UX sense of things, and that's fine.)
However, as a programmer, once my designer has decided to put "this things" on top of "that rows" of "boxes" - I expect to be able to *implement* it.

And for most designs, the really tricky things to implement are shamefully simple to describe. ("What do you mean it's going to take a week to have rounded corners ?" slowly turned into "What do you mean you had to spend four hours rewriting this page to put the box at the left ?", then "What do you mean I need the latest version of FF to get a scrollbacr", etc...)

I've been in full https://xkcd.com/1425/ mode too often, with not enough intuition to help.

"I have my doubts that CSS is inherently bad and poorly designed such that incredibly intelligent people can't handle it"

Here is the problem:

* I'm *not* an "incredibly intelligent" person (it hurts, but I'll live with that)
* I don't understand why I would have to be one to create a three column layout ! (You're probably astutely aware of it, but... remember when people called it the "holy grail" ? "Holy grail" !!! What does it say about a technology, when a clean way to implement the one result that 80% of user want, is as difficult to find as a mythical artifact that gives eternal life ?)

I can't shake the feeling that It should *not* be that hard.

(The feeling is strong enough to shut down my brain.)

Second element of sadness: Expectation != Reality. (https://waitbutwhy.com/2013/09/why-generation-y-yuppies-are-unhappy.html)

# It's getting better

> If you feel snakebitten by past CSS, it's time to try it again because it's gotten more capable and, dare I say, easier.

(Of course I've been snakebitten, I told you I've been doing CSS for 10 years ;) )

Yes, a couple of things are now much more reasonable to do.

Rounded corners have been working for a few years ; about precisely at the time where flat design became the fashion. #irony

Grids are becoming a reality (provided you don't want subgrids before people go to Mars.)

> Try flexbox!

I loved `flexbox`. Very much. Almost make it possible to (gasp), align nested boxes.

Until about three weeks ago ;)

Then one of my boxes became too big. Especially on mobile browsers.

And I wanted a ... scrollbar ...

Of course I played with `overflow` (for a few hours. Not the greatest fun.) I was pretty amazed at the way to style a scrollbar, given how hard it was to get one, and leave my layout sensible.

In short, I bumped into a number of variations of this: https://stackoverflow.com/questions/14962468/flexbox-and-vertical-scroll-in-a-full-height-app-using-newer-flexbox-api

As nothing worked and my work day was spoiled, I went back home, spent the night googling and venting on twitter, before sending a very sad email to an helpful expert in the field :D

None of what I tried was fixed until I subtly rewrote my layout and added a couple of "min-heights" in places where it did not make any intuitive sense.

I had zero confidence that the next time I would find such a problem, I would be able to understand it, and fix it.

The next time came yesterday :D

I was about to feel sad again, but then I saw your response on twitter, so, hey, I'm going to get a grip, that's just a css bug , etc...

So, I'm heading back to (you guessed it) trying to fix a scrollbar on a flexbox based layout.

Hopefully
Thanks again for taking the time to answer, have a great holiday seasons, Merry Christmas, and a Happy New Year.
