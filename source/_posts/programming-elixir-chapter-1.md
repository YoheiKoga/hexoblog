title: プログラミングElixir 1章
date: 2016-11-23 11:39:11
tags:
- Elixir
---

[プログラミングElixir](https://www.amazon.co.jp/Elixir-Dave-Thomas-ebook/dp/B01KFCXP04/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=1479868823&sr=8-12)の第一章を読んでみてる


<!-- more -->

<br>

# iexのiヘルパーの部分

iexのヘルパーのとこで使う`i`の例で`iex> i "cat"`としてるところ

画面に以下のように出力されるはず

```elixir
iex(6)> i "cat"
Term
  "cat"
Data type
  BitString
Byte size
  3
Description
  This is a string: a UTF-8 encoded binary. It's printed surrounded by
  "double quotes" because all UTF-8 encoded codepoints in it are printable.
Raw representation
  <<99, 97, 116>>
Reference modules
  String, :binary
```


ここで、

```
Raw representation
  <<99, 97, 116>>
```

と出力されている

これ、10進数のアスキーコードのことなので `<<99, 97, 116>>` でアルファベットのcatと表示される

http://www3.nit.ac.jp/~tamura/ex2/ascii.html

アスキーコードでは、99は'c'で97は'a'で116は't'なのである

なので、iex上で以下のようにしてもちゃんと表示されるみたい

```
iex(8)> <<99, 97, 116>>
"cat"
```





