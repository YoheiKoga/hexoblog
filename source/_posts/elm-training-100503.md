title: Elm練習してみる3
date: 2016-10-05 15:38:31
tags:
- Elm
---


引き続きElmの練習してみる

[Elmの練習](https://yoheikoga.github.io/2016/10/05/elm-training/)

[Elm練習してみる2](https://yoheikoga.github.io/2016/10/05/elm-training-100502/)


<!-- more -->


<br>

---
# 公式のexampleに少し手を加えてみる
---


公式のサンプルのbuttons

[buttons](http://elm-lang.org/examples/buttons)

これ、modelやupdateに型宣言がついていないのでこれに型をつけることで

少し練習になるかと思ってやってみる

最終的なコードは以下になる

```hs buttonsCustomize.elm
import Html exposing (Html, div, button, text)
import Html.App exposing (beginnerProgram)
import Html.Events exposing (onClick)


main =
  beginnerProgram
    { model = model
    , view = view
    , update = update
    }


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
      model + 1

    Decrement ->
      model - 1
```


以下流れを書いていってみる

<br>

## mainを書き換える


mainの部分は通常以下のように一般的にできる

```hs
main =
  beginnerProgram
    { model = model  -- 0 から model に書き換えてあげる、ここが初期値になる
    , view = view
    , update = update
    }
```

この状態だと下記エラーが出る

```
Cannot find variable `model`

7|   beginnerProgram { model = model, view = view, update = update }
                               ^^^^^
Maybe you want one of the following?

    Html.code
    Html.del
    Html.node
```

modelというのが定義されてないのでわかりませんっていうこと

なのでmodelを定義してあげる

<br>

## modelを追加する

通常はmodelを定義するが、buttonsのexampleだと初期値が0でわざわざ定義しなくても

大丈夫っぽいから定義されていない

が、練習のため敢えて定義してやる

- 今回のbuttonsの場合、数字だけ必要で整数なのでInt型で定義してやる
- そして初期値に0を入れてやる

以下のように定義できる

```hs
-- Model

type alias Model = Int

model : Model
model = 0
```

<br>

## viewに型をつけてやる

おそらくそのままでも型推論で勝手に型が定義されているはずなので

自分で敢えて設定してやる

といっても、基本的にはviewの型は`view : Model -> Html Msg`となることが多い？みたいなので

素直にそうしてやる(この辺りは熟練したら自分で定義できていくようになるんだろなと思う)

```hs
-- View

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```


下記エラーが出るはず

```
Cannot find type `Html`

19| view : Model -> Html Msg
                    ^^^^
Maybe you want one of the following?

    Html.Html
```

Htmlという型はみつかりませんという風に出てくる

これはHtml.Htmlをimportしていないからなので、importの部分を書き換えてやる

```hs
import Html exposing (Html, div, button, text)
```

これでエラーが消えるはずである

<br>

## updateに型を定義する

最後にupdateに型を定義する

これも、よく用いられるのが`update : Msg -> Model -> Model`なので素直につけてやる

```hs
-- Update

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1
```

ここはそのままでうまくいった

Elmは情報がまだまだ少ないけど

こんな感じでsampleのコードを少しづつ改変していけば練習になりそう





