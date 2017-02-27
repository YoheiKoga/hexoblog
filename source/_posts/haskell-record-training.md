title: Haskellのレコード練習:Personのリストから20歳以上の値を取り出す
date: 2017-01-19 17:19:37
tags:
- Haskell
---

Qiitaの記事で以下Haskellの入門記事があって

[Haskell入門。関数からモナドまで](http://qiita.com/lotz/items/003585e3c52cd24ce59d)

すごく丁寧に書かれてるので今始めるなら上記記事が良さそう

で、その中で下記レコードのとこで演習問題的なのがあったのでメモがてら記述

[レコード](http://qiita.com/lotz/items/003585e3c52cd24ce59d#%E3%83%AC%E3%82%B3%E3%83%BC%E3%83%89)

<!-- more -->

<br>

## とりあえずmapを使う方法と使わない方法がある

ので両方合わせたのが下記

```haskell
adults :: [Person] -> [String]
adults [] = []
adults (Person name age : xs)
  | age > 19 = [name] ++ adults xs
  | otherwise = [] ++ adults xs

adults' :: [Person] -> [Person]
adults' [] = []
adults' (Person name age : xs)
  | age > 19 = [Person name age] ++ adults' xs
  | otherwise = [] ++ adults' xs

main :: IO ()
main = do

  print $ adults [Person "alice" 19, Person "bob" 20]
  print $ map name $ adults' [Person "alice" 18, Person "bob" 20]
```

<br>

qiita内の記事だと`map name $ adults [Person "alice" 18, Person "bob" 20]`で出力されるようにしていたので

`adults'`関数のほうだとそのようにできるはず


これが綺麗なコードかと言われるとわからないが練習がてらでメモとして残しておく


<br>

余談

最近関数型の練習でHaskell, Scala, Elm, Elixirあたりをうろちょろしてて全部中途半端だなぁと思ってもいるが

他の言語だとわかりやすく説明されてるところとかもあるので

ちょっとずつ色々なものを触ってみてると、立ち戻ってきた時に以外と書けるようになってたりして面白い

(例えばElm触ってるとなんかHaskellの書き方がぼんやりとわかってくるし、そもそもElmはHaskell触ってたから導入自体はそんなに苦じゃなかった)

相互補完してちょっとずつ進んでいこうと思う