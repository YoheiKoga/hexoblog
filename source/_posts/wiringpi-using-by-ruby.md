title: Rubyでwiringpiを使う
date: 2016-08-14 16:27:04
tags:
- RaspberryPi
- Ruby
---


## wiringPiでPWM制御する
まずはLEDでやってみるといい(LEDの明暗コントロール)
下記サイト参考
http://lchikaamazon.hatenablog.com/entry/2013/11/18/191031

用いたもの

RaspberryPi2
LED
抵抗(470Ω)
ジャンパー線2本
その他キーボードなど


以下は、Raspbian Jessieの場合は最初からWiringPiが入っているので
やらなくて大丈夫です(むしろやるとバージョン違いの読み込みエラーとか起きるかも？)

WiringPiが入ってるかどうかはコンソール上で

```
$ gpio -v
gpio version: 2.32
Copyright (c) 2012-2015 Gordon Henderson
This is free software with ABSOLUTELY NO WARRANTY.
For details type: gpio -warranty
...

```

みたいなのが出てきたら大丈夫です
入ってなかったらたぶん「そんなコマンドないよ」みたいなのが出てくるはず

**--------------------------ここから**
**--------------------------**
wiringpiを使用するためにはちょっと手順があるので記載
下記も参考に
https://projects.drogon.net/raspberry-pi/wiringpi/download-and-install/

```
$ sudo apt-get update
$ sudo apt-get upgrade
```
なければgit-coreもインストール

```
$ sudo apt-get install git-core
```

gitからwiringpiをクローン
ディレクトリは適宜自分で作成してても可

```
$ git clone git://git.drogon.net/wiringPi
$ cd wiringPi
$ git pull origin
$ ./build
```
でビルドする

**--------------------ここまではRaspbian Jessieの場合はやらなくて大丈夫**
**--------------------**
次に、RubyでWiringPiを使うためのgemを入れる
下記を参照にした
[GPIO control on Raspberry Pi with Ruby](http://blog.gaku.net/gpio-control-on-raspberry-pi-with-ruby/)


ruby-devを入れておかないとどうもgem installがうまくいかなかったので入れます
```
$ sudo apt-get install -y ruby-dev
$ sudo gem install wiringpi
```

でwiringpiのgemがインストールされます

###スクリプトを書く

wiringpiでのピン番号は、GPIOの番号や順番のピン番号とは異なっているので注意
下記参照
[WiringPi Pins](http://wiringpi.com/pins/)

例えば後述のスクリプト例ではWiringPiの1ピンを用いています
これは、GPIO表記だとGPIO18, 普通のピン番号(3.3V出力を1番ピンとして順番に数字をふっているもの)だと12番ピンのことです

下のように配線してあげました
GNDは6番ピンを使ってます

<img src="/2016/04/15/RubyでWiringPiを使う/RaspberryPi.jpg" width="300px" height="200px" alt="RaspberryPi">


### 実際にLチカをやってみる
下記スクリプトを適当な名前で保存して、sudoをつけて実行すれば明暗点滅Lチカが出来ます
ファイル名は test.rb にしてます
(test.rb)
```ruby
require 'wiringpi'

LED = 1
io = WiringPi::GPIO.new

io.soft_pwm_create(LED,0,100)
  10.times do
    (0..100).each do |x|
      io.soft_pwm_write(LED,x)
      sleep(0.005)
    end
    (0..100).each do |x|
      io.soft_pwm_write(LED, (100-x))
      sleep(0.005)
    end
end
```


WiringPiを扱う時は基本的にはルートユーザーである必要があるらしいので
sudo をつけてrubyを実行してあげます

```
$ sudo ruby test.rb
```

以上、明暗点滅が出来ました
