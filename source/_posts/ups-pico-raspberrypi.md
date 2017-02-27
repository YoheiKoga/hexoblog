title: RaspberryPiバックアップバッテリーUPS PIcoの使い方
date: 2016-08-14 16:34:38
tags:
- RaspberryPi
---


ラズパイを使用していて一番困るのが、いきなり電源が落ちた時などに
ファイルシステムが破損する恐れがあること

そこで、突然の電源停止時にも安全に電源を落とせるようなバックアップとしてのデバイスがUPS PIco
[ラズベリーパイ / Raspberry Pi 専用 UPS 無停電電源ボードUPS PIco](http://www.furutaka-netsel.co.jp/utility/upspico.html)

基本的には下記リンク公式のsimple Setting Guideを参考にやっていけばいける
(PDF注意)
[Simple Setting Guide for the UPS PIco](http://pimodules.com/_pdf/Simple_Setting_Guide_for_the_UPS_PIco.pdf)


いろいろレジスタなどいじれるがひとまず使ってみる

2016年4月26日現在
使用している環境は

・RaspberryPi2 B
・OS: Raspbian Jessie(2016-02)
・UPS PIco (HAT v1.1)

<!-- more -->

---
## UARTとI2Cの設定
---


UPS PIcoはRaspberryPiとの接続で
UARTとI2Cを使用するので使用可能な状態に設定してやる

まずはI2Cに必要なライブラリをapt-getでインストールする

```
$ sudo apt-get install i2c-tools libi2c-dev python-smbus
```

次に設定を行う

設定はRaspberryPi Jessieの場合簡単で

```
$ sudo raspi-config
```

でラズベリーパイの設定画面から
Advanced optionsを選択して

Serialをdisableにする(最初の選択肢で "NO" を選択してやればよい)
=> SerialコンソールがdisableになるのでUARTとしてGPIOを使用可能になる

I2Cをenableにする(最初の選択肢で "YES" を選択してやればよい)

これで再起動したら設定が反映されます

UARTが使用できるかどうかはcuコマンドやminicomなどで(後述)
I2Cが使用できるかどうかはi2cdetectコマンドなどで(後述)
確認可能

---
## UPS PIcoをラズパイにはめる
---

manualの手順にはひとまず簡単な文章だけあったが流れとしては

1. UPS PIcoにバッテリーをはめる
2. Raspiにのせる
3. Raspiの電源をいれる

だけです

RaspberryPiの電源を入れた状態でUPS PIcoを乗せるのはちょっと危なそうなので
必ずラズパイは電源offの状態で乗せるように

外す場合は、HV1.1(バージョンみたいなもの？手持ちのやつはこれだった)の場合、RaspberryPiの電源ケーブルからの
電力はoffにした状態で、UPS PIcoのUPRSボタンを1秒以上押せば安全に取り外せる状態になる

UPSのバージョンは購入したら箱の裏側に HV1.1みたいなのが書いてあると思われるのでそれで確認


---
## PIcoとシリアル通信してみる
---

シリアル通信で接続を確認してみる
マニュアルではminicomを使用してるが自分はよくcuコマンドを使っているので
それを使う

cuコマンドが入っていない場合は下記コマンドでインストール
```
$ sudo apt-get install cu
```

実際にシリアル通信してみる
```
$ cu -s 38400 -l /dev/ttyAMA0
```

これでUPS Picoと接続できたか確認できるので
後はResetボタン(UPSR: UPS Reset Button)を押してみる

```
Connected.

UPS PIco RESTART CAUSE: MCLR_FROM_RUN


----------------------
www.pimodules.com
UPS PIco Hardware Release: V1.10 Q
Firmware Release: V1.0 XBMC 11.11.2015 0x54
----------------------

```

みたいなのが表示されたら成功


もし接続出来ない場合は、AdafruitのUART設定を参考にして

Raspbian Jessieの場合
```
$ sudo systemctl stop serial-getty@ttyAMA0.service
$ sudo systemctl disable serial-getty@ttyAMA0.service
$ sudo reboot
```

をしてみる


I2Cの接続確認は、UPS Picoをラズパイに乗せた状態で

下記コマンドを打って、以下のように数字が表示されたら成功
```
$ sudo i2cdetect –y 1

     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- 68 69 6a 6b -- -- -- -- 
70: -- -- -- -- -- -- -- --                   
```

となれば成功


しかし、この状態ではまだ電源監視システムは動いていない
(一応電力は供給されるみたいだが、自動でshutdownする機能はまだ持っていない)
ので
サポートファイルをダウンロードして設定してやる

---
## 電源監視をする
---

UPS PIcoのサポートファイルをダウンロードして設定してやる
(ダウンロードしたスクリプト内にディレクトリ指定などあるので、わからない場合などまずはルートディレクトリで行ってやること)

```
$ cd
$ wget http://www.pimodules.com/_zip/UPS_PIco_Supporting_Files.zip
$ unzip UPS_PIco_Supporting_Files.zip
```

スクリプトファイルとして
picofssd.py : 電源監視用pythonスクリプト
picofu3.py 	: UPS PIco firmware更新用pythonスクリプト
pico_status.py  : UPS PIcoのステータス確認用pythonスクリプト

設定ファイルとして
rc.local 

がダウンロードされるので

試しに picofssd.py を使ってみる

```
$ sudo python picofssd.py
```

この状態で、UPS PIcoのFSSDボタンを押すと、シャットダウンされるはず

pythonスクリプトをコンソールから起動させた状態では扱いにくいので
ラズパイ立ち上げ時に自動で起動するようにしてやる

---
## スクリプトファイルをバックグラウンドで起動
---

落としてきたzipファイル内に、rc.localのファイルがあるのでこれをコピーして使ってやる

元々ラズパイにはrc.localファイルが存在するため
一応バックアップをとっておく
```
$ sudo cp /etc/rc.local /etc/original_rc.local
```

次に、ダウンロードしてきたrc.localを移動してアクセス権限を変更して再起動してやる
```
$ sudo mv rc.local /etc/
$ sudo chmod +x /etc/rc.local
$ sudo reboot
```

これで、電源監視が行われているはずなので
試しにラズパイの電源供給を止めてやる

バッテリーが充電されていたら、電源offから30秒後にシャットダウンが開始されれば成功
(2016年4月26日時点でのUPS PIcoのfirmwareのバージョンではデフォルトが30秒後に設定されているが後述の新しいfirmwareではデフォルトが60秒に設定されている)


もしもうまくいかない場合は、ラズパイのほうをアップデートするなどすると
解決することがあるのでやってみる
自分の場合は、インストールしたものの原因なのかはわからないが
rpi-updateしてやったらfirmwareが更新されてうまくいったところもあった

```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo rpi-update
```

これで基本的なUPS PIcoの使い方はOKなので
後は少し捕捉していく

---
## firmwareの更新
---

購入した際、UPS PIcoのfirmwareのバージョンは54だった
先ほどダウンロードしたpythonスクリプト(pico_status.py)による確認

```
$ sudo python pico_status.py 

			 pico status V1.0
***********************************
  UPS PIco Firmware: 54
  Powering Mode: RPi
  BAT Volatge: 3.08 V
  RPi Voltage: 4.81 V
  SOT23 Temperature: 28 C
  TO-92 Temperature: 00 C
  A/D1 Voltage: 0.0 V
  A/D2 Voltage: 0.0 V
***********************************
```


このfirmwareを更新してやる

まず、最新のfirmwareをダウンロードしてやる

もしSerialでのコンソールログインがonになっている場合は
前述のとおりraspi-configなどからoffにしてやる


次にfirmwareをダウンロードしてやる

```
$ wget http://www.pimodules.com/_zip/UPS_PIco_Firmware_Update.zip
$ unzip UPS_PIco_Firmware_Update.zip
```


これでfirmwareがダウンロードできた
2016年4月26日現在でのファイル名は

`UPS_PIco_0x5C_25_02_2016.hex`

だったのでこれを用いてupdateしてやる
updateは、pythonスクリプトのpicofu.pyを用いる
(アップデートのスクリプトもバージョンみたいなのがあるらしく、現時点ではpicofu3.pyという名前だった)

まず、UPS PIcoのリセットボタン(UPSR)を押す

下記コマンドを打ち込む(firmwareのバージョンによって名前が違う場合は適宜修正する)

```
$ sudo i2cset -y 1 0x6b 0x00 0xff && sudo python picofu3.py -v -f UPS_PIco_0x5C_25_02_2016.hex
```

マニュアルのように
....... 10% ........

みたいになったら成功
終わったらラズパイおよびUPS PIcoを再起動してあげれば
更新が適用される


```
$ sudo python pico_status.py

      pico status V1.0
***********************************
  UPS PIco Firmware: 5c
  Powering Mode: RPi
  BAT Volatge: 3.39 V
  RPi Voltage: 4.8 V
  SOT23 Temperature: 28 C
  TO-92 Temperature: 00 C
  A/D1 Voltage: 0.0 V
  A/D2 Voltage: 0.0 V
***********************************
```
Firmwareが5cに更新されてた


また、firmwareのバージョン確認だけなら以下のコマンドでも確認可能

```
$ sudo i2cget -y 1 0x6b 0x00
0x5c
```

---
## その他気付いたこと
---

#### バッテリーの充電に関して

UPS PIcoはラズパイに乗せているだけでバッテリーが充電される仕組みになっているが
UPS PIcoを用いていて、pico_status.pyで確認した際
バッテリー(BAT Voltage)が2.7V付近から充電されないことがあった

おそらく、これはHDMIでのディスプレイ接続を行っていたため
バッテリーが電力を消費するほうにながれていたのではないかと考えられる

実際、ディスプレイ接続をやめてssh接続でラズパイを確認してやれば
すぐにBAT Voltageが3V以上になった


困った時は色々繋いでるものをいったん外してみよう
(特に、RaspberryPi3だとそもそもの電力消費が大きくなる傾向にあるので使うとどうなるかは電力面で判断するほうがよいかも)


#### i2cdetectした場合

i2cdetectした時に、0x68のところに "UU" と出るが
これは、Raspbian自身がこのアドレスを管理している、ということらしい
http://news.mynavi.jp/articles/2014/08/21/raspberry-pi4/002.html

rc.localで設定したものが反映されてると思われるのでこれはそのままで大丈夫


#### 色々とコマンドを使える

詳しくは[manual](http://pimodules.com/_pdf/UPS_PIco_Manual.pdf)(PDF注意)
に載っているが、コマンド経由で設定や設定情報の取得など出来る

例えば、下記コマンドで自動シャットダウンの時間を取得できる

```
$ sudo i2cget -y 1 0x6b 9
0x3c
```

0x3cは16進数表記で、10進数だと60のことなので
デフォルトでは電源供給が止まってから60秒後にシャットダウンするということがわかる


これを変更するには
```
$ sudo i2cset -y 1 0x6b 9 15
```

としてやれば、シャットダウンまで15秒に変更できる

一応確認してみる
```
$ sudo i2cget -y 1 0x6b 9
0x0f
```

0x0fは15のことなので、これで変更に成功できた


あとは色々あるが基本的に
`i2cset コマンド`で値の設定
`i2cget コマンド`で値の取得

を行う
例えば、UPS PIcoに載ってるLED(D5: 青, D6: 赤)のon offも手動で切り替え可能で

```
# BLUE-LED on
$ sudo i2cset -y 1 0x6b 12 1

# BLUE-LED off
$ sudo i2cset -y 1 0x6b 12 0

# RED-LED on
$ sudo i2cset -y 1 0x6b 13 1

# RED-LED off
$ sudo i2cset -y 1 0x6b 13 0
```

となる

後は色々manualを見ながら試してみると面白いこと出来そう








