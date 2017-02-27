title: Elmで現在時刻を表示するアナログ時計
date: 2016-11-17 21:08:52
tags:
- Elm
---

Elmの公式サイトのexampleにtimeがあって

[example time](http://elm-lang.org/examples/time)

アナログ時計(みたいなもの)を表示するサンプルなのだが

現在時刻の秒を表示しているのではなくて

[適当に1秒刻みに秒針が動いているものが表示されているだけ](https://yoheikoga.github.io/2016/10/26/elm-training-102501)みたいなのと


<br>

2016年11月にElm v0.18 が出たので、練習がてら現在時刻を表示するアナログ時計を作ってみた

<br>


作成は[公式のexample](http://elm-lang.org/examples/time)と

下記stack overflowを参考にした

[How do I get the current date in elm?](http://stackoverflow.com/questions/37910613/how-do-i-get-the-current-date-in-elm)


Elmのバージョンはもちろん0.18を使用した



<!-- more -->

<br>

---
# とりあえずコードだけ載せる
---

SVGを使うので `$ elm package install elm-lang/svg` でelm-lang/svg を入れておくこと

とりあえず書いたコードは下記で

[githubにもあげてみた](https://github.com/YoheiKoga/ElmAnalogClockTimeNow)

```elm
module TimeTest exposing (..)

import Html exposing (..)
import Svg exposing (..)
import Svg.Attributes exposing (..)
import Time exposing (..)
import Date exposing (..)
import Task


main =
  Html.program
    { view = view
    , init = init
    , subscriptions = subscriptions
    , update = update
    }

-- MODEL
type alias Model =
  Time

init : (Model, Cmd Msg)
init =
  (0, now)

-- UPDATE

type Msg 
  = SetTime Time

update : Msg -> Model -> (Model, Cmd Msg)
update (SetTime time) _ =
  (time, Cmd.none)
-- Elm公式サイトのtimeのExampleの書き方に従うなら下記
-- update msg model =
--  case msg of
--    SetTime newTime ->
--      (newTime, Cmd.none)

now : Cmd Msg
now =
  Task.perform SetTime Time.now


-- SUBSCRIPTION

subscriptions : Model -> Sub Msg
subscriptions model =
  Time.every Time.second SetTime

-- VIEW

view model =

  let
    secondNow =
      Date.second <| Date.fromTime <| model
    minuteNow = 
      Date.minute <| Date.fromTime <| model
    hourNow =
      (Date.hour <| Date.fromTime <| model) % 12

    angleSecond =
      toFloat <| 6 * secondNow
    angleMinute =
      toFloat <| 6 * minuteNow
    angleHour =
      -- 下記は(toFloat <| 30 * hourNow) + (angleMinute / 12) と同じ
      (+) (angleMinute / 12) <| (toFloat <| 30 * hourNow)


    handSecondX =
      toString (50 + 38 * sin (angleSecond * pi / 180))
    handSecondY =
      toString (50 - 38 * cos (angleSecond * pi / 180))

    handMinuteX =
      toString (50 + 32 * sin (angleMinute * pi / 180))
    handMinuteY =
      toString (50 - 32 * cos (angleMinute * pi / 180))

    handHourX =
      toString (50 + 25 * sin (angleHour * pi / 180))
    handHourY =
      toString (50 - 25 * cos (angleHour * pi / 180))


  in
    div []
        [ svg [ viewBox "0 0 100 100", width "300px" ]
            [ circle [ cx "50", cy "50", r "45", fill "#2a2a2a" ] []
            , circle [ cx "50", cy "50", r "40", fill "#f6f6f6" ] []
            , line [x1 "50", y1 "50", x2 handSecondX, y2 handSecondY, stroke "#2a2a2a", strokeWidth "0.5"][]
            , line [x1 "50", y1 "50", x2 handMinuteX, y2 handMinuteY, stroke "#2a2a2a", strokeWidth "1"][]
            , line [x1 "50", y1 "50", x2 handHourX, y2 handHourY, stroke "#2a2a2a", strokeWidth "2"][]
            ]
        -- 以下は時間のデバッグ用に表示
        , div [] [Html.text ("model(UNIX time): "        ++ (toString model))]
        , div [] [Html.text ("Date.fromTime model: "     ++ (toString (Date.fromTime model)))]
        ]
```

<br>

下の画像みたいなのが表示されたら成功

<img src="/2016/11/17/elm-analog-clock-time-now/analogClock.gif" width="400px" height="400px">


余力があったら作り方のチュートみたいなのを書こうと思う