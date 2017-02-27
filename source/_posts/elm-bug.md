title: elm-make: Map.!: given key is not an element in the mapというバグが出たら
date: 2016-11-18 10:57:17
tags:
- Elm
---


Elm v0.18を使っていて

`$ elm-reactor`したら突然

```
elm-make: Map.!: given key is not an element in the map
```


という画面が表示されてしまった

結論から言うと、elmのコンパイル系のバグっぽいらしく

elm-stuffフォルダを削除してから、`$ elm-package install`コマンドでpackageを入れ直したら直りました

<!-- more -->

<br>

# slackのグループで解決


Elmのslackグループがあって

そこで同じ問題に遭遇されてる方がいた
https://elmlang.slack.com/archives/general/p1479415555031783

で、「elm-stuffを削除してelm-package installし直すといいよ」って回答もあった
https://elmlang.slack.com/archives/general/p1479422440031805

こういう時slack便利だなと改めて思った

