title: Elm練習してみる2
date: 2016-10-05 11:37:33
tags:
- Elm
---

[Elmの練習](https://yoheikoga.github.io/2016/10/05/elm-training/)

引き続き

<!-- more -->


# fieldを色々改変してみる

viewのところってどんな感じで書ける書けるのか試してみる

元のコードは公式exampleのfieldを参考にした

[field](http://elm-lang.org/examples/field)

元のコードのviewの部分

```elm
-- VIEW

view content =
  div []
    [ input [ placeholder "Text to reverse", onInput NewContent, myStyle] []
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

公式ドキュメントのHtmlのところをみてみると色々と足していけるらしい


## divの第二引数を削除してみる


以下のようにdivタグの第二引数を削除してやってみると

```elm
view content =
  div []
```

下記のようなエラーがでる

```
The argument to function `beginnerProgram` is causing a mismatch.

9|   beginnerProgram { model = "", view = view, update = update }
                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Function `beginnerProgram` is expecting the argument to be:

    { ..., view : String -> Html Msg }

But it is:

    { ..., view : String -> List (Html a) -> Html a }
```

これだけみたらなんのことかわからないが、引数は2つ必要なのでこういうエラーが出るっぽい

下記のようにしてやるとちゃんとエラーが消えて

そのかわりなにもviewに記載してないので白紙のページが表示される

```elm
view content =
  div [][]
```

## h1タグをたしてみる


試しに以下のようにh1を足してみる

```elm
view content =
  div []
    [ input [ placeholder "Text to reverse", onInput NewContent, myStyle] []
    , div [ myStyle ] [ text (String.reverse content) ]
    , h1 [][text "h1 test"]  -- 追加
    ]

```


`$ elm-reactor`で起動してみると以下のエラーが出る

```
Cannot find variable `h1`

26|     , h1 [][text "h1 test"]
          ^^
Maybe you want one of the following?

    Html.h1
    Html.h2
    Html.h3
    Html.h4
```

h1なんて見つかりませんよ、Html.h1ではないですか？みたいなのが出るので書き換える

```elm
view content =
  div []
    [ input [ placeholder "Text to reverse", onInput NewContent, myStyle] []
    , div [ myStyle ] [ text (String.reverse content) ]
    , Html.h1 [][text "h1 test"]  -- 訂正
    ]

```


これでちゃんと動いた!

もちろん、最初のimportのところでh1を足してあげても良い

`import Html exposing (Html, Attribute, text, div, input, h1)`


こんな感じで色々足してみる

尚いちいち`Html.h1`みたいに書くのはめんどうなので、`import Html exposing (Html, Attribute, text, div, input)`となっているところを

`import Html exposing (..)`と書きかえてやることで、Html moduleから全てをひっぱってこれるのでそうする

基本的には、HTMLの使い方と一緒である

例えばaタグならhrefが、imgタグならsrcが使えるっぽいというか使ってやる

ちょっと違うところは、`type`はElmの予約語で使えないので、buttonタグなどを使う際に

typeを指定してやりたい時はシングルクウォートを付けた`type'`を使う

参考

http://package.elm-lang.org/packages/elm-lang/html/1.1.0/Html-Attributes

<br>

下記にいくつか列挙してみた

```Elm
import Html exposing (..)
import Html.App exposing (beginnerProgram)
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
    [ input [ placeholder "Text to reverse", onInput NewContent, myStyle] []
    , div [ myStyle ] [ text (String.reverse content) ]
    , Html.h1 [][text "h1タグです"]
    , a [href "https://google.com"] [text "googleのリンクです"]
    , br [][text "brタグもいけるよ、brタグなので表示されないよ"]
    , span [][text "spanタグです"]
    , blockquote [][text "blockquoteです"]
    , blockquote []
        [ p [][text "入れ子もできます"]
        , p [][text "blockquoteに入ったpタグの二行目です"]
        ]
    , pre []
      [ code [][text "<div>code</div>
                                             <p>aaa</p>
                              preタグとcodeタグを組み合わせも可能"]
      ]
    , em [][text "hello em タグ"]
    , strong [][text "　hello strong タグ"]
    , ul []
      [ li [][text "list1"]
      , li [][text "list2"]
      , li [][strong[][text "list3 with strong タグ"]]
      , li [][em [][text "list4 with em タグ"]]
      ]
    , img[src "./test.jpg"][] -- 同じディレクトリにtest.jpg画像を設置しておく
    , textarea [][text "初期値が欲しいときはここを記載しておく"]
    , button [name "hello", value "valuedayo", type' "button"][text "buttonです"]  -- ボタンタイプを指定したい時は type' を使う
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


基本的にはHTMLで使ってる知識を適用してやればいいっぽいので、後は好きなようにやってみる



