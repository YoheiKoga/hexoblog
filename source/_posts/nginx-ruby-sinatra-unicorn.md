title: Nginx + UnicornでRaspberryPiのsinatraアプリを動かしてPCブラウザで確認
date: 2016-09-20 12:02:27
tags:
- Ruby
- Nginx
- RaspberryPi
---


Nginxの練習をRaspberryPiで行ってみる

ついでなので、sinatraアプリを作ってPCのブラウザで確認してみる

<!-- more -->

---
# Nginxとは
---

>nginx（「エンジンエックス」と発音[3]）は、フリーかつオープンソースなWebサーバである。

[引用: nginx wikipedia](https://ja.wikipedia.org/wiki/Nginx)

webサーバとしてはApacheが有名だが

nginxはオープンソースでありそれに置き換わるものらしい

なんとなく触っておくことにした

<br>

---
# webサーバーのnginx入れてみる
---

RaspberryPiでNginxを動かしてみる

とりあえずruby + sinatraで動かしてみる

<br>

---
# nginxの導入
---
ひとまずRaspberryPi公式サイトにあったのでやってみる

https://www.raspberrypi.org/documentation/remote-access/web-server/nginx.md

下記も参考になる

http://igarashi-systems.com/sample/translation/raspberry-pi/remote/remote-web-server-nginx.html

入れるのは簡単で

### nginx導入とstart

インストール

$ sudo apt-get install nginx

スタートさせる

$ sudo /etc/init.d/nginx start

適当にパソコンのブラウザでRaspberryPiに設定したIPアドレスでアクセスする

IPアドレスの調べ方はifconfigコマンドでok, 固定IPにしてると楽

http://raspberrypi-ip-address

これだけ

Nginxのページが表示されたら成功

簡単なので次にsinatraを動かせるか調べる

<br>

# 色々な用語が出てくる

## rails nginxで検索したところ

なんかNginxで調べてるとgem passengerって検索で出てくるけどこれはなんだろう

↓

>Phusion Passengerは、RailsをApache（WEBサーバー）上で動かすためのApacheモジュール。

(引用: http://d.hatena.ne.jp/MonteCut/20111114/1321280563)


どうやらRailsアプリの実行環境の一つみたい

ここのgem fileのとこに簡単な説明がある

https://rubygems.org/gems/passenger/versions/5.0.28

<br>


## sinatra nginxで検索したところ

ThinとUnicornというのが出てくる


### Unicorn

HTTPサーバーのこと？
http://blog.designrecipe.jp/2011/07/30/unicorn/

>Rails、Rack アプリケーションを動作させるコンテナとしては、Passenger、Thin、Mongrel などの選択肢がある。 それ以外にも Unicorn という Rack アプリケーション向けの HTTP サーバがあり

(上記リンク引用)

PassengerもThinもUnicornもHTTPサーバーらしい


Unicornが情報が多めで、どうやら早いみたいなのでそれでやってみる


<br>

---
# とりあえずunicornでやってみる
---


下記リンクを参考にした
http://hayo0914.hatenablog.com/entry/2014/09/15/163648

http://blog.designrecipe.jp/2011/07/30/unicorn/


### 適当なディレクトリを作成して, bundle initしてGemfileを生成

Gemfileにsinatraとsinatra-contribとunicornを追加する

```rb Gemfile
source 'https://rubygems.org'

gem "sinatra"
gem "sinatra-contrib"
gem "unicorn"
```

まずは動作環境の確認として

適当にappディレクトリにrackで起動させるためにconfig.ruファイルを作る

```rack app/config.ru
require 'rubygems'
require 'sinatra/base'
class HelloApp < Sinatra::Base
  get '/hello' do
    'Hello World'
  end
end
run HelloApp
```


自分はrackupした時に下記エラーが出たので

Unable to activate sinatra-1.4.7, because rack-2.0.1 conflicts with rack (~> 1.5) 

下記リンクを参考にして解決した

http://mitsuakikawamorita.com/blog/?p=3135

rackが二種類入ってて競合したためらしいのでリンク先通りに新しいほうのrackをuninstallした


で、rackupでスタートさせる

```
$ rackup app/config.ru -o 0.0.0.0
```

これで適当なPCのブラウザから
RaspberryPiのipアドレスとポート番号9292、/helloのところにアクセスすればよい

http://raspberrypi-ip-address:9292/hello


接続できてHello Worldが表示されたら成功


<br>

---
# Unicornを使う
---

### Gemfile

unicornを追加して以下のようになる

```rb Gemfile
source "https://rubygems.org"
gem "unicorn"
gem "sinatra"
gem "sinatra-contrib"
```

### 設定

Unicornはunicornコマンドを使って起動するらしい

まずは[参考リンク通り](http://hayo0914.hatenablog.com/entry/2014/09/15/163648)にやってみる

app/unicorn.confに以下を記述

```sh app/unicorn.conf
worker_processes 10
listen '/tmp/unicorn.sock'
stderr_path File.expand_path('unicorn.log', File.dirname(__FILE__))
stdout_path File.expand_path('unicorn.log', File.dirname(__FILE__))
preload_app true
```


### nginxと連携

/etc/nginx/nginx.confに連携のコードを記載する

upstreamもserverも、nginx.confに最初から記述されている http{} の中に記述することに注意

また、 `your-rasberrypi-ip-address` のところは自分のRaspberryPiで設定した固定IPアドレスなどを記述すること

例えば固定IPが 192.168.1.200 とかだったらそれを記述する

```conf /etc/nginx/nginx.conf

http {

    ...

    upstream unicorn {
        server unix:/tmp/unicorn.sock;
    }
    server {
        listen       80;
        server_name  your-raspberrypi-ip-address;

        location ~ \.rb$ {
            proxy_pass http://unicorn;
        }
    }

}
```

で、nginxを再起動させる

```
$ sudo /etc/init.d/nginx reload
```


### サーバを起動

Gemfileにgemを記述してbundle installしたので、下記コマンドでunicornを起動させる

```
$ bundle exec unicorn -c unicorn.conf -D
```

### ログファイルの確認

unicornはデフォルトでconfig.ruをアプリケーションのエントリポイントとして認識するらしい

つまり、起動時にconfig.ruが前出のunicornコマンドによって読み込まれているかログで確認する

```
$ cat unicorn.log
```

エラーが表示されていなくて、参考リンクみたいにworkerプロセス2つ、masterプロセス2つ

が立ち上げっていれば良い


<br>

# アプリを動かす

通常ではnginxが自動で立ち上がると思っていたがそのままブラウザでアクセスすると

http://your-raspberrypi-ip-address:8080

なんか接続できないと言われた

http://your-raspberrypi-ip-address

にアクセスしてみると

502 Bad Gateway
と表示されてしまった

それはただ単にunicornが起動していないだけ？みたい

http://qiita.com/mdew150/items/afaec692f2562a9b381d


なので、unicorn.confがあるディレクトリで下記コマンドを実行すると、サーバーが立ち上がって
$ bundle exec unicorn


http://your-raspberrypi-ip-address:8080/hello


などにアクセスするとconfig.ruに記述したHello Worldが表示されると成功である

(your-raspberrypi-ip-addressのところは自分のRaspiのIPアドレスをいれる)

