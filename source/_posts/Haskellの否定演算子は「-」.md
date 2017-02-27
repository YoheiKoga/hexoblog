title: Haskellの否定演算子は「/=」
date: 2016-04-11 11:59:53
tags:
- プログラミング
- Haskell
---

タイトルが結論です、Haskellで「/=」と書いてあったら否定演算子のことです
Ruby, Python, PHP, Javaなど他の言語では「!=」のことです

<!-- more -->
ちょっとHaskellを触ってみようかと思い
チュートリアルや基本の説明などを見ていたのですが
http://learnyouahaskell.com/starting-out

その中で

```hs
ghci> 5 /= 5
False
ghci> 5 /= 4
True
```

って出てきて「え、なにこれ」
ってなったけどすぐに解決しました
https://en.wikibooks.org/wiki/Haskell/Truth_values

ただの論理演算子の否定の演算子でした

よく見る他の言語だと`!=`なんですがHaskellだと`/=`なんですね
Scalaでも`!=`みたいなのでちょっと戸惑いました

