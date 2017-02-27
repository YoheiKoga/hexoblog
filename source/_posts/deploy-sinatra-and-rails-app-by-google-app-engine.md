title: Google App EngineでsinatraとRailsをデプロイ
date: 2016-08-14 16:44:36
tags:
- Ruby
- Rails
- Sinatra
---


[RubyがGoogle App Engineで利用可能に。ベータ公開開始](http://www.publickey1.jp/blog/16/ruby_google_app_engine.html)

というのを見かけて、Ruby使えるのかじゃあSinatraやRailsも動くのかな？と思って調べたら既に先人がいたので倣ってやってみた


自分のマシンはMacで行った
Rubyのversionは2.3.0, railsは4.2.5

<!-- more -->

# Sinatraアプリ

まずは下記参考ページ通りにsinatraアプリをデプロイしてみる
参考
[Sinatraで試すGoogle App Engine / Ruby](http://qiita.com/ma2saka/items/4da532d621694dc00ead)

## Google App

公式サイト通りにインストールする
https://cloud.google.com/sdk/?hl=ja#download


```
$ curl https://sdk.cloud.google.com | bash
$ gcloud init
```

`To continue, you must log in. Would you like to log in (Y/n)? Y`と聞かれるのでYでyesを選択

ブラウザが開くのでgoogleのアカウントでログイン


作成しているprojectがリストアップされるので選択する
projectが一つの場合は自動的にそれが選択される

もしGoogle Compute Engine APIをenableにしていたら次にどのCompute Engine zoneを使うか聞かれるらしい
(自分は使っていない)

これでgcloudを使う準備は整った！


あとはqiitaのサイト通り
app.yaml
Gemfile
config.ru

を作成して必要なコマンドを叩けばファイル構成は完成

そこまで出来たら
```
$ gcloud preview app deploy
```

とすることでデプロイが出来る
最初はちょっと時間がかかる(10分ぐらい？)

あとは、最後に
Deployed service [default] to [https://xxxxxxxxxxx.appspot.com]

みたいなのがコンソールに出てくるのでそのURLにアクセスすれば動いているのが確認できた！


# Railsをやってみる

参考
[Google App EngineでRailsを動かしてみる](http://qiita.com/imaimiami/items/e7e5bdce1cd493d17016)

[Hello Worldのデプロイ（Google App EngineとRuby on RailsでHello World）](http://qiita.com/kaorumori/items/3ccadd9e958ae0e46282)

[Ruby on Rails を Google App Engine 上で動かしてみる](http://blog.toshimaru.net/ruby-on-google-app-engine/)

Google App Engineの[コンソール](https://console.cloud.google.com)から先ほどのsinatraアプリのプロジェクトとは別のプロジェクトを作成しておく

自分のパソコンの適当な場所に
Railsのプロジェクトディレクトリを作ってrails newなどしておく

```
$ mkdir rails-app
$ cd rails-app
$ rails new .
$ rails g controller hello_world
```

hello_world_controller.rbが作成されるので
コードを書く

```rb /app/controllers/hello_world_controller.rb
class HelloWorldController < ApplicationController
  def index
    render text: "Hello Rails on GAE!"
  end
end
```

```sh /config/routes.rb
#下記を追加
root 'hello_world#index'
```


できたらgcloud initする
```
$ gcloud init
```

次に、作成したアプリのフォルダ(今回だとrails-app)にapp.yamlを作成する
app.yamlの中身は

```sh rails-app/app.yaml
runtime: ruby
vm: true
entrypoint: bundle exec rackup -p 8080 -E production config.ru
```

とした

このままデプロイすると、デプロイは出来るが
なんか"product環境なのにSECRET_KEYがありません"みたいなのが出るので記述してあげる

まずはSECRET_KEYの作成コマンドとして下記を実行

```
$ bundle exec rake secret
```

でSECRET_KEYを作成してコピーしておく
(長い数字とアルファベットの文字列が生成される)

それを、rails-app/config/secrets.ymlに記述する
最初、下記のようになってると思われるので

```sh rails-app/config/secrets.yml
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

たとえば`bundle exec rake secret`で出てきた文字列が

abcdefghijklmnopqrstuvwxyz123456789

だとしたら(もちろん本当はもっと長いし複雑)

```sh rails-app/config/secrets.yml
production:
  secret_key_base: abcdefghijklmnopqrstuvwxyz123456789
```

としてやる

で、デプロイ
```
$ gcloud preview app deploy
```

途中の選択肢は Y (yes)にしておいて
しばし待ちます

で、出てきたURLにアクセスして無事

Hello Rails on GAE!
が表示されたら成功

