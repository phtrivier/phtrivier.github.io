---
layout: post
title:  "Elm and the \"tail end\" - Part 3 - Handling input (badly)"
date:   2016-10-21 21:00:00 +0000
tags: elm waitbutwhy
---
## Making the page interactive (or at least, trying)

_This is part 3 of a series. You should probably start at [the first part](/2016/08/21/elm-tail-end-part-1)._

Our application has been pretty static so far, we should add some interactivity.
We'll add a way to change your current age, and your life expectancy.

Let's ask the questions from last part again.

### _What is this application about ?_

The same as earlier (just an age and an expectancy.) So the `Model` does not have to change.

_Or so you think..._


### _What can this application do ?_

We can know change the age and expectancy, so let's add two messages for that.
The messages will be 'parameterized' with the new values.

Here we go :

``` haskell
type Msg
    = Start
    | ChangeAge Int -- The Int will be the new age (no pun intended)
    | ChangeExpected Int -- The Int will be the new age expectancy
```

### _How does this application start ?_

The same as usual.

### _Where do we go from here ?_

When we receive a message to change any value, we turn the model into... the same model with a different value.

``` haskell
update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    case action of
        Start ->
            ( model, Cmd.none )

        ChangeAge age ->
            ( { model | model.age = age }, Cmd.none )

        ChangeExpected expected ->
            ( { model | model.expected = expected }, Cmd.none )

```

### _Wait, aren't we suppose to display stuff, too ?_

Sure, we can display a couple inputs as well as our view.
In its simplest form :

``` haskell
view : Model -> Html Msg
view model =
    div [ class "page" ]
        [ inputs model
        , tailendView model
        ]


inputs : Model -> Html Msg
inputs model =
    div []
        [ input
            [ placeholder "Age"
            , value (toString model.age)
            ]
            []
        , input
            [ placeholder "Expected"
            , value (toString model.expected)
            ]
            []
        ]
```

We had to use
[`toString`](http://package.elm-lang.org/packages/elm-lang/core/latest/Basics#toString)
because the input wants to display a `String`, and not an `Int`. (And if
you've been using Javascript for too long, you might have forgotten
it, but it's two different things. Anyway.)

So all this displays a nice set of input, and if you type something in them, pretty much *nothing* happens.

### Introducting events

Elm has a complete package devoted to handling events, named, ahem,
[Html.Events](http://package.elm-lang.org/packages/elm-lang/html/1.1.0/Html-Events). It
has *functions* that are meant to be used to generate attributes of
`Html` elements, in order to fill the usual 'callbacks'.

If we look at `onInput`, for example, we see the signature :

``` haskell
onInput : (String -> msg) -> Attribute msg
```

Which in our case, means we have to provide it with a function that will turn a `String` (what is typed by the user in the field), and generate a `Msg`.

We would use it like this :


``` haskell
inputs : Model -> Html Msg
inputs model =
    div []
        [ input
            [ placeholder "Age"
            , value (toString model.age)
            , onInput handleAgeInput
            ]
            []
           ...
```

Let's try different possible functions for how to handle age input:

``` haskell
handleAgeInput : String -> Msg
handleAgeInput str =
    ChangeAge str

```

That's the kind of things you see in tutorials, except in our cases, there is a catch : `ChangeAge` does not take a `String`, but an age.
So this won't compile. (But `elm` message will be nice.)

How about parsing the String ?

``` haskell
handleAgeInput : String -> Msg
handleAgeInput str =
    ChangeAge (toInt str)
```

Again, this won't compile, because
[`toInt`](http://package.elm-lang.org/packages/elm-lang/core/4.0.5/String#toInt)
will not return an `Int`, but a composite type called
[Result](http://package.elm-lang.org/packages/elm-lang/core/4.0.5/Result)
that *forces* you to deal with failures (in this case, an entry that
can not be parsed as an `Int`, because your user found it soooo smart to
type "foo" in the age field. And we're not going to play with the
[type=number](https://www.w3.org/TR/html-markup/input.number.html)
attribute, cause that would be cheating. And this _aparté_ is way too
long, again.)

Here, we have several ways of handling this, and _I'm not sure which one is the best_ (did I mention I write this mostly to educate *myself* ?)

#### Use a default value

That seems to be suggested
[here and there](http://package.elm-lang.org/packages/elm-lang/core/4.0.5/String#toInt)
; just assume that if the user types a love letter in the age field,
it means they're 15 years old. Or 0.

``` haskell
handleAgeInput : String -> Msg
handleAgeInput str =
    let
        age =
            Result.withDefault 0 (toInt str)
    in
        ChangeAge age
```

That will work, provided the view code gracefully handles the zeros.

#### Use a String for ChangeAge

What if `ChangeAge` used a `String` instead of an `Int` ?
`handleAgeInput` would be trivial to write :

``` haskell
type alias Msg
    = Start
    | ChangeAge String -- The string is simply what the user typed
    ...

handleAgeInput : String -> Msg
handleAgeInput str =
    ChangeAge str
```

So, all and dandy ? Well, no, because then, in the `update` code, you have to deal with the string conversion :

``` haskell
update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    case action of
        Start ->
            ( model, Cmd.none )

        ChangeAge age ->
            ( updateAge model age, Cmd.none )

        -- ...

updateAge : Model -> String -> Model
updateAge model age =
    case String.toInt age of
        Ok age ->
            { model | age = age }

        Err _ ->
            { model | ????? }

```

You might want to use `Result.withDefault` here, or just write `age =
0`, but it's really the same as the previous situation.

Both of those solutions have a problem in the `view` function.

First of all, the `view` function will not be able to display an
helpful error message in case of invalid display. (Something like "You
should have typed a number for your age.").  It could "guess" that
there is an error if the age is the default value (0), and maybe you
can write the messages so that if either 0 or a bad age is used.

Secondly, `view` will have to put something in the `value` attribute
of the Input. If you decide to type a default value, than any time you
enter something bogus, the field will be replaced by 0, which is kinda
strange.

#### Using Maybes

Since it's unclear whether the age is filled at a given moment, we might want to make it explicit, using the `Maybe` type.

It _kinda_ works, but I'm not that happy with the result.
In details, here is how it would go (I only show the `age` bits, `expected` is mostly the same.)

``` haskell
type alias Model =
    { age : Maybe Int
    , expected : Maybe Int
    }

type Msg
    = Start
    | ChangeAge String
    | ChangeExpected String


initialModel : Model
initialModel =
    { age = Just 35
    , expected = Just 90
    }

update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    case action of
        -- ...

        ChangeAge age ->
            ( updateAge model age, Cmd.none )

-- ...

updateAge : Model -> String -> Model
updateAge model str =
    let
        age =
            str
                |> String.toInt
                |> Result.toMaybe
    in
        { model | age = age }

-- ..

inputs : Model -> Html Msg
inputs model =
    div []
        [ input
            [ placeholder "Age"
            , value (maybeToString model.age)
            , onInput ChangeAge
            ]
            []
        , input
            [ placeholder "Expected"
            , value (maybeToString model.expected)
            , onInput ChangeExpected
            ]
            []
        ]


maybeToString : Maybe Int -> String
maybeToString x =
    case x of
        Just a ->
            toString a

        Nothing ->
            ""

-- ...

tailendView : Model -> Html Msg
tailendView model =
    case ( model.age, model.expected ) of
        -- Destructuring pattern matching for the win !
        ( Nothing, _ ) ->
            div [] [ text "You should type an age" ]

        ( _, Nothing ) ->
            div [] [ text "You should type an expected age" ]

        ( Just age, Just expected ) ->
            let
                crossed =
                    age

                uncrossed =
                    expected - age
            in
                -- ...


```

This works, but :

* the Maybe's are checked all the time
* I'm pretty sure there is a shorter way to do `maybeToString`
* this still does not prevent the user from typing an age that is
  bigger than her expectancy. And we should use the type system to
  ["make impossible states impossible"](https://www.youtube.com/watch?v=IcgmSRJHu_8&index=13&list=WL).

Remember when I told you that `elm` forces you to think about error
cases early one ?  We have _two_ freaking fields here, and we're
already wondering what to do.

On the bright side, the same code in Javascript would be littered with
`if`s for null checks, and possible places for conversions, so at
least the `elm` code is _clean_.  But it still feels a bit
complicated, so I'm sure there is a better way.

Which we'll give a try to find next time !
