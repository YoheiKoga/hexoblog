title: RaspberryPiにnode.jsを導入する
date: 2016-04-24 12:21:59
tags:
- node
- RaspberryPi
---

## 結局nodebrewを使うのが楽そう

node.jsを入れるのには、nvmやnodebrewなどバージョンやパッケージを管理してくれるツールを
使うのがいいのだけどRaspberryPiでもLinuxでもMacでも、nodebrewが一番楽(だと思ってる)ので

nodebrewをインストールしてから、node.jsをインストールしてやる
ラズパイのOSはRaspbian wheezyを使用してる. jessieでも大丈夫だと思われる

```
$ sudo curl -L git.io/nodebrew | perl - setup
$ sudo echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.bashrc
$ source .bashrc
# バージョンの確認ができたら成功
$ nodebrew -v
nodebrew 0.9.5
...

# 使えるnode.jsのバージョン確認
$ nodebrew ls-all
...

# ひとまずversion 5系の2016年4月時点最新版を入れてみる
$ nodebrew install-binary v5.11.0
...

$ nodebrew use v5.11.0

# nodeコマンドやnpmコマンドでバージョンが確認出来たら成功
$ node -v
v5.11.0
$ npm -v
3.8.6
```


以上、簡単にnode.jsを導入することができた
