title: Elm練習8
date: 2016-10-12 12:38:40
tags:
- Elm
---

引き続きElm練習

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)


今回はMouseイベントについて

<!-- more -->

<br>

---
# MouseやKeyboardにもいろいろなイベントが付加できるっぽい
---

[Elm tutorial / Subscriptions](https://www.elm-tutorial.org/en/03-subs-cmds/01-subs.html)

のところでKeyboard eventとMouseイベントが出てくる

この例ではMouseのクリックとKeyboardの何かキーをおした時にイベントが発火するという例だが

それ以外にもMouseを動かした時とかKeyboardのキーをあげた時とか他にもあったので使い方含め纏めてみる

<br>

### Mouseのイベント

公式のドキュメントを参照

http://package.elm-lang.org/packages/elm-lang/mouse/latest/Mouse

- clicks: クリック(mouseのボタンを押して離す)した時に起きる
- moves: mouseを動かした時に起こる
- downs: mouseのクリックボタンを押し下げた時に起きる
- ups: mouseのクリックボタンを上げた時に起きる, ほとんどクリックと似てる?

mouseのup, down, clickの違いや使いみちについてはゲームとかで利用されるらしい

[clickとmousedownの違い](http://naoyashiga.hatenablog.com/entry/2013/10/19/105445)

<br>

### Keyboardのイベント

公式のドキュメントを参照

http://package.elm-lang.org/packages/elm-lang/keyboard/latest/Keyboard

- presses: キーを押したときに起こる(押し続けると連続して起こる)
- downs: キーを押し下げたときに起こる(押しっぱなしはできない)
- ups: キーを上げたときに起こる

<br>

### チュートリアルのコードを書き換えてみる

チュートリアルの、subscriptionsの部分をの関数を書き換えてやれば動作が変わる

ためしに、Mouseはmovesを、Keyboardはupsを設定してみた

```elm
-- Subscriptions

subscriptions : Model -> Sub Msg
subscriptions model =
  Sub.batch
    [ Mouse.moves MouseMsg
    , Keyboard.ups KeyMsg
    ]
```


他のところは型の設定とかなので基本的にさわらなくて大丈夫みたい

コードの全体は以下のようになる

```elm
module Main exposing (..)

import Html exposing (Html, div, text)
import Html.App
import Mouse
import Keyboard


-- Model

type alias Model =
  Int

init : (Model, Cmd Msg)
init =
  (0, Cmd.none)

-- Messages

type Msg
    = MouseMsg Mouse.Position
    | KeyMsg Keyboard.KeyCode



-- View

view : Model -> Html Msg
view model =
  div []
    [text (toString model)]


-- Update

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of 
    MouseMsg position ->
      (model + 1, Cmd.none)

    KeyMsg code ->
      (model + 2, Cmd.none)

-- Subscriptions

subscriptions : Model -> Sub Msg
subscriptions model =
  Sub.batch
    [ Mouse.moves MouseMsg
    , Keyboard.ups KeyMsg
    ]

-- Main

main : Program Never
main =
  Html.App.program
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    }
```



