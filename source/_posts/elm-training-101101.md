title: Elm練習6
date: 2016-10-11 15:57:07
tags:
- Elm
---

引き続きElm練習

[Elmの練習5](https://yoheikoga.github.io/2016/10/06/elm-training-100601/)

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)

moduleの使い方を簡単にメモ

<!-- more -->


<br>

# moduleの作り方と使い方

Elmもmoduleとして名前をつけて外部から扱うファイルとして作成することができる

例えば、Main.elmというファイルに対してファイルの先頭に

```
module Main exposing (..)
```

とつけるとmodule化することができる

Players/Utils.elm というファイル構成だったら

```
module Players.Utils exposing (..)
```

とする

<br>

importする時は下記のようにする

```
import Players.Utils
```

<br>

---
## 例として、AdditionalExample.elmにadd1, reduce1という関数を定義して、elmのbutton sampleに適用してみる
---

それぞれファイルは同じディレクトリにあるとして

```hs AdditionalExample.elm
module AdditionalExample exposing (..)

add1 : Int -> Int
add1 x =
  x + 1

reduce1 : Int -> Int
reduce1 x =
  x - 1
```

button sampleのコードは下のように訂正する

```hs ButtonSample.elm
import AdditionalExample exposing (..)

import Html exposing (Html, div, button, text)
import Html.App exposing (beginnerProgram)
import Html.Events exposing (onClick)


main =
  beginnerProgram { model = model, view = view, update = update }


-- Model

type alias Model = Int

model : Model
model = 0

-- View

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]


-- Update

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      add1 model

    Decrement ->
      reduce1 model

```

のようにすればよい

