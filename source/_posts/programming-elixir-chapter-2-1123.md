title: プログラミングElixir 2章
date: 2016-11-23 12:23:35
tags:
- Elixir
---

[プログラミングElixir](https://www.amazon.co.jp/Elixir-Dave-Thomas-ebook/dp/B01KFCXP04/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=1479868823&sr=8-12)の第二章を読んでみてる

<!-- more -->

<br>

# リストについて

「2.2 もっと複雑なマッチ」の部分

他の関数型言語だと、listには同じ型のものしか入れられないことが多い(だいたいそう？)が

Elixirは並べられるみたい

```
iex(8)> list = [1, "dog", true]
[1, "dog", true]
iex(9)> [a, b, c] = list
[1, "dog", true]
iex(10)> a
1
iex(11)> b
"dog"
iex(12)> c
true
```


