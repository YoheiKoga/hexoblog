title: プログラミングElixir第6章
date: 2016-12-05 14:37:41
tags:
- Elixir
---

第6章

6.12 ライブラリを見つける
のところ

問題文のModulesAndFunctions-7の回答、オペレーティングシステムのシェルでコマンドを実行するについて

最初のほうの回答が`System.cmd("date")`となってるがエラーが出てくる

結論から言うと、`System.cmd("date", [])`としてやればdateコマンドが実行される

参考: [[Elixir]外部コマンドを叩く] (http://qiita.com/darui_kara/items/755463f2c4769d1777ed)



<!-- more -->


<br>

## System.cmd() の引数は２つ

`System.cmd("date")`だけiexで実行すると以下のエラーが出てきて

```elixir
** (UndefinedFunctionError) function System.cmd/1 is undefined or private. Did you mean one of:

      * cmd/2
      * cmd/3

    (elixir) System.cmd("date")
```

System.cmd/1 (System.cmdについて引数1の関数)は定義されてないかprivateですよってエラーがでる

で、続きに`cmd/2またはcmd/3じゃないですか？` ってメッセージに書いてあるので

試してみたらできた

```elixir
iex(55)> System.cmd("date", [])
{"2016年 12月 5日 月曜日 14時33分39秒 JST\n", 0}
```

とりあえず空のリストを与えている

これ、第二引数のところでコマンドのオプションを加えてるみたい

なので、以下のように色々オプションを渡してあげれる

```elixir
iex(53)> System.cmd("date", ["-u"])
{"2016年 12月 5日 月曜日 05時33分26秒 UTC\n", 0}
iex(63)> System.cmd("date", ["-u"])         
{"2016年 12月 5日 月曜日 05時44分48秒 UTC\n", 0}
iex(64)> System.cmd("date", ["-n"])
{"2016年 12月 5日 月曜日 14時44分52秒 JST\n", 0}
iex(65)> System.cmd("date", ["-nu"])
{"2016年 12月 5日 月曜日 05時44分54秒 UTC\n", 0}
iex(66)> System.cmd("date", ["-n", "-u"])
{"2016年 12月 5日 月曜日 05時45分03秒 UTC\n", 0}
```

ちなみに、cmd/3もあるみたいなので下記のようにもできる

```elixir
iex(56)> System.cmd("date", [], [])
{"2016年 12月 5日 月曜日 14時33分42秒 JST\n", 0}
iex(57)> System.cmd("date", ["-u"], [])
{"2016年 12月 5日 月曜日 05時33分45秒 UTC\n", 0}
iex(58)> System.cmd("date", [], ["-u"])
** (FunctionClauseError) no function clause matching in System.cmd_opts/3
    (elixir) lib/system.ex:578: System.cmd_opts(["-u"], [:use_stdio, :exit_status, :binary, :hide, {:args, []}], "")
    (elixir) lib/system.ex:555: System.cmd/3
```

第三引数だけいれるってのはだめみたい

まだ調べてないのでわからないが、ひとまずこんな風にしてshellコマンドを実行できるっぽい

