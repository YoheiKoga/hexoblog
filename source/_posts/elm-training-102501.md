title: Elm練習9
date: 2016-10-26 10:07:04
tags:
- Elm
---

引き続きElm練習

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)


[exampleのtime](http://elm-lang.org/examples/time)のデバッグしてみる

<!-- more -->

<br>

---
# 色々といじってみる
---


timeのexampleがなんで1秒刻みで出来てるのかとか現在時刻の表示の仕方とか
わからなかったのでとりあえず画面にいろいろ表示させてみる


とりあえず今回ためしてみた最終的なコードは以下のようになる

```elm
module TimeTest exposing (..)

import Html exposing (Html, div)
import Html.App as App exposing (program)
import Svg exposing (..)
import Svg.Attributes exposing (..)
import Time exposing (Time, second, millisecond)
import Date exposing (Date)

main =
  program
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions 
    }


-- MODEL
-- ModelはTime型
type alias Model = Time

init : (Model, Cmd Msg)
init =
  (0, Cmd.none)


-- UPDATE

type Msg
  = Tick Time

-- update : Msg -> Time -> (Time, Cmd Msg)
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    -- 新しい時刻が入ってきた場合(というかこれしか入ってこない)
    Tick newTime ->
      (newTime, Cmd.none)


-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
  -- Time.every second Tickで現在のUNiX timeを取得することができる
  -- Time.every millisecond Tick -- millisecondで動かしたい場合
  Time.every second Tick


-- VIEW

-- 今回のmodelは、1秒刻みのUNIX timeを取得することができるやつ
view : Model -> Html Msg
view model =
  let
    angle =
      -- turns : Float -> Float(Convert turns to standard Elm angles(radians))
      -- ex) turns 180 / pi  --=> 360 : Float, turnsは2πのこと
      -- Time.inMinutes model で現在UNIX時刻を分表示する
      turns (Time.inMinutes model)

    handX =
      toString (50 + 40 * cos angle)

    handY =
      toString (50 + 40 * sin angle)


  in
    div[]
      [ svg[ viewBox "0 0 100 100", width "300", height "300"]
        [ circle [cx "50", cy"50", r"45", fill"#99ffaa"][]
        , line[ x1 "50", y1 "50", x2 handX, y2 handY, stroke "black"][]
        ]
      , div [][text ("UNIX Time is: " ++ toString model)]
      , div [][text ("handX is: " ++ handX)]
      , div [][text ("handY is: " ++ handY)]
      , div [][text ("cos angle is: " ++ toString (cos angle))]
      , div [][text ("sin angle is: " ++ toString (sin angle))]
      , div [][text ("現在時刻を表示 " ++ toString(Date.fromTime model))]
      , div [][text ("現時刻の秒を表示 " ++ toString(Date.second (Date.fromTime model)))]
      ]
```


画面は以下のようになる、色とかsampleと変えてみてる

<img src="/2016/10/26/elm-training-102501/sample.jpg" width="300px" height="200px">




<br>

---
# そもそも何が表示されているのか
---


どうやら、UNIX時間の秒刻み部分が表示されているだけみたいで

現在時刻の秒とは関係ないみたい

viewのところに色々表示させるために以下のようにsvgの部分をdivで囲ってみる

で、試しにmodelを表示させてみる


```hs
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
    div[]
      [ svg[ viewBox "0 0 100 100", width "300", height "300"]
        [ circle [cx "50", cy"50", r"45", fill"#99ffaa"][]
        , line[ x1 "50", y1 "50", x2 handX, y2 handY, stroke "black"][]
        ]
      , div [][text ("UNIX Time is: " ++ toString model)]
      ]
```


これでわかるのは、modelはいわゆるUNIX時間を表示しているということ

[UNIX時間](https://ja.wikipedia.org/wiki/UNIX%E6%99%82%E9%96%93)

つまりmodelは1970年1月1日からの経過秒数を更新表示し続けるので

これを利用すれば1秒刻みの動きが生成できる

アナログの形にするには、三角関数を使えば `-1 ~ 1` の間の数値を得ることができるので使ってるっぽい

あとは、svgのline関数を使えば適当な長さのlineを生成できるので、三角関数を利用してアナログ時計風にしてるだけっぽい
(なので表示されている秒数は実際の秒数とは関係ないっぽい)



<br>


この要領で、handXやhandY, cos angleやsin angleも表示させてみると、ただ適当な値がうまい具合に表示されているだけなのがわかる

```hs
--VIEW

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
    div[]
      [ svg[ viewBox "0 0 100 100", width "300", height "300"]
        [ circle [cx "50", cy"50", r"45", fill"#99ffaa"][]
        , line[ x1 "50", y1 "50", x2 handX, y2 handY, stroke "black"][]
        ]
      , div [][text ("UNIX Time is: " ++ toString model)]
      , div [][text ("handX is: " ++ handX)]
      , div [][text ("handY is: " ++ handY)]
      , div [][text ("cos angle is: " ++ toString (cos angle))]
      , div [][text ("sin angle is: " ++ toString (sin angle))]
      ]
```

<br>

---
# 現在時刻はどうやって表示すればよいのか
---

time関数はあくまで刻む時間を表示するとかUNIX時間を取得するためのものみたいなので

現在時刻とか日時を得るためにはDate関数を使う、のでDateをimportしておく

現在時刻を表示したい場合は以下のようにする、秒とか特定のものを得たい場合もDate.second関数のようにして使う

```hs
...
import Date exposing (Date)
...


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
    div[]
      [ svg[ viewBox "0 0 100 100", width "300", height "300"]
        [ circle [cx "50", cy"50", r"45", fill"#99ffaa"][]
        , line[ x1 "50", y1 "50", x2 handX, y2 handY, stroke "black"][]
        ]
      , div [][text ("UNIX Time is: " ++ toString model)]
      , div [][text ("handX is: " ++ handX)]
      , div [][text ("handY is: " ++ handY)]
      , div [][text ("cos angle is: " ++ toString (cos angle))]
      , div [][text ("sin angle is: " ++ toString (sin angle))]
      , div [][text ("現在時刻を表示 " ++ toString(Date.fromTime model))]
      , div [][text ("現時刻の秒を表示 " ++ toString(Date.second (Date.fromTime model)))]
      ]
```

Date関数とTime関数をうまく使えばデジタル時計とか日時を表示させることができそう

Elmのデバッグの仕方がまだよくわからないけど、定義した関数が実際どんな動きをしてるかなどは

こんな感じで視覚化できそう

