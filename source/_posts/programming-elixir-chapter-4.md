title: プログラミングElixir 4章
date: 2016-11-23 16:11:26
tags:
- Elixir
---

[プログラミングElixir](https://www.amazon.co.jp/Elixir-Dave-Thomas-ebook/dp/B01KFCXP04/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=1479868823&sr=8-12)の第4章を読んでみてる

Elixirはバージョン1.3.3を使用している

<!-- more -->

<br>

# with do endについて

4.7 変数スコープのところで `with do end` がでてきてなんだこれって思ったけど

ひとまず、他の関数型言語でいうところの`let do`みたいなもの？なのかな？
http://qiita.com/HirofumiTamori/items/591925f6c09de6c67422

なんかリンク先では`with do:`になっててendがないけど、とりあえず試してみる

ひとまず使ってみるだけなら以下のようにしてみれば動くことは動く

```elixir test.exs
owner = "some name"

with  name = "/etc/passwd",
      stat = File.stat!(name),
      owner = stat.uid,
do:
      IO.puts "#{name} is owned by user ##{owner}"

      IO.puts "And #{owner} is still someone"
```


ElmとかHaskellで出てくる`let do`構文に確かに似てて

プログラミングElixir内でも、「ローカル変数のスコープを定義する」と書かれている(２つある役割の一つ)

withの中で変数に値を入れて、doの中でその変数を使う、みたいな使い方なのかなこの例をみると

<br>

### with do: の部分を変数に格納することもできる

下記のようにもできる

```elixir test.exs
owner = "some name"

sample_test =   with  name = "/etc/passwd",
                      stat = File.stat!(name),
                      owner = stat.uid,
                do:
                      IO.puts "#{name} is owned by user ##{owner}"
                      IO.puts "And #{owner} is still someone"

IO.puts sample_test
```


<br>

ちなみにこれ、`IO.puts sample_test`のところをコメントアウトして実行すると以下のエラーが出る

```
warning: variable sample_test is unused
  test.exs:3

/etc/passwd is owned by user #0
And some name is still someone
```


ググると以下が出てきた

[is there a way to suppress warnings like `variable x is unused` ?](https://groups.google.com/forum/#!topic/elixir-lang-talk/tgVFfJG7xBU)


変数sample_testは使われてませんよって警告

Rubyでも`unused variable`って警告が出て来ることがあって、無駄な変数が定義されてるときに警告してくれるものっぽい

Elixirではこういうふうに出てくるんだな


で、プログラミングElixirには`with do end`の構文でのってるけど、これでやるとエラーになる・・・


**以下はエラーになる**

```elixir
owner = "some name"

sample_test =   with  name = "/etc/passwd",
                      stat = File.stat!(name),
                      owner = stat.uid,
                do
                      IO.puts "#{name} is owned by user ##{owner}"
                      IO.puts "And #{owner} is still someone"

                end
```


なんか仕様が変わったのかな、、、このあたりはわからない











