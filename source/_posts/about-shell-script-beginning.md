title: shellスクリプトについて調べてみた(初心者)
date: 2016-08-14 15:52:22
tags:
- shell
---

# シェルスクリプト

シェルスクリプトとかシェルコマンドというのは、要するには「黒い画面」に対してコマンドを実行するやつ

例えばMacだとターミナル、Linuxだとコンソール画面
のことになるのかな
(UNIXシェルって書いてあるんで、windowsだとどうなるんだろ？？？)

### シェルコマンド
シェルコマンドは、自分で一行ずつ実行するコマンドで、例えば

$ cd ~/

と打てばルートディレクトリに移動するし

$ mkdir hogehoge

とすれば、hogehogeというディレクトリ(いわゆるフォルダ)が作成される

### シェルスクリプト
シェルスクリプトというのは、このコマンドをまとめてファイルにしたものです

まずスクリプトとは、
> スクリプトとは、メモ帳などのテキスト エディタで作成し、特定のファイル拡張子を付けて保存したプレーン テキスト ファイルです。
(引用: https://technet.microsoft.com/ja-jp/scriptcenter/ff576118.aspx )

とありますし、いわゆるファイルのことです

例えばbashスクリプトで、hogehogeディレクトリを作成してさらに ls コマンドで現在のディレクトリ内容を表示させるようにしたい場合は

shelltest.sh
```sh
#!/bin/bash

mkdir hogehoge
ls
```

というファイルを作成して、下記chmodコマンドで実行権限を付与した後
```
$ chmod +x shelltest.sh
```

コマンドラインで実行してやると結果が表示される

```
$ ./test.sh
hogehoge test.sh
$ 
```

のようになるやつである
これも立派なシェルスクリプトである(自分もまだよくわかってないけど)

こういうのを、foreachやifを使って制御したスクリプトを書くことで
時間短縮にもっていけたりするので慣れると便利です

例えば、RubyやPythonなど(に限らないですが)の環境構築を行う場合に
インストールしていくものが多いので、コマンドラインで手打ちしていくと
時間もかかるし間違える場合もあったりしますが
シェルスクリプトを準備しておくと、自動化できてとても楽です
(パッチ処理っていうのかな)


# sh?bash?zsh?

元来シェルはshという1種類しかなかったとのこと
http://itref.fc2web.com/unix/shell.html

しかしそこはプログラミング言語と同じで、色々な機能をもたせた亜種が登場したみたいです

# 初心者はどのシェルを使えばよいのか？

自分はRubyやPython、さらにRaspberryPiを良く使うのですが、よく見かけるのは
bashが多い気がします
また、Macの標準ログインシェルはbashみたい
http://blog.tokoyax.com/entry/zsh/bash-to-zsh

だから記事もbashが多いのか

設定も.bash_profileに設定しているのが多い気がします
日本語だと検索して出てくる記事もbash_profileが多いので
最初慣れない内は、bashを利用して、設定も.bash_profileに記載していくのがいいんじゃないでしょうか(という感想)
pythonの記事だとzshのものもちょくちょくみかけます

どうも調べてみると、zshは玄人向けでとても機能が豊富なシェルみたいです
[ヾ(oﾟωﾟo)ﾉﾞbashからzshに移行して2014年を迎えよう！[ログインシェル]](http://qiita.com/harapeko_wktk/items/47aee77e6e7f7800fa03)
[なぜzshを使うのか](http://qiita.com/mollifier/items/0b618f9e969193ccf5c8)


# .bash_profile? .bashrc?

bashを使う場合
先ほど設定は.bash_profileに記載すると言いましたが
.bashrcに設定している記事も見かけます

こういうの素人だとわけわからないしコピペしてみてるから動かないし・・・

ひとまず
.bash_profile : ログイン時に一度設定すればいいもの
.bashrc : 起動するたびに設定する必要のあるもの

ということみたいです、下記参照
[.bash_profile ? .bashrc ? いろいろあるけどこいつらなにもの？](http://qiita.com/hirokishirai/items/5a529c8395c4b336bf31)
[.bash_profileと.bashrcの違いが覚えられないあなたへの処方箋](http://dqn.sakusakutto.jp/2012/08/bash_profile_bashrc.html)

普段使用する分には、あまり気にしなくて良いみたい(そもそもそんなに使わない人だと違いが生じない？)




