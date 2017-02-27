title: RaspberryPi + BMP180 + muninで温度データをグラフ化
date: 2016-08-14 16:32:32
tags:
- RaspberryPi
- python
- 電子工作
---


温度湿度気圧センサのBMP180(Adafruit製)を用いて
温度データを取得後、muninというツールを用いてグラフ化した

用いたもの
・RaspberryPi2 B (Raspbian wheezy(2015年5月バージョン))
・BMP180

pythonはRaspbianに入っていたものをそのままなのでバージョンは2.7.3です

<!-- more -->

### まずはpythonでデータ取得をやってみる
下記を参考にした、そのままで出来ます
http://qiita.com/masato/items/d5c19a883b0ba3ec7d5b

i2cを使用可能な状態にしておくこと
参考リンク
http://qiita.com/tchisaka/items/e8a2b09c808715dc8515

```
$ sudo apt-get update
$ sudo apt-get install git build-essential python-dev python-smbus
$ git clone https://github.com/adafruit/Adafruit_Python_BMP.git
$ cd Adafruit_Python_BMP
$ sudo python setup.py install
```

実際に配線してみてexampleのコードを試してみる

```
$ cd Adafruit_Python_BMP/example
$ sudo python simpletest.py

Temp = 22.60 *C
Pressure = 100439.00 Pa
Altitude = 73.86 m
Sealevel Pressure = 100439.00 Pa

```

のように表示されたら成功

### muninでグラフ化してみる

センサからの値をグラフ化する方法は色々あるみたいですが
[pythonでのグラフ描画について調べた](http://yoheikoga.github.io/2016/04/20/python%E3%81%A7%E3%81%AE%E3%82%B0%E3%83%A9%E3%83%95%E6%8F%8F%E7%94%BB%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E8%AA%BF%E3%81%B9%E3%81%9F/)

RaspberryPiだと、よく使われるのはMuninというツールを使ってやります

Muninとは、そもそもはリソース管理ツールみたいです
[リソース監視ツール Munin を使う](http://www.omakase.org/misc/_munin.html)


RaspberryPiに導入するのに色々な情報があって最初は錯綜したけど
やってみたらシンプルでした

以下は設定の参考先
http://www.lisa.jp/index.php/Munin
http://blog.makotokw.com/2013/11/13/raspberry-pi%E3%81%A7%E3%82%B5%E3%83%BC%E3%83%90%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%82%92%E7%9B%A3%E8%A6%96%E3%81%99%E3%82%8B/
http://bey.jp/?p=15720
http://bey.jp/?p=15709


2016/04/21現在だと


```
$ sudo apt-get update
$ sudo apt-get install munin munin-node
```

してやれば入ります
後は
```
$ startx
```

してやって、ブラウザを開いて、アドレスバーに
`http://raspberrypi/munin`
と入れてやってmuninの画面が出てきたら成功

### ただしこの状態だと、他のブラウザからは見ることが出来ない
試してみた↓

自分のラズベリーパイは、固定IPアドレスに設定しているので
例えば
address 192.168.11.1
とかだったとしたら、パソコンのブラウザなどから
`http://192.168.11.1/munin`
にアクセスしてみると

>Forbidden
>You don't have permission to access /munin on this server.


みたいなのが表示されます

どのブラウザからも確認するためには
muninの設定を書き換えてあげる必要がある

```
$ sudo vi /etc/munin/apache.conf
```

以下の部分を見つける
```
<Directory /var/cache/munin/www>
  Order allow,deny
  Allow from localhost 127.0.0.0/8 ::1
```

下記のように書き換える

```
<Directory /var/cache/munin/www>
  Order allow,deny
  # Allow from localhost 127.0.0.0/8 ::1
  Allow from all 
```

保存して再起動する

```
$ sudo reboot
```

再起動後、自分のパソコンなどのブラウザから

`http://192.168.11.1/munin`

でアクセスして、muninの画面が表示されたら成功です



### 実際にmuninによるグラフ化を試す

温度センサのBMP180で試してみた
ライブラリのダウンロードや使い方などは以下を参照
http://qiita.com/masato/items/d5c19a883b0ba3ec7d5b

まずはAdafruitのライブラリのexampleをコピーしておいて、それを書き換える
コピーしたファイル名は `temp.py`にしました

ディレクトリは以下のようになっている(はず)なので
/Adafruit_Python_BMP/examples/


まずはライブラリのサンプルスクリプトをコピーしてあげて書き直す
```
$ cp simpletest.py temp.py
$ sudo vi temp.py
```

以下のように書き換える
コメントの部分を消去して、最後のほうのprintの行をコメントアウトして、最後の行を追加したら下記のようになる

```
import Adafruit_BMP.BMP085 as BMP085

sensor = BMP085.BMP085()

#print('Temp = {0:0.2f} *C'.format(sensor.read_temperature()))
#print('Pressure = {0:0.2f} Pa'.format(sensor.read_pressure()))
#print('Altitude = {0:0.2f} m'.format(sensor.read_altitude()))
#print('Sealevel Pressure = {0:0.2f} Pa'.format(sensor.read_sealevel_pressure()))
print '{}'.format(sensor.read_temperature())

```


できたら、ファイルをpathの通ったところにコピーする
例えば /usr/local/bin がいいのではないかと思われるので以下のようにする

```
$ sudo cp temp.py /usr/local/bin
```

#### シェル・スクリプトでプラグインを作る
下記を参考にした
http://blog.bnikka.com/raspberrypi/dht11-raspberrypi.html

```
$ sudo vim /usr/share/munin/plugins/temp
```

下記を記述

```
#!/bin/sh

#%# family=auto
#%# capabilities=autoconf

GETNUM=`python /usr/local/bin/munin_temp.py 11 4`

if [ "$1" = "autoconf" ]; then
        if [ -n ${GETNUM} ] ; then
                echo yes
                exit 0
        else
                echo no
                exit 0
        fi
fi

if [ "$1" = "config" ]; then
        echo 'graph_title temperature'
        echo 'graph_args -r --lower-limit 0'
        echo 'graph_vlabel C'
        echo 'graph_category Weather'
        echo 'total.label temperature'
        echo 'total.min 0'
        echo 'total.draw LINE2'
        echo 'total.type GAUGE'
        exit 0
fi

echo "total.value $GETNUM";

```

スクリプトの準備が整ったのでコンソールから下記コマンドをうちこんであげる

```
# ルートアクセス権限を与える
$ sudo chmod 755 /usr/share/munin/plugins/temp

# muninでグラフ化出来るようにプラグインにシンボリックリンクを与える
$ sudo ln -s /usr/share/munin/plugins/temp /etc/munin/plugins/temp

# ルート権限でアクセスできるようにする
$ sudo vi /etc/munin/plugin-conf.d/temp

# 下記2行を記述
[temp]
user root

# muninを再起動
$ sudo /etc/init.d/munin-node restart

```

後はブラウザからmuninにアクセスすればよい
参考リンク先
http://bey.jp/?p=15709
みたいに、muninブラウザの左の欄に "weather" という項目があったら成功

あとは、温度データは5分に1回の取得になるので
何分か待ったら更新される

やってみたら下みたいなグラフが得られた
(30分ぐらいしか動かしてないのでデータ少ない)

<img src="/2016/08/14/raspberrypi-bmp180-munin-data/munin.jpg" width="300px" height="200px" alt="RaspberryPi">


これでグラフ化が成功しました


## 以下はやらなくても大丈夫な追加設定

### 負荷を軽減する

デフォルトの設定だと5分毎にデータをもらってグラフを書き換えているみたいなので
ひとまず負荷を軽減する策を講じる

#### グラフにブラウザからアクセスする時に更新するようにする

```
$ sudo vi /etc/munin/munin.conf
```


以下２つの行を見つける。２つは少し離れているので注意
```
#graph_strategy cron

#cgiurl_graph /munin-cgi/munin-cgi-graph
```

２つともコメントを外して以下のようにする
```
graph_strategy cgi

cgiurl_graph /munin-cgi/munin-cgi-graph
```

保存する。次にブラウザで見る時の制限を解除する
```
$ sudo vi /etc/munin/apache.con
```

```
# 以下の行をみつける

<Location /munin-cgi/munin-cgi-graph>
  Order allow,deny
  Allow from localhost 127.0.0.0/8 ::1

<Location /munin-cgi/munin-cgi-html>
  Order allow,deny
  Allow from localhost 127.0.0.0/8 ::1

# 次にように書き換える

<Location /munin-cgi/munin-cgi-graph>
  Order allow,deny
  # Allow from localhost 127.0.0.0/8 ::1
  Allow from all 

<Location /munin-cgi/munin-cgi-html>
  Order allow,deny
  # Allow from localhost 127.0.0.0/8 ::1
  Allow from all 

```
保存して再起動する

```
$ sudo reboot
```



