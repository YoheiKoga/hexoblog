title: Haskellでa~zのアルファベットを表現する
date: 2016-08-24 11:44:12
tags:
- Haskell
---

結論から書くと、`'a' <= x && x <= 'z'` と書いてやればよいっぽい

<br>

## Rubyでaからz

Rubyだと、a~zとかアルファベット全部(a-z, A-Z)を書く時は正規表現的な書き方で
`[a-z]`とか`[a-zA-Z]`とか記述するし
アルファベットの配列が欲しい時は

``` ruby
('a'..'z').to_a
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
```

と書いてやれば良い

<br>

## Haskellの場合
---

Haskellだと、[関数プログラミング実践入門](https://www.amazon.co.jp/%E9%96%A2%E6%95%B0%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AE%9F%E8%B7%B5%E5%85%A5%E9%96%80-%E2%94%80%E2%94%80%E7%B0%A1%E6%BD%94%E3%81%A7%E3%80%81%E6%AD%A3%E3%81%97%E3%81%84%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%9F%E3%82%81%E3%81%AB-WEB-PRESS-plus/dp/4774169269)の本に載ってる例を参考にするなら

第3章のパターンマッチとガードを組み合わせるのところででてきたコードを参考にすると

```hs
caseOfFirstLetter :: String -> String
caseOfFirstLetter "" = "empty"
caseOfFirstLetter (x:xs)
  | 'a' <= x && x <= 'z' = "lower"
  | 'A' <= x && x <= 'Z' = "upper"
  | otherwise            = "other"
```

のように、`'a' <= x && x <= 'z'` のように書いてやればよいっぽい

大文字を表す時は`'A' <= x && x <= 'Z'`となるし

小文字も大文字も全て含めたいなら `('a' <= x && x <= 'z') || ('A' <= x && x <= 'Z')`のようにかけばひとまず大丈夫かな？
この辺りはもうちょっと調べてみる

・・・
第四章を読んでたら出てきた

```hs
Prelude> ['A'..'Z']
"ABCDEFGHIJKLMNOPQRSTUVWXYZ"

Prelude> ['A'..'z']
"ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_`abcdefghijklmnopqrstuvwxyz"

Prelude> ['A'..'Z'] ++ ['a'..'z']
"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"

```

でいろいろ生成できるっぽい

【20160826 追記】
よく調べてみたらHaskellのチートシートの中に記述があった
[Haskellチートシート(翻訳)](http://qiita.com/techno-tanoC/items/1fa1c65db08da2440fc4#%E5%88%97%E6%8C%99)