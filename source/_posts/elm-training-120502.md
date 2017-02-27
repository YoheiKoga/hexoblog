title: packageのetaque/elm-responseを使ってみる
date: 2016-12-05 11:46:09
tags:
- Elm
---


[qiitaのElm Advent Calendar 2016](http://qiita.com/advent-calendar/2016/elm)の記事で

etaque/elm-responseのpackageを使ってるやつがあったがどんなpackageかわからなかったので

[Elm0.18で簡単なの作ったのでThe Elm Architectureをざっくり説明する](http://qiita.com/miyamo_madoka/items/c709ee89bc1c0e91e61e)

とりあえず[sample/random](http://elm-lang.org/examples/random)に適用してみた


<!-- more -->

2016年12月現在最新のElm 0.18を使った

<br>

# そもそもResponseのpackageは何をするためのものなのか

おそらく、Elm Architectureを記述するのに大規模になるとわかりにくくなってしまうので

少し異なった書き方をするための補助的なパッケージなのではと推測される


<br>

中身を見てみると

どうやら、Taskからの応答をパイプでマッチさせるためのpackageとのことで

[Elm Response](http://package.elm-lang.org/packages/etaque/elm-response/latest)


> This package offers alternate ways to construct and transform responses:

> このパッケージは、(Elm Architectureの)構築と変換に対して別の方法(書き方)を提供するパッケージです


だと

これを読む感じだと、「Elm Architectureに則った書き方もあるけどパイプとか使ってちょっと違った書き方できるようにしたよ！」

ってことなのかな？

というわけで、試しにElm ExampleのRandomのsampleを書き換えてみた

例えば、Modelの部分はResponseのwithNoneを使って下記のように書き換えることができる


```elm
import Response exposing (withNone)
...

-- MODEL


type alias Model =
  { dieFace:Int
  }

init : (Model, Cmd Msg)
init =
  { dieFace = 1
  }
    |> withNone

  -- under is also ok
  -- Model 1 |> withNone

  -- original is under
  -- (Model 1, Cmd.none)
```


こうすることで、initのところは `init = {dieFace = 1} |> withNone` または ` init = Model 1 |> withNone ` と書き換えることができる


<br>

また、withCmdを使えばupdateは以下のように書き換えることができる

```elm
import Response exposing (withCmd, withNone)
...

-- UPDATE


update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Roll ->
      --(model, Random.generate NewFace (Random.int 1 6))
      withCmd (Random.generate NewFace (Random.int 1 6)) model

    NewFace newFace ->
      -- (Model newFace, Cmd.none)
      Model newFace |> withNone
```

Randomのexampleだと、あまり簡潔にはなってないような気もするが

ひとまず、見通しをよくするためのpackageらしいので

「使わなくてももちろん書けるけど」ちょっと見通しを良くしたいときに使うpackageなのかな？

というイメージ








