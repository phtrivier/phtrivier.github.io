---
layout: post
title:  "Elm and the \"tail end\" - Part 4 - Better error handling (hopefully)"
date:   2017-02-04 10:00:00 +0000
tags: elm waitbutwhy
---
## Improving error handling

_This is part 4 of a series. You should probably start at [the first part](/2016/08/21/elm-tail-end-part-1)._

Okey, so last time we added some validations, but we had two issues at least :

 * the weird `maybeToString` function
 * `Maybe`s all over the place felt weird
 * The rule "expectancy should be bigger than current age" was not validated.

### Not reinventing the wheel

Turns out `maybeToString` can at least be done shorter by composing `Maybe.withDefault` and `Maybe.map`

``` haskell
maybeToString : Maybe Int -> String
maybeToString x =
    x
        |> Maybe.map toString
        |> Maybe.withDefault ""
```

I'll leave it as a separate function, because, well, it's small enough.
And it turns out I might actually *not* need it.

### A 'Period' vs 'two strings in fields'

My main problem is that I want to keep track of a period, but, let's
face it : most of the time, I don't *really* have a period.
So let's make peace with that, and just write :

``` abap
type alias Model =
    { age : String
    , expected : String
    }


type alias Period =
    { age : Int
    , expected : Int
    }
```

*Impresive*, isn't it ?

And I will add a function that converts those two strings to a period, *or* to an error type that indicates what is the problem.

``` haskell
type PeriodError
    = InvalidValues
    | InvalidAge
    | InvalidExpected
    | InvalidRange
```

Yes, I guess that covers all possible cases.

``` haskell
toPeriod : Model -> Result PeriodError Period
toPeriod model =
    let
        age =
            model.age
                |> String.toInt

        expected =
            model.expected
                |> String.toInt
    in
        case ( age, expected ) of
            ( Err _, Err _ ) ->
                Err InvalidValues

            ( Err _, _ ) ->
                Err InvalidAge

            ( _, Err _ ) ->
                Err InvalidExpected

            ( Ok age, Ok expected ) ->
                if age <= expected then
                    Ok { age = age, expected = expected }
                else
                    Err InvalidRange
```

This boringly parses stuff, and returns an error.
This is also a natural place to check the ages.

From then on, everything just rolls on.

``` haskell
-- Yes, the model is now only strings
initialModel : Model
initialModel =
    { age = "35"
    , expected = "90"
    }

-- Actions are still the same
type Msg
    = Start
    | ChangeAge String
    | ChangeExpected String


init : ( Model, Cmd Msg )
init =
    ( initialModel
    , Cmd.none
    )


-- Actions simply udate the models
update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    case action of
        Start ->
            ( model, Cmd.none )

        ChangeAge a ->
            ( { model | age = a }, Cmd.none )

        ChangeExpected e ->
            ( { model | expected = e }, Cmd.none )

```

The views are hardly changed, except that we don't need to coerce back into a String, since we hold... a String.

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
            , value model.age
            , onInput ChangeAge
            ]
            []
        , input
            [ placeholder "Expected"
            , value model.expected
            , onInput ChangeExpected
            ]
            []
        ]
```

And displaying the tailend becomes trivial.

``` haskell
tailendView : Model -> Html Msg
tailendView model =
    let
        period =
            toPeriod model
    in
        case period of
            Err e ->
                periodError e

            Ok p ->
                periodView p

```

With either a nice view :

``` haskell
periodView : Period -> HtmlMsg
periodView p =
    let
        crossed =
            p.age

        uncrossed =
            p.expected - p.age
    in
        div [ class "tailend-view" ]
            ((List.repeat crossed crossedItem) ++ (List.repeat uncrossed uncrossedItem))

```

Or an error message :

``` haskell
periodError : PeriodError -> Html Msg
periodError error =
    let
        message =
            case error of
                InvalidValues ->
                    "Please type an age and expected age"

                InvalidAge ->
                    "Please type an age"

                InvalidExpected ->
                    "Please type an expected age"

                InvalidRange ->
                    "Your expected age should be bigger than your age"
    in
        div [] [ text message ]

```

So, roughly speaking, things work.
But, there is a 'but' :

* everything is in the same file. I'm not sure how to refactor this.
For example, ideally I would like to move the 'inputs' function, but it needs to 'Msg' type.
* Better yet, I would like to hide everything related to the period picker in a module. But it seems like creating a 'Period Picker' component, and evan tells me [not to do that !](https://guide.elm-lang.org/reuse/)

So that's the next thing to look into, probably...

As usual, the code is on [Github](https://github.com/phtrivier/tailend/tree/part4)
