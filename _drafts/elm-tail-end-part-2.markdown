---
layout: post
title:  "Elm and the \"tail end\" - Part 2 - Displaying a 'tail end'"
date:   2016-08-21 16:14:17 +0200
tags: elm waitbutwhy
---
## Elm application life cyle

Let's do a first simpler version of our application, that simply displays a hardcoded failend image for a fixed setting.

The life of a Elm application is described better than I will do by its [creator](https://guide.elm-lang.org/architecture/).

The general idea is that, to make an application, you have to answer a few questions.

### _What is this application about ?_

That's your `Model`. The data of you application. It is just that. Data.

The scary and important thing is that all data of your application should live in
one data type.

Imagine that in Javascript, instead of having your data scattered around your application (some associated to DOM nodes, some in a top level object, some in global variables, etc...), you have one root object, and everything should use that.

`elm` has a nice type system, so the "kind" of data you'll manage is stricly defined.
(So stricly define that it will make you cringe, some times - more on that later.)

In our case, _what are we talking about ?_ We want do display a "tail end" graph, which means we have to give two information :

``` haskell
type alias Model =
    { age : Int -- How old are you ?
    , expected : Int -- How old do you expect to live (no jinxing implied)
    }
```

Note that I suppose you can call your `Model` type however you want. But let's not surprise everyone ?

### _What can this application do ?_

You capture all possible actions in a type `Msg` type. For our first version, things will be deceptively simple : the application can `Start`. And nothing else.

``` haskell
type Msg
    = Start
```

A real life application would have to model everything that can
happen, and `elm` forces you to be a bit explicit about events that
would normally be "implicit".

For example, there would be a `Msg` for user actions that make you
trigger an HTTP request ; and another `Msg` for when the HTTP response
comes *back* ; and *another* for when the HTTP response comes back with
an error.

The bad part is that you have to think about all cases early on.
The good news is that you think about all cases early on !

### _How does this application start ?_

This really ask two questions :

* what is the initial state of the application (that's answered by giving a value of the `Msg` type)
* should I do something right after starting ? (for example, go get a list of TODO items from an API, etc...)

In our case, the answers will be :

* I want to display the graph for a 35 yo person, who expects to live at least [^1] to 90
* There is nothing to do once the application is started.

This is all captured in the `init` function :

``` haskell
initialModel : Model
initialModel =
    { age = 35
    , expected = 90
    }


init : ( Model, Cmd Msg )
init =
    ( initialModel
    , Cmd.none
    )
```

### _Where do we go from here ?_

_(Mandatory [BTVS](https://www.youtube.com/watch?v=7XdAQpq_1Xw) reference. Sorry)_

Your application will then enter an infinite loop of going from a `Model`, to reacting to `Msg`, to a new version of the `Model`.
That's your moment to play God / Mother nature / Game Master and set up the rules for your world.
As an apprentice "All powerfull being", your first set of rules would be simple :

> "Let everything stay the same and never change. Time for a beer." (Lazy Creator, on a Friday.)

``` haskell
update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    ( model, Cmd.none )
```

### _Wait, aren't we suppose to display stuff, too ?_

Yes, because it's a Web application. You'll have to display

TODO(pht) adapt https://gitlab.com/phtrivier/longtail/blob/master/src/elm/Tailend.elm to
display something simple.

---

<br/>

[^1] Really, I mean it, *at least*. I don't want to jinx it for anyone. Live long and prosper, etc...
