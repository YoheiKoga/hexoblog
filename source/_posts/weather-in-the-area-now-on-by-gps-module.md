title: GPSモジュールを使って現在地の天気情報をAPIで取得する
date: 2016-08-14 16:45:46
tags:
- python
- 電子工作
---


OpenWeatherMapという天気情報をAPIで提供しているものがあって
[OpenWeatherMap](http://openweathermap.org/)

緯度経度情報から、天気情報を取得出来るということで
GPSモジュールを使って現在地の緯度経度から天気情報を取得してみた

コードはgithubに上げてます
[weatherApiWithGPS](https://github.com/YoheiKoga/weatherApiWithGPS)

<!-- more -->

参考リンク
[OpenWeatherMapを利用して天気情報を取得してみる](http://ryus.co.jp/blog/openweathermap/)
[天気情報を取得する](http://qiita.com/s0hno/items/b67a24bd3cbeb6e7cdc0)
[無料天気予報APIのOpenWeatherMapを使ってみる](http://qiita.com/nownabe/items/aeac1ce0977be963a740)


環境はMac OsX 10.9, Pythonは Anaconda 2.3.0(python version 3.4.3) を用いた

---
# まずは上記参考リンクに沿って、OpenWeatherMapに登録してAPIを使うためのAPI keyを取得する
---

まずは簡単な例
取得したAPI_KEYが abcdefghijklmnopqrstuvwxyz だったとしたら
appidのところは

```python
appid = "abcdefghijklmnopqrstuvwxyz"
```

と記載する

```python weatherapi.py
# coding:utf-8

import urllib.request
import json

location = "Fukuoka-shi,jp"
mode = "json"
metric = "Metric"
lat = "33.590355"
lon = "130.401716"
# metric = "Default"  # Kelvin
# metric = "Imperial" # Fahrenheit
appid = "write your api key"

# location from city name
# url = "http://api.openweathermap.org/data/2.5/weather?q={a}&mode={b}&units={c}&appid={d}".format(a=location, b=mode, c=metric, d=appid)

# location from geocode
url = "http://api.openweathermap.org/data/2.5/weather?lat={a}&lon={b}&units={c}&appid={d}".format(a=lat, b=lon, c=metric, d=appid)

response = urllib.request.urlopen(url).readline()
weather = json.loads(response.decode('utf-8'))  

print(weather['name'])
print(weather['main']['temp'])
```

緯度経度は[google Geocoding](http://www.geocoding.jp/?q=%E7%A6%8F%E5%B2%A1%E5%B8%82)を用いて福岡市の場所を取得した
location from city nameの行をgeocodeの代わりに使用すれば都市名からも天気情報を取得することができる

これで天気情報が取得できたら、次にGPSモジュールを使って緯度経度を取得し、天気情報を得てみる


---
# GPSを使った位置情報の取得
---

GPSモジューつの使い方は[Pythonでシリアル通信](http://yoheikoga.github.io/2016/08/14/serial-communication-by-python/)を参照


結論から言うと、GPSモジュールを接続して
下記コードを実行すると取得できる
USBポート名とOpenWeatherMapのAPI_KEYは自分の環境のものを記入する

```python gpsWeather.py
# coding:utf-8

import urllib.request
import json
import serial
import re
import types
import time

ser = serial.Serial(

      # add your own port name
      # 自分のUSBポート名を記入する
      port = "/dev/tty.usbserial*",

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
  
  # GPRMCデータのみを取得する
  if re.search(r'RMC', gpsData):

    str = gpsData.split(",")
    
    # str[3]は緯度、str[5]が経度なので、str[3]とstr[5]に値が入っていたら緯度経度情報を利用する
    if str[3] != "" and str[5] != "":
      lat_60decimal = float(str[3])
      lng_60decimal = float(str[5])
      lat = int(lat_60decimal/100)+(lat_60decimal/100 - int(lat_60decimal/100))*100/60
      lng = int(lng_60decimal/100)+(lng_60decimal/100 - int(lng_60decimal/100))*100/60
      mode = "json"
      metric = "metric"

      # add your API_KEY in appid. for instance, if your API KEY is abcdefghijklmnopqrstuvwxyz, write like under way
      # appid = "abcdefghijklmnopqrstuvwxyz"
      appid = {"add your API_KEY"}

      url = "http://api.openweathermap.org/data/2.5/weather?lat={a}&lon={b}&mode={c}&units={d}&appid={e}".format(a=lat, b=lng, c=mode, d=metric, e=appid)

      response = urllib.request.urlopen(url).readline()
      weather = json.loads(response.decode('utf-8'))  
      
      print(weather['name'])

      # 現在の天気を表示
      print("weather is :", weather['weather'][0]['main'])
      # 現在の気温を表示
      print("temperature is :", weather['main']['temp'])
      print("")
      time.sleep(1.0)

    else:
      print("no data")
```

## コードの説明

### GPSモジュールはNMEAの形でいくつかの種類のデータを出力する

参考
http://www.hiramine.com/physicalcomputing/general/gps_nmeaformat.html

GPRMCは、基本的な情報を含んでいるデータで時刻情報や緯度経度を出力するので
今回はGPRMCを利用している


### GPSモジュールからのデータを改変して使用できる緯度経度情報に計算する

コードの中で、得られたGPS情報を計算する部分がある

```python
lat_60decimal = float(str[3])
lng_60decimal = float(str[5])
lat = int(lat_60decimal/100)+(lat_60decimal/100 - int(lat_60decimal/100))*100/60
lng = int(lng_60decimal/100)+(lng_60decimal/100 - int(lng_60decimal/100))*100/60
```

GPSモジュールから得られるGPRMCの緯度経度は、度分表示(秒は含まれていないっぽい)
つまり、60進数表記

なので、google mapだとか、weather api　にぶち込む時は
10進数表記にしてやらないといけないので計算する

例えば

$GPRMC,052004.000,A,3335.7339,N,13023.8791,E,1.16,80.86,201215,,,A*54

というGPRMCが得られたら、緯度経度は

緯度 33.357339 N (33度35.7339分, N(北緯))
経度 130.238791 E (130度23.8791分, E(東経))
で60進数表記なので

10進数に直すためには
度 + 分/60 をしてやらないといけない

参考
http://blog.fkoji.com/2005/09210026.html
http://qiita.com/tag1216/items/0b38ee5aedea0ef4a058

たとえば上のGPRMCのデータからだと

緯度(10進数) = 33 + 35.7339/60 ~= 33.59556166666667
経度(10進数) = 130 + 23.8791/60 ~= 130.3979733333333

となる

この緯度経度10進数表記をgoogle map や weather api につっこむとちゃんとしたデータが得られる。



これで現在地のデータを取得することができた

GPSモジュールとAPIを組み合わせてみたけどこういうのから何かできないかなと思う

