title: プログラミングElmを始めてみる
date: 2016-09-13 10:26:51
tags:
- Elm
---

elm-lang.org
http://elm-lang.org/

<br>

---
# Haskellの影響を受けた関数型web言語
---

どうやらHTML, CSS, JSをひとつのファイルで書けるらしい
(コンパイルするとJSが吐き出される)

で、純粋関数型なので副作用なども生じにくいとか

[今日から始めるelm-lang](http://qiita.com/koichirokamoto/items/97218ee7fa641eb13847)

[Haskell 的視点から見た Elm](http://qiita.com/Kinokkory/items/0e10588ccc7b11224d3d)


[Elmとはどんな言語か？その７つの特徴【Elmアドベントカレンダー2014 1日目】](http://doloopwhile.hatenablog.com/entry/2014/12/01/200000)

<!-- more -->

<br>

### Functional Reactive Programmingに対応

FRPというリアクティブプログラミングに対応しているらしい

### モナドは無い？

Haskellによくにた文法ではあるがElmにはFRPがあるのでモナドは無いらしい
???

### とにかくJSをほぼ純粋関数型的に書くことができる

JSは関数型的に書くことができるけど純粋ってわけでもないので
これが本当ならいいかもしれん(ES6ではどうかわからないけど)


<br>

---
# ともあれ始めて見る
---


### まずは公式サイトをみてみる

公式サイトで始めてみる
[Get Started](http://guide.elm-lang.org/get_started.html)

Qiitaの記事も参考になりそう
[今日から始めるelm-lang](http://qiita.com/koichirokamoto/items/97218ee7fa641eb13847)

自分はMac OSX El Capitanで行った

### インストール

brewだと0.17らしいが最新は0.17.1っぽい？

公式でもインストーラー使ってたのでインストーラーを使う

### Editor

公式ではSublime Textをおすすめされているのでそっちでもいいが

VScodeの拡張でも書きやすいらしい

VScodeつかってみよかな

<br>

---
# とりあえず書いてみる
---

### まずはHello World

Qiitaの記事を参考にした
[Elm Reactor で Hello World](http://qiita.com/masakielastic/items/36a757ae08c1c1bd35ac)

記事内ではnpm installでelmを入れてるっぽいけどまぁ大丈夫だろう

適当なディレクトリを作成して`elm-make`でプロジェクトをセットアップする

```
$ mkdir elm-project
$ cd elm-project
$ elm-make --yes

# パッケージの導入
$ elm-package install elm-lang/html --yes

# Hello World用のファイルを作成する
$ touch Hello.elm

```

### とにかくコードを書いてみる

```elm Hello.elm
import Html exposing (text)

main = text "Hello, World!"
```

### HTTPサーバーを起動させる

ターミナルでHTTPサーバーを起動させる

```
$ elm-reactor
```

で、デフォルトだとポート8000でサーバーが起動するのでブラウザで [http://localhost:8000/](http://localhost:8000/) をひらく

以下のような画面が出てきたら成功っぽい

<img src="/2016/09/13/start-programming-elm/elm1.jpg" width="300px" height="200px">

あとはHello.elmをクリックして開いてみて、Hello World!が表示されたら成功

ちなみにサーバー起動時にport番号を指定したい場合、例えば3000にしたい場合は以下のようにする

```
$ elm-reactor --port 3000
```

### 公式サイトのコード

適当なディレクトリにMain.elmファイルを作る(さっきと同じところでいいかな)
中身はIntroductionに従って以下な感じ

```elm Main.elm
import Html exposing (Html, button, div, text)
import Html.App as App
import Html.Events exposing (onClick)

main =
    App.beginnerProgram { model = 0, view = view, update = update}

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

で、`$ elm-reactor`でサーバーを起動してあげて
下記リンクみたいに数字の増減ボタンが表示されたら成功
http://elm-lang.org/examples/buttons

<br>

# Elmファイルをコンパイルしてみる

`elm-make`コマンドでコンパイルしてみる

```
$ elm-make Main.elm
```

すると、同じディレクトリにindex.htmlファイルが生成しているので
ブラウザでアクセスしてみて動いたら成功

これだけ短いコードだったがindex.htmlファイルの中身を見てみたら8000行ぐらい
出力されていたので中身は省略

ちなみに、名前をつけてコンパイルしたい場合は以下のようにする

```
$ elm-make Hello.elm --output main.html
```

コマンドについては`$ elm-make -h`でオプションを調べることができる

<br>

# elmのREPL

以下のコマンドを打つとElmのREPLが起動する

```
$ elm-repl
```

<br>

# その他チュートリアル

elm-tutorial.orgというのがあったので
そこをみてやってみてもいいかもしれない
http://www.elm-tutorial.org/en/


<br>

# もしもelm-reactorの挙動が変になったら

下記qiita記事によると、coreを4.0.1 -> 4.0.2に更新した後、elm-reactorを動作させたら
表示されなくなったとのこと
http://qiita.com/koichirokamoto/items/97218ee7fa641eb13847

elm-stuffディレクトリを削除してelm-package installしたら戻ったとのことだが
念の為覚えておこう



