title: Elm練習7
date: 2016-10-12 10:57:13
tags:
- Elm
---

引き続きElm練習

[Elm練習6](https://yoheikoga.github.io/2016/10/11/elm-training-101101/)

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)

今回はSub/Cmd型の基本構造(architecture)

<!-- more -->

<br>


# Program型 Cmd/Subバージョン

Cmd/Subバージョンの説明は[プログラミング言語Elmの薄い本(サイト)](https://giisyu.gitbooks.io/elm_usui_book/content/src/elmArchitecture/cmdSub.html)を参照した

コードの基本形は[Elm tutorial / Structure of Html.App](https://www.elm-tutorial.org/en/02-elm-arch/02-structure.html)を参考にした

<br>

---
## Cmd/Sub型の基本形
---

大体下記のようなものは決まった構文が基本形になるっぽい

- mainにはHtml.App.program関数？を使う
- program関数の引数はinit, view, update, subscriptionsの４つ
- program関数の引数の内容を記述するために下記を定義する
  - MODEL
  - VIEW
  - UPDATE
  - SUBSCRIPTIONS

<br>

```hs
import Html exposing (..)
import Html.App


-- MODEL

type alias Model =
  someType(ex: String, Int, Boolean etc)


-- for example, Model = String
init : (Model, Cmd Msg)
init =
    ("Hello", Cmd.none)


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [text model]


-- UPDATE

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    NoOp ->
      (model, Cmd.none)

-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
  Sub.none

-- MAIN

main : Program Never
main =
  Html.App.program
    { init = init
    , view = view
    , update = udate
    , subscriptions = subscriptions
    }
```

subscriptionsにはmouseの挙動やキーボード入力イベント、ブラウザのロケーション変化などを入力できるので公式のexampleも見ておくとどんな感じかわかってよさそう


[examples/random](http://elm-lang.org/examples/random)

[examples/http](http://elm-lang.org/examples/http)

