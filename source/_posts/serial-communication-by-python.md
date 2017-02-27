title: Pythonでシリアル通信
date: 2016-08-14 16:37:12
tags:
- python
---


試しに、GPSモジュールを使ってデータを取得してみる
環境はMac OsX 10.9, Python 3.4.3(Anaconda 2.3.0)
使用したものは

[Adafruit Ultimate 66チャンネル10Hz GPSモジュール Version 3](https://www.switch-science.com/catalog/1085/)
(いまだと秋月電子に[もっと安いやつ(ＧＰＳ受信機キット　１ＰＰＳ出力付き　「みちびき」対応)](http://akizukidenshi.com/catalog/g/gK-09991/)があるのでそれでも可)

[FTDI USBシリアル変換アダプター(5V/3.3V切り替え機能付き)](https://www.switch-science.com/catalog/1032/)

その他USBケーブル(A - microB)ジャンパーワイヤーやブレッドボードなどひつようなもの


### pySerialをインストールする

```
$ pip install pyserial
```

### GPSをMacと接続する

GPSモジュールとUSBシリアル変換アダプターの接続は

GPSモジュール : USBシリアル変換アダプター
Vin : VCC
GND : GND
TX  : RX
RX  : TX

となるように接続する

<img src="./gps.jpg" width="300px" height="200px">

Pythonでシリアル通信
シリアル変換アダプターのジャンパーピンは、今回のGPSだと3.3Vでも5Vでもどちらでも動くのでひとまず3.3Vにしている

あとはUSBケーブルでMacと接続して、USBケーブルのポート名を取得しておく
ポート名の取得は、terminal上で下記コマンドを打つと出てくる

```
$ ls /dev/tty.usbserial*
/dev/tty.usbserial-AI03CNY6
```

上記例だと、AI03CNY6の部分を出てきたやつに変えてやると自分のポート名になる

簡単な例(gpsデータからGPRMCのみ取り出す)
```python gps.py
# coding:utf-8

import serial
import types
import re

ser = serial.Serial(
      
      # Macの場合、 /dev/tty.usbserial-* の形で認識される。WindowsならCOM3とかCOM4とかになるはず
      # ls /dev/tty.usbserial* で出てきたポート名を入れること
      port = "/dev/tty.usbserial-AI03CNY6",
      baudrate = 9600,
      parity = serial.PARITY_NONE,
      bytesize = serial.EIGHTBITS,
      stopbits = serial.STOPBITS_ONE,
      timeout = None,
      xonxoff = 0,
      rtscts = 0,
      )

while True:
  gpsData = ser.readline().decode('utf-8')

  if re.search(r'RMC', gpsData):
    print(gpsData.strip())
```

if文のところを取り除いてあげれば出力されるGPSデータ全てが出力される


