title: Elm練習12
date: 2016-11-18 14:31:21
tags:
- Elm
---

引き続きElm練習

[Elm練習 archives](https://yoheikoga.github.io/tags/Elm/)

今回はデバッグのためにmaybe型を無理やりtext関数で表示させる

Elmはv0.18を用いている
(今回、v0.18から導入された field関数(Elm v0.17では(:=)だった関数)を使ってるので、Elm v0.18でないと動かないと思われる)

<!-- more -->

<br>

---
# Maybe型とかResult型をtextで表示したい
---

なんか、Maybe型などは単なるstring型とかではないので、そのまま`text`で表示しようとしても

型エラーが起きて表示されない

Result型からはtoMaybe関数を使えばMaybe型にいけるけどそれ以降はなさそう・・・

ので、自分で関数作って無理やり表示させる

例えば

```elm
import Json.Decode exposing (..)
import Html exposing (..)

json = """{ "person": { "name": "tom", "age": 42 } }"""

decoding = decodeString (field "person" (field "name" string))


main =
  view

view =
  text (Result.toMaybe (decoding json))
```

<br>

上記を実行すると下記エラーが出る



```
The argument to function `text` is causing a mismatch.

47|   text (Result.toMaybe (decoding json)
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Function `text` is expecting the argument to be:

    String

But it is:

    Maybe String
```

<br>

textはString型をとるけど、あなたが引数に渡したのはMaybe String型だよ

って怒られてしまう

<br>

なので、自分でMaybe a -> a となる関数を作ってやれば良い

例えば(綺麗なコードではないけど)いかのようなmaybeStringToString関数をつくってやればよい

ResultはMaybeと同じで暗黙的にimportされてるのでimportしなくてよい

```elm
import Json.Decode exposing (..)
import Html exposing (..)

json = """{ "person": { "name": "tom", "age": 42 } }"""

decoding = decodeString (field "person" (field "name" string))


main =
  view


view =
  text (maybeStringToString (Result.toMaybe (decoding json)))

maybeStringToString : Maybe String -> String
maybeStringToString msg =
  case msg of
    Just x -> x
    Nothing -> ""
```

これで`$ elm-reactor`で [localhost:8000](http://localhost:8000) を確認してみると、画面に `tom`と表示されるはず