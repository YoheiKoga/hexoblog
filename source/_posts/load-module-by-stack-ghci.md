title: stack ghciで読み込まれるファイルはcabalファイルで設定する
date: 2016-08-24 10:09:58
tags:
- Haskell
---

関数型プログラムを触ってみたかったのでHaskellをやってみている
[CentOSでHaskellプログラミング始め](http://yoheikoga.github.io/2016/08/14/haskell-on-centos/)

で、[すごいH本](https://www.amazon.co.jp/dp/B009RO80XY/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)を読み終えたので次に[関数プログラミング実践入門 ](https://www.amazon.co.jp/%E9%96%A2%E6%95%B0%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AE%9F%E8%B7%B5%E5%85%A5%E9%96%80-%E2%94%80%E2%94%80%E7%B0%A1%E6%BD%94%E3%81%A7%E3%80%81%E6%AD%A3%E3%81%97%E3%81%84%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%9F%E3%82%81%E3%81%AB-WEB-PRESS-plus/dp/4774169269)
を読んで見てる


で、第三章の関数定義のところでghciにファイルをロードするところがでてくるが

Haskellを使うのにstackを使っていると `stack ghci`とすることで、プロジェクトのディレクトリからhsファイルをロードすることができるのだが
そのままだと、基本的には最初から生成されているMain.hsがロードされる


(stackは以下qiitaの記事がわかりやすいかと)
[Haskellのビルドツール"stack"の紹介](http://qiita.com/tanakh/items/6866d0f570d0547df026)
[Stackでやる最速Haskell Hello world! (GHCのインストール付き！)](http://qiita.com/igrep/items/da1d8df6d40eb001a561)


## 例えばSample.hsをロードしたい場合
--- 

Main.hsではなく、自分で作成したファイル(例えば projectDir/src/Sample.hs)をロードしたいと思ったら
projectDir/projectName.cabal のcabalファイルを修正してやればよい

例えば、`stack new projectName simple`で新しいプロジェクトを作ると、cabalファイルは以下のようになる

```
name:                projectName
version:             0.1.0.0
synopsis:            Simple project template from stack
description:         Please see README.md
homepage:            https://github.com/githubuser/third-project#readme
license:             BSD3
license-file:        LICENSE
author:              Author name here
maintainer:          example@example.com
copyright:           2016 Author name here
category:            Web
build-type:          Simple
cabal-version:       >=1.10

executable projectName
  hs-source-dirs:      src
  main-is:             Main.hs
  default-language:    Haskell2010
  build-depends:       base >= 4.7 && < 5
```


この時、executable projectNameの `main-is:` がMain.hsになっているので
ここを`Sample.hs`としてやって、srcディレクトリに自分でSample.hsファイルを作成してやれば

`stack ghci`コマンドをした時にSample.hsがロードされる


まぁ、こういうことしなくてもコンソールでghciする際にロードするファイルを引数で指定してやればいいだけなのはあるが

`ghci src/Sample.hs`

cabalファイルの中身を知っておくのも良いかと思う