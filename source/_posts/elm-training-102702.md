title: Elm練習11
date: 2016-10-27 11:19:40
tags:
- Elm
---

引き続きElm練習

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)


debugger(のように使える?)elm-time-travelを試してみる

Elmのバージョンはv0.17.1を使ってます

githubのページ
[elm-time-travel](https://github.com/jinjor/elm-time-travel)


<!-- more -->

<br>

---
# 使い方
---

### packageをインストールする

下記コマンドでpackageをインストールする

```
$ elm-package install jinjor/elm-time-travel -y
```

### Html.Appの代わりにimportする

elmのコードはbeginnerProgramまたはprogramを使用するために

Html.Appを先頭でimportしているが

その代わりに、`TimeTravel.Html.App`をimportしてやる

```elm
-- import Html.App as App
import TimeTravel.Html.App as TimeTravel
```

いくつかサンプルがある
http://jinjor.github.io/elm-time-travel/


例えば、公式の[field](http://elm-lang.org/examples/field)をTimeTravelで置き換えるとコードは下記のようになる

```elm FieldSample.elm
import Html exposing (Html, Attribute, text, div, input)

-- import Html.App exposing (beginnerProgram)  -- ここを置き換えてやる
import TimeTravel.Html.App exposing (beginnerProgram)

import Html.Attributes exposing (..)
import Html.Events exposing (onInput)
import String


main =
  beginnerProgram { model = "", view = view, update = update }


-- UPDATE

type Msg = NewContent String

update (NewContent content) oldContent =
  content


-- VIEW

view content =
  div []
    [ input [ placeholder "Text to reverse", onInput NewContent, myStyle ] []
    , div [ myStyle ] [ text (String.reverse content) ]
    ]

myStyle =
  style
    [ ("width", "100%")
    , ("height", "40px")
    , ("padding", "10px 0")
    , ("font-size", "2em")
    , ("text-align", "center")
    ]

```


また、もちろんApp.programにも適用できるのでexampleの[time](http://elm-lang.org/examples/time)のコードは以下のようにしてやればよい

```elm TimeSample.elm
import Html exposing (Html)

-- import Html.App as App -- ここを置き換える
import TimeTravel.Html.App as App

import Svg exposing (..)
import Svg.Attributes exposing (..)
import Time exposing (Time, second)



main =
  App.program
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    }



-- MODEL


type alias Model = Time


init : (Model, Cmd Msg)
init =
  (0, Cmd.none)



-- UPDATE


type Msg
  = Tick Time


update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Tick newTime ->
      (newTime, Cmd.none)



-- SUBSCRIPTIONS


subscriptions : Model -> Sub Msg
subscriptions model =
  Time.every second Tick



-- VIEW


view : Model -> Html Msg
view model =
  let
    angle =
      turns (Time.inMinutes model)

    handX =
      toString (50 + 40 * cos angle)

    handY =
      toString (50 + 40 * sin angle)
  in
    svg [ viewBox "0 0 100 100", width "300px" ]
      [ circle [ cx "50", cy "50", r "45", fill "#0B79CE" ] []
      , line [ x1 "50", y1 "50", x2 handX, y2 handY, stroke "#023963" ] []
      ]

```

こんな感じで使ってやれば良い

`field`の例だと、debugとしてNewContentが表示され、`time`の例だとTickが表示されるので

Msgの部分がdebugの結果として表示されるのかな？

と思われる



