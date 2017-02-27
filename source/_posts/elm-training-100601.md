title: Elmの練習5
date: 2016-10-06 11:04:26
tags:
- Elm
---

引き続きElmの練習

[Elmの練習](https://yoheikoga.github.io/2016/10/05/elm-training/)

[Elm練習してみる2](https://yoheikoga.github.io/2016/10/05/elm-training-100502/)

[Elm練習してみる3](https://yoheikoga.github.io/2016/10/05/elm-training-100503/)

[Elm練習してみる4](https://yoheikoga.github.io/2016/10/05/elm-training-100504/)


今回はMaybe型から値を取り出してみる

<!-- more -->


<br>

---
# Maybeから値の取り出し
---

ElmではList.maximumはMaybe型を返すっぽい

```hs
List.maximum [1,2,3] -- Just 3 : Maybe.Maybe number
```

純粋に数字が欲しいのだがどうやるんだろ

HaskellだとfromMaybe関数があるみたいなのだが

=> CoreのResultにfromMaybeがあるっぽい

http://package.elm-lang.org/packages/elm-lang/core/4.0.5/Result

fromMaybeの定義は以下のようになっている

```
Result.fromMaybe
<function> : a -> Maybe.Maybe b -> Result.Result a b
```


まずは直接あててみる

```hs
Result.fromMaybe (List.maximum [1,2,3])
<function> : Maybe.Maybe a -> Result.Result (Maybe.Maybe number) a
```

あれ、関数が返ってきた

どうやら引数２つらしい

Haskellでの使い方に倣ってみると, Maybe型の値がJust値ならば中の値xを、そうでなければ第一引数の値dを返すらしい

参考 

http://skami.iocikun.jp/computer/haskell/web_lecture/for_programmer/fromMaybe.html


というわけで第一引数にまずは適当にアンダーバーを指定してみる

```hs
Cannot find variable `_`

3|   Result.fromMaybe _ (Just 3)
                      ^
Maybe you want one of the following?

    e
    Basics.e

```

アンダーバーなんてないよ、 e か Basics.e じゃない？
みたいなことを言われたのでそうしてみる

```hs
> Result.fromMaybe e (Just 3)
Ok 3 : Result.Result Float number
> Result.fromMaybe Basics.e (Just 3)
Ok 3 : Result.Result Float number
```

今度はOkという型がついて返ってきた...

なんか単純じゃなさそうなので調べてると、Maybe#withDefaultというのを見つけた

http://package.elm-lang.org/packages/elm-lang/core/4.0.5/Maybe#withDefault

withDefaultの定義は下記のような感じ

```
Maybe.withDefault
<function> : a -> Maybe.Maybe a -> a

# ex
withDefault 100 (Just 42)   -- 42
withDefault 100 Nothing     -- 100
```

引数２つで、Justだったらその中身を、Nothingだったら第一引数を返す？みたい

Maybeをimportしてこれでやってみたら下記のように取り出せた

```
> import Maybe

> Maybe.withDefault 100 (Just 3)
3 : number
> Maybe.withDefault 100 (List.maximum [1,2,3])
3 : number
```


これでとりあえず値は取り出せた

以下は余談でもう少し試した結果

<br>

### 第二引数と第一引数の型は一致してないといけないっぽい

第一引数がString、第二引数がMaybe numberの場合

```
> Maybe.withDefault "hello" (List.maximum [1,2,3])
-- TYPE MISMATCH --------------------------------------------- repl-temp-000.elm

The 2nd argument to function `withDefault` is causing a mismatch.

4|   Maybe.withDefault "hello" (List.maximum [1,2,3])
                                ^^^^^^^^^^^^^^^^^^^^
Function `withDefault` is expecting the 2nd argument to be:

    Maybe String

But it is:

    Maybe number

Hint: I always figure out the type of arguments from left to right. If an
argument is acceptable when I check it, I assume it is "correct" in subsequent
checks. So the problem may actually be in how previous arguments interact with
the 2nd.
```

> Maybe.withDefault "hello" (Just "world")
"world" : String
```

### 第二引数がNothingの場合

```hs
> Maybe.withDefault 1 (Nothing)
1 : number
> Maybe.withDefault "hello" (Nothing)
"hello" : String

> List.maximum []
Nothing : Maybe.Maybe comparable
> Maybe.withDefault "hello" (List.maximum [])
"hello" : String
```

Nothingなので第一引数が返ってくる




### 第一引数に Basics.e を指定

```hs
> Maybe.withDefault Basics.e (List.maximum [1,2,3])
3 : Float
```

今までnumberだったのに、Floatが返ってきた

どうやらBasics.eの型定義はFloatらしい

http://package.elm-lang.org/packages/elm-lang/core/4.0.5/Basics#e


ということは第一引数にはpiとかも使えるのかな

```hs
> Maybe.withDefault Basics.pi (List.maximum [1,2,3])
3 : Float
> Maybe.withDefault Basics.pi Nothing
3.141592653589793 : Float
```

いけた


というかeって何なのかと思ったら自然対数の底のeっぽい

```hs
> e
2.718281828459045 : Float
```

てっきりerrorがどうとかとおもってた


とりあえず以上である
