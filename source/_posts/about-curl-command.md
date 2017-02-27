title: Particle Electronで使うcurlコマンド
date: 2016-08-14 16:02:29
tags:
- Linux
- 電子工作
---


[particle Electron始めて見たのでメモ](http://yoheikoga.github.io/2016/03/23/particle-Electron%E5%A7%8B%E3%82%81%E3%81%A6%E8%A6%8B%E3%81%9F%E3%81%AE%E3%81%A7%E3%83%A1%E3%83%A2/)

Particle Electronを使ってると、APIを叩こうみたいな場面が出てきて
https://docs.particle.io/guide/getting-started/examples/electron/#use

curlコマンドを使うが使い方をよくわかってないとつらい

APIを叩く時にコマンドライン上でcurlコマンドを打つことが多いみたいなのでメモ
尚、curlの詳しいマニュアル(man)は以下を参照
[curl.1 man ページ](http://www.hcn.zaq.ne.jp/___/unix/curl_manpage.html)

Particle ElectronのAPI詳細については以下を参照
https://docs.particle.io/reference/api/


<!-- more -->

# curlとは

Linuxのコマンドの一つで、HTTPアクセスをしてコンテンツを取得できるコマンド
https://hydrocul.github.io/wiki/commands/curl.html

URLをパラメータにしてそのコンテンツを標準出力させるのが基本的な使い方らしい
つまりただ単にcrulコマンドをするだけ。何もつけないとGETリクエストとして認識されるらしい
ちなみにリダイレクト( > ファイル名)をすれば、ファイル保存も出来る。 -o オプションでも保存可能

ex)
```
$ curl http://www.example.com/ > example.html
```

curlとwgetの違いについては以下に纏められてました（たぶん）
http://postd.cc/curl-vs-wget/

# WebAPIリクエスト時にcurl

APIリクエストを送る時に参考にさせてもらった、よく使うオプションの説明なども丁寧でよい
[curl を使って Web API をテストする(1)](http://lovepeers.org/2014/06/30/curl-web-api/)
[curl を使って Web API をテストする(2)](http://lovepeers.org/2014/07/07/curl-web-api-2/)
[WebAPIリクエスト仕様書としてcurlコマンドのご提案](http://qiita.com/Hiraku/items/dfda2f8a5353b0742271)
[とりあえず覚えた cURL のオプション](http://ichiroku11.hatenablog.jp/entry/2014/03/31/232758)
[cURL を用いた API の使用法](https://access.redhat.com/documentation/ja-JP/Red_Hat_Enterprise_Virtualization/3.0/html/REST_API_Guide/appe-REST_API_Guide-cURL_Integration.html)

# Particle Electronでcurlコマンドを使ってみる

コマンドラインからElectronの情報などをgetしてみよう

## 自分のaccess tokenなどをgetしてみることでオプションなどを使ってみる

```
$ curl https://api.particle.io/oauth/token -u particle:particle -d grant_type=password -d username=joe@example.com -d password=SuperSecret
```

username= のところに自分の登録したメールアドレスを
password= のところにその自分のパスワードを
打ち込んで見ると、 token_typeやaccess_tokenの情報が返ってくる

## curlのオプション

API叩く時によく見かけるオプションについて
http://www.hcn.zaq.ne.jp/___/unix/curl_manpage.html

### -d, --data 
指定したデータをPOSTリクエストとしてHTTPサーバーに送信する
とりあえず、フォームの送信と同様のことをおこなう感じ

Particle ElectronのLED on off をコマンド経由で行う場合があって
やり方のひとつに、curlコマンドを打ち込む方法がある
例えば以下のようになる

device IDが0123456789abcdefで
access_tokenが123412341234だった場合
(IDとaccess_tokenの調べ方は[前回](http://yoheikoga.github.io/2016/03/23/particle-Electron%E5%A7%8B%E3%82%81%E3%81%A6%E8%A6%8B%E3%81%9F%E3%81%AE%E3%81%A7%E3%83%A1%E3%83%A2/)参照)

```
curl https://api.particle.io/v1/devices/0123456789abcdef/led \
  -d access_token=123412341234 \
  -d params=on
```

APIのURLに対して、POSTリクエストでaccess_token(認証に必要)と実行するパラメータ(LED on なら params=on, offなら params=off)を投げかける

### -X
使用する要求のコマンド。-Xに続けて GET POST PUT DELETEのいずれかを使用する
ex)
```
$ curl -X POST https://api.particle.io/oauth/token -u particle:particle -d grant_type=password -d username=joe@example.com -d password=SuperSecret

# usernameにメールアドレスを、passwordにパスワードを入れる
# 例えば example@email.com, password=12345678だとしたら curl -X GET https://api.particle.io/v1/access_tokens -u example@email.com:12345678 となる
$ curl -X GET https://api.particle.io/v1/access_tokens -u username:password
```

### -i
レスポンスヘッダを見たい時に使う


ひとまず以上、もう少し勉強すべし






