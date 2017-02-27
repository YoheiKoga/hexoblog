title: RaspberryPi, WebIOPi, HomebridgeでiphoneからLED操作
date: 2016-08-14 15:56:59
tags:
- RaspberryPi
---


# RaspberryPiのGPIOピンをスマホから操作してみる




# WebIOPi

RaspberryPiのGPIOを制御できるライブラリ、さらにREST APIも同時に制御できるとのこと(つまりWeb上から制御できる)
http://todotani.cocolog-nifty.com/blog/2016/01/homebridgeraspb.html
http://www.hiramine.com/physicalcomputing/raspberrypi/webiopi_install.html

インストール方法
2016年3月24日現在最新版は 0.7.1みたいなのでそれに合わせた
0.7.1はRaspberry Pi 2には、単純インストールは対応してないみたいなので下記リンクなどを参考にインストールすること
http://deviceplus.jp/hobby/raspberrypi_entry_030/

<!-- more -->

```
$ wget http://sourceforge.net/projects/webiopi/files/WebIOPi-0.7.1.tar.gz
$ tar xvzf WebIOPi-0.7.1.tar.gz
$ cd WebIOPi-0.7.1
$ sudo ./setup.sh
```

途中、yes / no を聞かれるのが2回あるのでひとまず y キーをエンターする

```
Sign-up for free at http://developer.weaved.com then enter your Weaved login: 
```

みたいなのがでてくるので
http://developer.weaved.com
でsign up しておく

登録したメールアドレスがlogin IDで、パスワードを入力するとなんかインストールが始まって、終わると

```
We will now register your device with the Weaved backend services. 
Please provide an alias for your device: 
```

と出てくるので適当な名前をつける(aliasは名前という意味なので、意訳すると「あなたのデバイスの名前は何にしますか？」みたいな感じ)
raspiqps
と名づけてみた

少し待つと、登録が終わってインストール成功のため以下のようなのが出てくる

```
WebIOPi successfully installed
* To start WebIOPi foreground : sudo webiopi [-h] [-c config] [-l log] [-s script] [-d] [port]

* To start WebIOPi background : sudo /etc/init.d/webiopi start
* To start WebIOPi at boot  : sudo update-rc.d webiopi defaults

* Weaved IoT Kit installed, log on http://developer.weaved.com to access your device

* Look in /home/pi/download/WebIOPi-0.7.1/examples for Python library usage examples

```

これでひとまずはセッティングok


## 次にRaspberryPi側のセッティング
Node.jsとHomebridgeのインストールをする
Node.jsはC++14のコンパイル環境が必要だが、RaspberryPiのデフォルトはgcc4.6?みたいで対応していないので
まずはそこからインストールする

RaspberryPiのosが wheezyの場合は、apt-getの依存性をjessieに変更してやる必要がある
https://solarianprogrammer.com/2015/01/13/raspberry-pi-raspbian-install-gcc-compile-cpp-14-programs/
を参考にして

```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo vim /etc/apt/sources.list
```

これで、一行目のwheezyのところをjessieに変更して保存する

/etc/apt/sources.list
```
deb http://mirrordirector.raspbian.org/raspbian/ wheezy main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspbian.org/raspbian/ wheezy main contrib non-free rpi

となっているので、一行目を変更する

deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspbian.org/raspbian/ wheezy main contrib non-free rpi
```



変更後
```
$ sudo apt-get install gcc-4.9 g++-4.9
```

でインストールが開始される(結構時間がかかった)


終わったら、再び依存性をwheezyに戻す

```
$ sudo vim /etc/apt/sources.list

以下のように直す
deb http://mirrordirector.raspbian.org/raspbian/ wheezy main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://archive.raspbian.org/raspbian/ wheezy main contrib non-free rpi

:wqで保存

$ sudo apt-get update
```

で環境をwheezyのやつで整える

この後、
```
$ g++-4.9 -v
```

とやればバージョン確認出来るのだが少し手間なので
デフォルトのコンパイラとして使うためにちょっと手直ししてやる
https://github.com/nfarina/homebridge/wiki/Running-HomeBridge-on-a-Raspberry-Pi

簡単で、コマンドを以下のようにうつだけ
```
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 60 --slave /usr/bin/g++ g++ /usr/bin/g++-4.9
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.6 40 --slave /usr/bin/g++ g++ /usr/bin/g++-4.7
```

これで、以下のコマンドをうってgccのバージョンが表示されたら成功
```
$ g++ -v

...
gcc version 4.9.2 (Raspbian 4.9.2-10) 

```

出来なかったら、下記ページを参考にする
http://lektiondestages.blogspot.jp/2013/05/installing-and-switching-gccg-versions.html


次にNode.jsをインストールします
## Node.jsインストール

node.jsは4.x系と5.x系があって、どちらも実はどんどんバージョンが更新されているのですが
開発の都合で２つ同時に進んでるみたいです

[Node.js 5.0がリリース。奇数バージョンは最新機能版、偶数バージョンは長期サポート版](http://www.publickey1.jp/blog/15/nodejs_50.html)

よくわからなかったら、長期サポート板である4.x系を使うのが良いのではないでしょうか


というわけで、2016年3月24日時点での4.x系最新バージョンのv4.4.1 を入れてみます
自分はRaspberryPi A+ で試しているので、CPUがARMv6ですので以下のようになります
```
$ wget https://nodejs.org/dist/v4.4.1/node-v4.4.1-linux-armv6l.tar.gz
$ tar -xvf node-v4.4.1-linux-armv6l.tar.gz
```

Raspberry Pi2の場合は、CPUがARMv7なので以下のようになります
```
$ wget https://nodejs.org/dist/v4.4.1/node-v4.4.1-linux-armv7l.tar.gz
$ tar -xvf node-v4.4.1-linux-armv7l.tar.gz
```


ひとまず、解凍されたnodeのディレクトリに移って
ファイルをコピーします
```
$ cd node-v4.4.1-linux-armv6l/
$ sudo cp -R * /usr/local/

# 以下コマンドでnodeのバージョンを確認できたらok
$ node -v
v4.4.1
```

次にAvahiをインストールします
## Avahiインストール

AvashiはLinuxのmdsnと呼ばれるものみたいです(いまのところ僕はよくわかってません)
が必要なので入れます

```
$ sudo apt-get install libavahi-compat-libdnssd-dev
```

以上です、簡単


## Homebridgeをインストール
https://github.com/nfarina/homebridge

Homebridgeをインストールする
```
$ sudo npm install -g homebridge
```
けっこう時間かかる

さらに、httpベースのpluginをインストール
```
$ sudo npm install -g homebridge-http
```

次に、pluginのためのconfigファイルを作成する
(下のほうに記載してますが、参考サイト通りに下記方法でやってみたけどうまくいかなかった・・・)
(さらに下の方に追記してますが、 homebridge-gpioというのを使って一応LEDの操作できました！)



```
$ sudo vim /home/pi/.homebridge/config.json

# 以下を記述


{
    "bridge":
    {
       "name": "Homebridge",
       "username": "CC:E1:D5:15:9D:AE",
       "port": 51826,
       "pin": "031-45-154"
    },
 
    "accessories": [
    {
        "accessory": "Http",
        "name": "test",
        "switchHandling": "yes",
        "http_method": "POST",
        "on_url":      "http://localhost:8000/GPIO/17/value/1",
        "off_url":     "http://localhost:8000/GPIO/17/value/0",
        "status_url":  "http://localhost:8000/GPIO/17/value",
        "service": "Light",
        "brightnessHandling": "no",
        "brightness_url":     "http://localhost/controller/1707/%b",
        "brightnesslvl_url":  "http://localhost/status/100054",
        "sendimmediately": "",
        "username" : "webiopi",
        "password" : "raspberry"                     
     }
  ]
}

```

bridgeの username はRaspberryPiのMacアドレス名を登録する
Macアドレスは
```
$ ifconfig | grep 'HWaddr'
```

とすると出てくるHWaddrのことです
ちなみに、HWaddrは小文字の英字が出てくるかもしれないですが
config.jsonに記載する時は大文字になおしてあげないといけないです


あと別に設定しなくても動くが
RaspberryPiが立ち上がった時に、Homebridgeを実行できるように設定しておくと便利なので
その設定をする
https://github.com/nfarina/homebridge/wiki/Running-HomeBridge-on-a-Raspberry-Pi

```
$ sudo vim /etc/init.d/homebridge

#!/bin/sh
### BEGIN INIT INFO
# Provides: homebridge
# Required-Start:    $network $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start daemon at boot time
# Description:       Enable service provided by daemon.
### END INIT INFO

dir="/home/pi"
cmd="DEBUG=* /usr/local/bin/homebridge"
user="pi"

:wq

$ sudo chmod 755 /etc/init.d/homebridge
$ sudo update-rc.d homebridge defaults
```



## Homekit用のアプリをインストールする

iphoneにHomekit用のアプリをインストールする
ひとまず Insence+というアプリをAppストアからインストールした

メールアドレスとパスワードを登録した
デバイスの登録などは下記を参照
https://datahotel.io/archives/725




この方法ではできなかった・・・
よくわからないけど、homebridge-gpioのほうがよいのではないか
ということで、homebridge-gpioを使った方法でチャレンジ

# 以下追記分、こちらの方法でできました

## homebridge-gpioを使ってみる

homebridge-gpio
https://www.npmjs.com/package/homebridge-gpio

homebridgeは上記方法でインストール済みとしておいて
homebridge-gpioとgpo-adminをインストールする

```
$ sudo npm install -g homebridge-gpio
$ git clone git://github.com/jamesblanksby/quick2wire-gpio-admin.git
$ cd quick2wire-gpio-admin
$ make
$ sudo make install
$ sudo adduser $USER gpio

```

config.jsonも書き換えてやる
GPIO17(ラズパイの11ピン)を用いてLチカするので下記構成になってます
```
$ sudo vim /home/pi/.homebridge/config.json


{
    "bridge":
    {
       "name": "Homebridge",
       "username": "CC:E1:D5:15:9D:AE",
       "port": 51826,
       "pin": "031-45-154"
    },

    "accessories": [
    {
        "accessory": "GPIO",
        "name": "GPIO17",
        "pin": 11,
        "username" : "webiopi",
        "password" : "raspberry"
    }
  ]
}

```

としてやる。

これで、homebridgeを起動してやると、iphoneのinsence+アプリからgpioのon offが出来る
(GPIOピンは17を用いた)


ただ、この方法でやっても、offの時にLEDが点灯してしまうので
何か方法があるはずだが・・・ひとまずうまくいったから置いておこう























