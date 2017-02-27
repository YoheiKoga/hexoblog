title: Elmの練習
date: 2016-10-05 11:08:41
tags:
- Elm
---

プログラミングのElmをやってみてる

バージョンは現在最新の0.17で、バージョンが変わると動かなくなる可能性あり

[プログラミングElmを始めてみる](https://yoheikoga.github.io/2016/09/13/start-programming-elm/)

Elmの書き方がよくわからないので練習がてら色々試してみる


<!-- more -->

<br>

---
# beginnerProgramってなに
---

(まだ理解が完璧ではないがおそらく) beginnerProgramはHtml.Appに含まれる関数で

Elmの初期に実装された、Elm-Architectureに則った書き方をする際に必要な関数

現行では、subscriptionsという関数が追加されているので、それに則ったprogramという関数もある

以下は調べた流れ

<br>

Elm書いてたら `import Html.App exposing (beginnerProgram)`

って出てくるけどこれはいったいなんなんだろうと思って調べてみたら下記がヒットした

[Elmというミニマムでフレームワークにもなる関数型altJS言語を触ってみよう！！！](http://qiita.com/jooex/items/89ab4bf7c953a6f40069)

>beginnerProgram 関数は引数にmodel、view、update関数をとります。
modelは状態の初期値、
viewは状態を画面に出す関数、
updateは(画面をクリックするなどして)イベントが起きた時の処理を書きます。


らしい

さらに下記を参考にすると

[プログラミング言語Elmの薄い本(サイト)](https://giisyu.gitbooks.io/elm_usui_book/content/)

- Elmのmainの型はHtml a か Program a である必要がある

- Html a 型
    - 静的な画面

- Program a 型
    - さらに入力や操作ができるアプリケーション(動的？)


- Elmには発案初期バージョンと、現行バージョンの2種類のElm-Architectureがある

- 初期型を初期バーション、現行をCmd/Subバージョンと呼ぶことにしている

- 初期のバージョンはビギナー用になっていて、 beginerProgramという関数を使う

- 現行バージョンは program という関数を使う

とのこと

とりあえず、beginnerProgramは簡単な静的ページ(データのやりとりとかない一枚ページと思えばいいかな)

を作るのに使う関数っぽくて

Elmをやるならそこから入るのがいいみたい

<br>

---
# Elm Architectureについて知らないといけない
---

Elmを書き始めると、何が必須でどこが変更可能なのか最初わかりずらい

model, view, update, init, subscriptionsといった

必ず必要な構文があってそれがわからないとおそらく書き方がわからないと思うので

Elmの構文は公式サイトなどで学んでおくべき

https://guide.elm-lang.org/architecture/

<br>

### 何を変更してよくて何がいけないか

を調べるためにsampleコードを改変してみた

公式サイトより

https://guide.elm-lang.org/

```elm
import Html exposing (Html, button, div, text)
import Html.App as App
import Html.Events exposing (onClick)

main =
  App.beginnerProgram { model = 0, view = view, update = update }

type Msg = Increment | Decrement

update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1

view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```


がもともとのcode

ただし、どうにも view = viewとか update = updateというのが慣れない

ので、変えてよさそうなところは変えてみて以下のようにしてみた

関数名とかほんと適当、でも動くのでこのへんは変えて良いところ(自分で名付けるところ)

というのがわかった(もちろん慣習に従ったほうがコードは読みやすくはなると思う)

<br>

```elm
import Html exposing (Html, button, div, text)
import Html.App as App
import Html.Events exposing (onClick)

main =
  App.beginnerProgram { model = 0, view = nanoyo, update = desuwa}

type PeroPero = Fueru | Heru

desuwa msg potechi =
  case msg of
    Fueru ->
      potechi + 1

    Heru ->
      potechi - 1

nanoyo model =
  div []
    [ button [ onClick Heru] [text "-"]
    , div [] [text (toString model)]
    , button [ onClick Fueru] [text "+"]
    ]
```


こんな感じで自分で色々変更できるとこは変更してやってみるといいかもしれない

型名とか関数名とか以外と変更可能だったりする

<br>

---
# 基本パターン
---

Model Update Viewで構成される

```elm
import Html exposing (..)

-- Model

type alias Model = {...}


-- Update

type Msg = Reset | ...

update : Msg -> Model -> Model
update msg model =
  case msg of
    Reset -> ...
      ...


-- View

view : Model -> Html Msg
view model =
  ...
```



とりあえず
- main内にbeginnerProgramを記載する、最初はmodel, view, updateだけでいい?
- Model, Update, Viewを記載する
  - Model -- アプリケーションの状態を示す、初期値とか変更があったら変更の値とか
  - Update -- どうやってアップデートするかを記述する、そのまま代入するとか四則演算を使うとか
  - View -- HTMLとしてどのように見えるかを記述する


<br>

### modelの書き方


```elm
type alias Model =
  { someFunction : someTypes      
  }

model : Model
model =
  { sumeFunction = "初期値を入れる"
  }
```


が基本形っぽい


<br>

### updateの書き方

```elm
type Msg
  = someUpdateFunction someType


update : Msg -> Model -> Model
update msg model =
  case msg of
    someUpdateFunction someNewVariable ->
      { model | content = someNewVariable}
```


<br>

### Viewの書き方

```elm
view : Model -> Html Msg
view model = 
  div []
    [ input [ placeholder "Text to reverse", onInput Changemychange][]
    , div [][ text (String.reverse model.content)]
    ]
```

<br>

---
# 例えばdivってなんで大カッコ２ついるの？
---

Elm書いてると

```elm
view : Model -> Html Msg
view model = 
  div []
    [ input [ placeholder "Text to reverse", onInput Change][]
    , div [][ text (String.reverse model.content)]
    ]

```


みたいなことになるけど

div [] []

って引数２つのリストなのはなんなんだろう

色々みてると、第一引数にはcss定義したstyleとかしてて第二引数には初期値とかコンテンツを入れるのかな

=> そうっぽい


myStyleが以下のように定義されている時

```elm
myStyle =
  style
    [ ("width", "100%")
    , ("height", "40px")
    , ("padding", "10px 0")
    , ("font-size", "2em")
    , ("text-align", "center")
    ]
```


例えば以下のようにHTMLに変換されるっぽい


```html
input [ placeholder "Text to reverse", onInput NewContent, myStyle ] []

<input placeholder="Text to reverse" style="width: 100%; height: 40px; padding: 10px 0px; font-size: 2em; text-align: center;">



div [ myStyle ] [ text (String.reverse content) ]

<div style="width: 100%; height: 40px; padding: 10px 0px; font-size: 2em; text-align: center;"></div>

```


なので

`div [styleやidやclassとかを記述] [初期値や変更後の値]`

みたいに指定するのかなたぶん


例えば、inputの第二引数にmyStyleを入れてみたら以下のようなエラーが出た

```
24|       input [ placeholder "Text to reverse", onInput NewContent, myStyle] [myStyle]
                                                                              ^^^^^^^^^
Function `input` is expecting the 2nd argument to be:

    List (VirtualDom.Node Msg)

But it is:

    List (VirtualDom.Property a)
```


inputの第二引数には List型の VirtualDom.Node Msg が入るはずなのに
List型の VirtualDom.Property a が入ってるよだめだよ

ってエラーがでるのでこういうのに従って書いていけばいいのかな





とりあえずここまで、もうちょいやってみないと