title: プログラミングElixir 7章 ListsAndRecursion-0
date: 2016-12-17 12:17:13
tags:
- Elixir
---

[プログラミングElixir](https://www.amazon.co.jp/dp/B01KFCXP04/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)
の第7章

<!-- more -->

練習問題:ListsAndRecursion-0 (70ページ目ぐらいの問題)

以下を回答として考えてみたけど

```elixir
defmodule MyList do
  def sum2([]), do: 0
  def sum2([head]), do: head
  def sum2([head|tail]), do: sum2([head+sum2(tail)])
end
```

defの2つめ、`sum2([head])`部分が冗長的になりすぎてて

[回答例](https://forums.pragprog.com/forums/322/topics/11929)みてみたら下記みたいにもっと簡単だった

```elixir
defmodule MyList do
  def sum3([]), do: 0
  def sum3([head|tail]), do: head + sum3(tail)
end
```

doの後sumで二回も囲む必要はないみたい

foldLeft(左畳み込み)を意識して作ってみたのでまぁ練習にはなったかな

