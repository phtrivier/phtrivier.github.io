---
layout: post
title:  "Elm and the \"tail end\""
date:   2016-08-21 16:14:17 +0200
tags: elm waitbutwhy
---
## Wait But Why's "tail end" vizualisation

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

The application should :

* ask you for your age
* ask you how long you expect to live
* ask you the kind of life event you want to vizualize
* show an image like the one above.

I'll build it with [Elm](http://www.elm-lang.org), a front-end application language[^2] that has the most polite compiler ever.

## Setting up an Elm application

### Downloading the Interwebs

> "Insert amusing quote about foreplay." _(Author Unknown)_

Installing anything is boring and often tedious, installing `elm`, is.. meww.

Assuming you have `nodejs` installed on your system, and you can
install `npm` modules locally without saying
['sudo'](https://docs.npmjs.com/getting-started/fixing-npm-permissions),
'please' or 'goddammit' :

    > npm install -g elm

`elm` by iself is 'just' a compiler that takes Elm source code
and generate a static JS file.  To make a full application, you'll
need some boilerplate code, and some tools that all the cool kids are
using those days (like grunt or gulp) to make the developpement
slightly easier.)[^3]

However, I'm going to stay minimalistic and see if I can gather enough information
from the official documentation and all to build a simple, single page app from
scratch. Cause it will be a bit more fun.

### Creating a project

Naively following the official [documentation](http://elm-lang.org/get-started) :

    > mkdir tailend
    > cd tailend
    > elm-package install

    Some new packages are needed. Here is the upgrade plan.

      Install:
        elm-lang/core 4.0.5
        elm-lang/html 1.1.0
        elm-lang/virtual-dom 1.1.0

    Do you approve of this plan? [Y/n] Y
    Starting downloads...

       ● elm-lang/html 1.1.0
       ● elm-lang/virtual-dom 1.1.0
       ● elm-lang/core 4.0.5

    Packages configured successfully!

Did I mention how *polite* elm is ? I have no idea what this upgrade
plan does, but I'm still asked if I'm okay ;) [^4].

You end up with an `elm-stuff` folder, full of, ahem, "elm stuff", I
guess ?  And an `elm-package.json` file that looks like a
glorified `package.json`.

```
{
    "version": "1.0.0",
    "summary": "helpful summary of your project, less than 80 characters",
    "repository": "https://github.com/user/project.git",
    "license": "BSD3",
    "source-directories": [
        "."
    ],
    "exposed-modules": [],
    "dependencies": {
        "elm-lang/core": "4.0.5 <= v < 5.0.0",
        "elm-lang/html": "1.1.0 <= v < 2.0.0"
    },
    "elm-version": "0.17.1 <= v < 0.18.0"
}
```

#### Code for 'Hello World'

The official documentation then tells you about `elm-make`, and to try and type something like this :

    > elm-make Main.elm --output=main.html

And that's a bit sad, because at this point you normally have *no
idea* what you're going to write in your `Main.elm` file, or even *where*
to put it (and don't let people give you ideas.)

It turns out a "Hello World" in `elm` is not obvious to write, so that might be why the official documentation does not really put it.
In the spirit of keeping it simple, here is the mimimum I could come up with (if any Elm expert has a better version, drop me an [email](mailto:phtrivier@yahoo.fr).

* Create a separate source folder (to avoid messing files around :)

```
> mkdir -p src/elm
```

* As per [`elm-make`](https://github.com/elm-lang/elm-make), change the default source directory in `elm-packages.json` :

```
   ... other lines..
   "source-directories": [
        "src/elm"
    ],
   ...
```

* Create `src/elm/Tailend.elm` ; this will be the real meat of our application

{% highlight haskell %}
-- src/elm/Tailend.elm
module Tailend exposing (..)

import Html exposing (..)
import Html.Attributes exposing (..)

-- Numbers are running the world
type alias Model =
    Int

-- Our app does nothing but start
type Msg
    = Start

-- We start with a null model, and nothing to do next
init : ( Model, Cmd Msg )
init =
    ( 0, Cmd.none )

-- We react to all messages but leaving the world intact, and doing nothing. Talk about a zen application.
update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    ( model, Cmd.none )

-- The simplest possible polite view.
view : Model -> Html Msg
view model =
    div []
        [ text "Hello world" ]

{% endhighlight %}

We'll come back to this file in further parts.

* Create `src/elm/Main.elm` ; this defines the main loop. We'll come back to it.

{% highlight haskell %}
-- src/elm/Main.elm
module Main exposing (..)

import Tailend exposing (init, update, view)
import Html.App as Html

main : Program Never
main =
    Html.program
        { init = init
        , update = update
        , view = view
        , subscriptions = \_ -> Sub.none
        }
{% endhighlight %}

Now you're ready to build something (from the root of the project)

    > elm-make src/elm/Main.elm --output=main.html
    Success! Compiled 2 modules.
    Successfully generated mail.html

If everything went okay, you should be able to open a newly generated `main.html` file in your favorite browser, and see a "Hello world".

If you've made a typo, (and I'm almost _hoping_ you've made a typo),
you should get one of the gorgeous error messages from `elm` :

```
○ → elm-make src/elm/Main.elm --output=main.html
-- NAMING ERROR ------------------------------------------ ./src/elm/Tailend.elm

Cannot find variable `Cmd.None`.

9| init = ( 0, Cmd.None)
               ^^^^^^^^
`Cmd` does not expose `None`. Maybe you want one of the following?

    Cmd.none

```

*YES*, I wanted `Cmd.none`. This is where Javascript would have told you that "undefined is not a function".
When you run the program for the first time, of course.

If you have any other error... well, it depends :/. Hopefully you can get help on the interwebs.

### Generating Elm vs HTML

Let's look at the generated main.html file :

``` html
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Main</title>
    <style>html,head,body { padding:0; margin:0; }
body { font-family: calibri, helvetica, arial, sans-serif; }</style>
    <script type="text/javascript">
      // .... LOOOOOOOTS OF GENERATED JS CODE
    </script>
  </head>
  <body>
    <script type="text/javascript">Elm.Main.fullscreen()</script>
  </body>
</html>

```

It has a number of issues :

- the title is hardcoded
- the style is embedded
- it has *tons* of generated js code
- there is a single line that has _some_ connection to our code : `Elm.Main.fullscreen()`

It turns out this line is needed because Elm can either take the full
control of the page (rendering everything) or be embedded inside a
bigger app.

We'll leave it as such, but instead of generating the page everytime, we'll provide it ourselve, and only generate a single file.
So we will end up with :

* a dynamic `tailend.js` file that `elm` generate from `Main.elm`, every time we build
* a static index.html file that we write, and loads this `tailend.js`
* static styles, resources, etc...

# Static index page and generated js file

Let's create the index page ourselves :

``` html
<!-- index.html -->
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="UTF-8">
    <title>The Tail End</title>
    <style>html,head,body { padding:0; margin:0; }
body { font-family: calibri, helvetica, arial, sans-serif; }</style>
    <script type="text/javascript" src="tailend.js"></script>
  </head>
  <body>
    <script type="text/javascript">Elm.Main.fullscreen()</script>
  </body>
</html>

```

Now build JS instead of building html :

    > elm-make src/elm/Main.elm --output=tailend.js
    > Success! Compiled 1 module.
    > Successfully generated tailend.js

Of course, you don't have a `main.html` file anymore (or, you can delete it if it's still around), but if you open `index.html`, things
should be fine.[^5]

### How about build tools ?

Front-end developers _loooooove_ build tools. In 2015, if you blinked
for 2 seconds, a new js build tool appeared, and you had to rewrite
your build system.
I'm not going to bother with that for the moment.

The only thing I'd miss at the moment is a 'watch' feature (something
that would recompile your main `tailend.js` file every time you change an `*.elm` file.

I'm not going to bother with that for the moment.

Go ahead and google `gulp`, `grunt`, `webpack`, `watch`, `reload` or whatever.

### Elm-reactor

There is an `elm-reactor` tool that compiles `elm` classes on the fly, it seems.
I'm not sure exactly how to use it in a simple case (maybe later on ?)

### Git and co

If you want to save your code as a git repository, you probably want to ignore some files :

```
# .gitignore
elm-stuff
tailend.js
```

If you want to follow, I'll leave a repository with the code on [github](http://github.com/phtrivier/tailend)

## Next bat-time

Next time, we'll actually try and display a "tail end" diagram, using nothing but two integers as a Model !

Stay tuned.

---

<br/>

[^1]: _Although, sadly, it's a bit debatable whether the blog is
    actually being *written* nowadays, because the author is a TED-certified master
    [procrastinator](http://waitbutwhy.com/2016/05/post-progress-meter.html)._

[^2]: _This is the moment I would normally amaze you with an explanation
    of how Elm is an ML-derivative language with a syntax akin to
    Haskell, and a runtime based on concepts of Functionnal Reactive
    Programming, and that's exactly when you would leave to watch
    reruns of Friends out of boredom, so I won't do that._

[^3]: _Creating such boilerplate from scratch is not too complicated,
    and covered [here](https://auth0.com/blog/creating-your-first-elm-app-part-1/) for the audacious._

[^4]: _Just for fun, I tried answering 'N'. It simply says 'Okay, I
    did not change anything!'. I'm pretty sure `nodejs` would have
    downloaded half the internet anyway, *before* displaying an
    annoying error message - because the developer needed to
    `require("insultuser")`._

[^5]: _If by 'fine' you understand having a 'tailend.js' that is 189k big (or 41k gzipped), all to create a single div - which would make [cmuratori](https://handmade.network) cry._
