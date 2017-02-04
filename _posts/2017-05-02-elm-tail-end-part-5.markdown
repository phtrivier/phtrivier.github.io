---
layout: post
title:  "Elm and the \"tail end\" - Part 5 - Refactoring (kinda)"
date:   2017-02-05 01:00:00 +0000
tags: elm waitbutwhy
---
## Refactoring the Period picker

_This is part 5 of a series. You should probably start at [the first part](/2016/08/21/elm-tail-end-part-1)._

After a few hours of intensive rubberducking with myself, I might have a kinda proper solution to the refactoring bit.
The two key insights where :

* you can leave state inside a module, exposing it as a type, *without* exposing its internals
* a `view` function can take good old `callbacks` (or `handlers`, or however you want to call it) to be able to return arbitrary types of `Msg`.

### A module with hidden state

In a new `Period.elm` file, I define an union type with a single constructor.
And a function to create a value of such a type.
The idea is that the calling code should *not* know how the state is maintained.

``` haskell
type State
    = State
        { age : String
        , expected : String
        }


initState : Int -> Int -> Maybe
initState age expected =
    State { age = age, expected = expected }
```

I add the definitions of a Period alias type, the conversion code from a State to a Period, and the error codes.

``` haskell

type alias Period =
    { age : Int
    , expected : Int
    }


type PeriodError
    = InvalidValues
    | InvalidAge
    | InvalidExpected
    | InvalidRange

stateToPeriod : State -> Result PeriodError Period
-- ...
```

Here comes the tricky part. Back into my `Tailend.elm` page, I will now want to do something like this :

```haskell
type alias Model =
    { periodState : Period.State }


initialModel : Model
initialModel =
    { periodState = Period.initState "35" "90"
    }

```

Yes : the only thing I keep around is an opaque State. Once it has been created, I don't know how it works.

What are the actions in my application now ? Well, I would like the `Period` component to report me when the state change,
so I just have... a single Msg type.

``` haskell
type Msg
    = Start
    | SetPeriodState Period.State

init : ( Model, Cmd Msg )
init =
    ( initialModel
    , Cmd.none
    )

update : Msg -> Model -> ( Model, Cmd Msg )
update action model =
    case action of
        Start ->
            ( model, Cmd.none )

        SetPeriodState s ->
            ( { model | periodState = s }, Cmd.none )

```

Now to the real fun. Intuitively, I would like to write view code like this :

``` haskell
-- THIS WON'T WORK!!
view : Model -> Html Msg
view model =
        div [ class "page" ]
            [ Period.view model.periodState
            , tailendView model
            ]
```

So the `view` code would look like :

``` haskell
-- WONT WORK
view : State -> Html Msg
view state =
    div []
        [ input
            [ placeholder "Age"
            , value age
            , onInput ???
            ]
            []
        , input
            [ placeholder "Expected"
            , value expected
            , onInput ???
            ]
            []
        ]
```

Two problems are obvious here :

- `Msg` won't be defined, so it won't compile
- What exactly should happen when the value is changed ?

To get some help, let us look again at the type of `onInput` from `Html.Events`

``` haskell
onInput : (String -> msg) -> Attribute msg
```

Of course, onInput can not know in general what kind of Msg must be generated.
So it is passed a function, that has a 'generic' return type, and generates events from this type.

It just turns out the the various Msg (ChangeAge, ChangeExpected, etc..) that we have used so far where functions (constant functions.)

And it turns out we can do the same with the `view` function itself. "Pass me a function that returns a Message, I'll call it when my State changes."
Sounds lots like callbacks, to me, so I wrote it with a kind-of-callback-y API from the 'client side'

``` haskell
-- in Tailend.elm
view : Model -> Html Msg
view model =
    let
        onPeriodStateChange =
            SetPeriodState
    in
        div [ class "page" ]
            [ Period.view onPeriodStateChange model.periodState
            , tailendView model
            ]
```

And now, `view` just has to be able to call the `callback` when the state actually changes.

``` haskell
-- NOTICE THAT msg is a generic type. So anything can be sent ; it's the callback that decides.
view : (State -> msg) -> State -> Html msg
view onChange ((State { age, expected }) as state) =
    div []
        [ input
            [ placeholder "Age"
            , value age
            , onInput (changeAge onChange state)
            ]
            []
        , input
            [ placeholder "Expected"
            , value expected
            , onInput (changeExp onChange state)
            ]
            []
        ]
```

Here, I am using a destructuring trick for union types with a single constructor, to access the age / expected vaues from the state.
(Remember, they are all String. This is internal to the Period module, so we're safe.)

The final touch is the `changeXxx` functions, that act as 'glues'. The API of `onInput` constraints their type, and we decide that the
contract of the `view` function is that its first argument (`onChange`) will be called with the new state, after a change.
So we compute that, and call the callback.

``` haskell
changeAge : (State -> msg) -> State -> String -> msg
changeAge toMsg (State { age, expected }) str =
    let
        state =
            State { age = str, expected = expected }
    in
        toMsg state


changeExp : (State -> msg) -> State -> String -> msg
changeExp toMsg (State { age, expected }) str =
    let
        state =
            State { age = age, expected = str }
    in
        toMsg state
```

To my earnest surprise, at this point (baring some `import` magic), things work.

See the code [here](https://github.com/phtrivier/tailend/tree/factor-view)

And I think I know how I would add other things (like, and image selector, or things like that.)

Which would be the next step (since it could involve overlays, or stuff...)
