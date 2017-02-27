title: CentOSでHaskellプログラミング始め
date: 2016-08-14 16:35:57
tags:
- Haskell
---


Haskellを始めてみる
最初はよくわからないのでCentOSに入れてやってみる
注意点としては、haskellを入れる際に容量が1.1GB程度と大きいので注意

<!-- more -->

---
# とりあえず触ってみる
---

クラウド上の環境が整ってるので触りやすい紹介がされてるページ
http://qiita.com/arowM/items/9ebfb7cafecd99290663#fn1

---
# CentOSへのインストール
---

### haskellの開発環境としてplatformとstackがある

現在では、stackというのが主流になってきているらしいのでそっちを入れるのが良い
一応、platformの入れ方も下記記述しておく

---

** platformここから **

参考
http://qiita.com/CPyRbJvCHlCs/items/9da9b43b553df8751a91

yumで簡単には入るが、epelのレポジトリが必要みたいなのでまずはそちらから入れる
その後、haskell platformを入れる容量が1.1GB程度と大きいので、容量注意なのと時間がかかる

ディスクの容量確認コマンドは `$ df -h` で確認できます

ただし、現在はHaskellの実行環境として

```
$ sudo yum install epel-release
$ sudo yum --enablerepo=epel install haskell-platform
```

** platformここまで **

---

どうやらplatformよりも、 stack という環境のほうが今はいいらしいのでそれを入れてみる
http://docs.haskellstack.org/en/stable/README/


CentOSのバージョンが 6と7で違ってくる
自分は 6系だったので

CentOSのバージョン確認コマンド
```
$ cat /etc/redhat-release
```

下記コマンドを行う

```
$ curl -sSL https://s3.amazonaws.com/download.fpcomplete.com/centos/6/fpco.repo | sudo tee /etc/yum.repos.d/fpco.repo
$ sudo yum -y install stack
```

`$ ghci` と打って、対話型が起動したら成功
抜ける時は `Ctrl+D` または `:quit`

`$ ghc --version ` でghcのバージョンも確認できる
2016/04/07 時点で 7.0.4だった

$ stack new my-project
  => my-projectというprojectを作る

$ stack setup
  => 必要ならコンパイラをダウンロードする

$ stack build
  => minimal projectをbuildする

$ stack exec my-project-exe
  commandを実行する

---
# 実際にやってみる
---

なんか適当なフォルダを作って

```
$ stack new my-project
$ cd my-project
```

でprojectの作成と移動を行って

まずはちょっとだけ編集する

src/Lib.hs を

```hs
module Lib
    ( someFunc
    ) where

someFunc :: String
someFunc = "Hello, Stack! Haskell!!"
```

として

```
$ stack runghc app/Main.hs
Hello, Stack! Haskell!!
```

となるはず

で、出来上がったアプリケーションをインストールしたい場合は

```
$ stack install
```

とすれば ok




しかしなんかいまいちなので別のチュートリアルをやってみる

https://github.com/commercialhaskell/stack/blob/master/doc/GUIDE.md#stacks-functions

インストールまでは一緒なのでやるとして

### PATHを通す

ひとまず bashrcにpathを通す

```
$ echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc
```

新しいprojectを作る, 最初はsetupやbuildもやってみる(本当に最初だと必要)

```
$ stack new helloworld new-template
$ cd helloworld
$ stack setup
$ stack build     #=> build ファイルを作る
```


これで既に動くようになる

### moduleを追加してみる

まずはmoduleを追加してみる

src/Lib.hs
```hs
module Lib
    ( someFunc
    ) where

import qualified Data.Text.IO as T

someFunc :: IO ()
someFunc = T.putStrLn "Hello world"
```

んで、buildしてみようとするとエラーがでるはず

Could not find module `Data.Text.IO'

みたいなやつ

なので、helloworld.cabalに追加してやる

```
・
・
・
library
  hs-source-dirs:      src
  exposed-modules:     Lib
  build-depends:       base >= 4.7 && < 5
                       -- this next line is the new one
                     , text
  default-language:    Haskell2010
・
・
・
```

<br />

---
# simpleテンプレートのチュートリアル
---

チュートリアルとしては以下のもある
http://seanhess.github.io/2015/08/04/practical-haskell-getting-started.html


stackをインストールして、pathなどの設定は終了しているものとして

```
$ stack new my-project simple
$ cd my-project
$ tree

.
├── LICENSE
├── Setup.hs
├── my-project.cabal
├── src
│   └── Main.hs
└── stack.yaml

```

となる。simpleテンプレートだとappフォルダはなくて、単純なsrcフォルダのみの構成になる


後はチュートリアル通りにやっていく
いくつかつまったところわかりにくいところをメモっていく

Making Changesのところ
greetを追加するところのソースは以下のようになる


```hs Main.hs

module Main where

main :: IO ()
main = do
  putStrLn "hello world"
  putStrLn (greet "bobby")
  putStrLn (greet "World")


greet name = "Hello " ++ name ++ "!"
```


`main = do` のブロックの中に`greet name = "Hello " ++ name ++ "!"`を入れていくのではないので注意




### stack buildは実行ファイルを作成する

```
$ stack build
$ stack exec my-project
hello world
Hello bobby!
Hello World!
```

stack buildコマンドは実行ファイルを作成して
bashなどでpathの設定をしていたら`stack exec ファイル名`コマンドで実行かけることもできる


