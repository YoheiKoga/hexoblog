title: すごいH本第五章のnumLongChains関数のリストを出力
date: 2016-07-06 10:48:12
tags:
- Haskell
---

[すごいHaskellたのしく学ぼう!!](https://www.amazon.co.jp/dp/B009RO80XY/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)

の「第五章 高階関数」 のところ

numLongChains関数が出てくる
100までの数字でコラッツ列はいくつ出てくるのか？
という問題に対して答えは66みたいだが

具体的なリストはどうやって出力したらいいのか、がアホなことにわからなかったので記述

<!-- more -->

## とりあえず最終的なコード

は以下のようになる

```hs Main.hs
numLongChains :: [[Integer]]
numLongChains = filter isLong (map chain [1..100])
  where isLong xs = length xs > 15

chain :: Integer -> [Integer]
chain 1 = [1]
chain n
  | even n = n : chain (n `div` 2)
  | odd n  = n : chain (n * 3 + 1)

main :: IO ()
main = do

  print $ numLongChains 
```


上記コードを実行するとリストが出力される


### わからなかったところ

numLongChains :: [[Integer]]

は大カッコふたつ[[]] でくくっている
これは二次元配列が出力されるため当たり前なのだがここで最初つまった


詰まった時は、ghciで入力してやれば型を調べることができるのでやってみる

```
$ ghci

Prelude> let chain n | even n = n : chain (n `div` 2) | odd n  = n : chain (n * 3 + 1)
Prelude> let numLongChains = filter isLong (map chain [1..100]) where isLong xs = length xs > 15
Prelude> :t numLongChains
numLongChains :: [[Integer]]
```

numLongChainsは[[Integer]] と定義されていると出てきたので、スクリプトで定義する場合はこれを記述してやればよい

### どのようなエラーが出るか、どうやって解決したらいいか

ghciで試す前は、リストが出てくるはずだから

```hs
numLongChains :: [Integer]
```

と定義していたがこれを実行してみると

```
Couldn't match type ‘Integer’ with ‘t0 a0’
Expected type: Integer -> Bool
    Actual type: t0 a0 -> Bool
In the first argument of ‘filter’, namely ‘isLong’
In the expression: filter isLong (map chain [1 .. 100])

Couldn't match type ‘[Integer]’ with ‘Integer’
Expected type: Integer -> Integer
    Actual type: Integer -> [Integer]
In the first argument of ‘map’, namely ‘chain’
In the second argument of ‘filter’, namely ‘(map chain [1 .. 100])’
```

と怒られてしまう
t0とかa0とかは変数のことで特に意味は無い？みたいな感じらしいのだが
http://stackoverflow.com/questions/22286602/what-does-t0-t1-t2-and-a0-mean-in-haskell

なんのことかこのへんまだよくわからないのだがとりあえず

filterの返り値をIntegerで定義してるけど[Integer]だよ
ってことらしいのでInteger -> [Integer]

とすると [Integer] -> [[Integer]]

となるよね、って話


filter関数の定義は

`filter :: (a -> Bool) -> [a] -> [a]`

で、述語を満たすリストを返すって定義で
そのリストは chain [1..100]でリスト定義されているので
結果リストの中にリストが入っている二次元配列のようなものになっているため

[[Integer]]で定義してやらないといけない


自分で言っててまだ理解できてない・・・

