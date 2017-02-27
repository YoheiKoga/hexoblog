title: Haskellで素数を出力
date: 2016-07-05 09:50:37
tags:
- Haskell
---


[codeIQで素数の数を出力する問題](https://codeiq.jp/q/1621)があったのでHaskellでやってみてたけど

言語選択しようと思ったらHaskellはなかったorz

ので、とりあえず書いてみたので記載

<br><br>

といっても難しいので色々と参考にした

[Haskell で素数](http://tsumuji.cocolog-nifty.com/tsumuji/2009/12/haskell-d3ed.html)

[Haskellで素数列生成](http://d.hatena.ne.jp/negikey/20150130/1422636764)

<br><br>
あと調べてみると、Melissa O'Neillさんの書いたコードがかなり高速らしいが読んでもわからなかったのでひとまず保留

[haskell wikiのPrime numbersページ](https://wiki.haskell.org/Prime_numbers#External_links)

[ONeillPrimes の実行速度](http://ducalog.tumblr.com/post/94141911827/oneillprimes-%E3%81%AE%E5%AE%9F%E8%A1%8C%E9%80%9F%E5%BA%A6)

<br><br>
いつか読めるようになるのだろうか

<br><br>

# ひとまずコードを読む練習

まだまだHaskellはわからないのでひとまず人のコードを読んでみたのでコメントがてら残しておく
まだ理解が足りてないので間違えてるとは思うがこういうの残しておかないと

```hs Main.hs
import Control.Applicative
import System.IO (isEOF)
import Data.Char

-- 参考: http://tsumuji.cocolog-nifty.com/tsumuji/2009/12/haskell-d3ed.html
  -- primeListはInteger型の引数を取ってInteger型のリストを生成する
primeList :: Integer -> [Integer]
  -- primeList n は 3以上の奇数のリスト(n-2まで)を篩にかけて2を先頭に加えたもの
primeList n = 2:sieve[3,5..(n-2)]
  where
      -- isqrtは、nの平方根を取ってtruncateで小数点を端折ったもの
    isqrt = (truncate.sqrt.fromIntegral) n
      -- sieveをガードで場合分け
    sieve (p:xs)
      | p > isqrt = p:xs
        -- xを奇数の配列から取り出した値で割った余りが0で無い場合をリスト内包表記でフィルタリングして、さらにsieveを再帰させる. p > isqrtまで続く
      | otherwise = p : sieve [x | x <- xs, rem x p /= 0]

main :: IO ()
main = do

  eof <- isEOF
  if not eof then do
      s <- getLine
      print $ length (primeList (read s :: Integer))
      main
  else return ()
```

これで、標準入力から数字を入力すると与えた数字より小さい値の素数の数が求められる
