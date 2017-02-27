title: RaspberryPiカメラをタクトスイッチで起動させる
date: 2016-08-14 16:04:18
tags:
- RaspberryPi
- 電子工作
- プログラミング
---


RaspberryPiのデジカメ化を行う
やることとしては

・RaspberryPiの電源を入れると自動ログインする
・タクトスイッチを押した時にプログラムが起動してカメラで撮影を行う

までやれるようにする

使ったものは
[RaspberryPi model A+](http://jp.rs-online.com/web/p/processor-microcontroller-development-kits/8332699/)
[RaspberryPi camera board](http://jp.rs-online.com/web/p/products/7757731/?cm_mmc=JP-PPC-_-google-_-3_JP_JP_M_SUPPORT_Exact-_-raspberry_pi%7C%E3%83%93%E3%83%87%E3%82%AA%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB&mkwid=sRoelAmR2-dc%7Cpcrid%7C86021785983%7Cpkw%7Craspberry+pi+camera+board%7Cpmt%7Ce%7Cprd%7C)
[タクトスイッチ](http://akizukidenshi.com/catalog/g/gP-03647/)
です




<!-- more -->

以下準備を色々とする

# タクトスイッチでshutdownが起こるようにする
タクトスイッチを押した時にプログラムが動き出すようにするのの練習として
スイッチを押した時にシャットダウンが起こるかを試した

下記参考サイトを参考にした
[Raspberry Pi にシャットダウンボタンをつける](http://d.hatena.ne.jp/penkoba/20130925/1380129824)
[タクトスイッチでRaspberryPiをシャットダウンさせてみた【Python】【supervisor】](http://raspi.seesaa.net/article/418174651.html)


# 自動ログインの設定

Raspberry Piの電源を入れた後、IDやパスワードを入力しないといけない状態だと
デジカメとしては不便なので下記サイトを参考に自動ログインするようにした
[Raspberry Piのログインを簡単にする設定（自動ログイン、デスクトップ表示）](http://pettomo.azurewebsites.net/?p=4451)


# スイッチを押すことでカメラ起動して写真を撮ってみる

下記のpythonスクリプトをホームディレクトリに作成(/home/pi/test.py)
尚、今回はGPIO25ピンにタクトスイッチが繋がるようにしている

```python
# coding:utf-8

import RPi.GPIO as GPIO  
import os
GPIO.setmode(GPIO.BCM)  

GPIO.setup(25, GPIO.IN, pull_up_down=GPIO.PUD_UP)  

try:  
        GPIO.wait_for_edge(25, GPIO.FALLING)  
except KeyboardInterrupt:  
        GPIO.cleanup()  # clean up GPIO on CTRL+C exit  
GPIO.cleanup()          # clean up GPIO on normal exit  
os.system("raspistill -o /home/pi/test.jpg")
```

保存した後にchmodでアクセス権限を与えて起動させてみる
```
$ chmod u+x test.py
$ sudo python test.py
```

で実行後、タクトスイッチを押すと、写真撮影がはじまります

難点は、このままだといちいちpythonスクリプトを起動しないといけないこと
これをデーモン化という手法で、バックグラウンドでプロセスを監視させていつでも起動できるようにしておく
デーモン化は前述のSupervisorというのを使うと簡単に出来る

# デーモン化により、プログラムの自動起動設定

supervisorを使って、ボタンを押した時に自動でカメラを起動するようにしてあげる

supervisorの使い方などは下記を参考にした
[Raspberry Piに【Supervisor】をインストールして、motionをデーモン化してみた](http://raspi.seesaa.net/article/415778601.html)


まず適当なディレクトリにパイソンのスクリプトファイルを作成する
今回は一番上のディレクトリ(/home/pi/)に、camera.pyを作成する
pinはGPIO25(22番ピン)を用いて、配置は[参考サイト](http://raspi.seesaa.net/article/418174651.html)と同じ配置です

camera.py

```python
# coding:utf-8
 
import time
import RPi.GPIO as GPIO	
import shlex
import subprocess
 
pin = 25
cmd = 'sudo raspistill -o test.jpg'
 
GPIO.setmode(GPIO.BCM)
GPIO.setup(pin, GPIO.IN)
 
def take_picture():
  print "take picture"
  proc = subprocess.Popen(shlex.split(cmd))
 
try:
  GPIO.wait_for_edge(pin, GPIO.FALLING)
except:
  print "Quit"
finally:
  print "clean up"
  GPIO.cleanup()
 
take_picture()
```

次に、supervisorの記述をしていく
これも参考サイトと大体同じなのですが
・commandのディレクトリが異なる場合は注意
・一度カメラを起動すると、プロセスが終了してしまうので、オートリスタート(autorestart)の設定をしてあげる必要がある

今回ディレクトリは /home/pi/ なので
autorestart=true を設置してあげるので
confファイルの記述は以下のようにしました

$ sudo vi /etc/supervisor/conf.d/camera.conf 

でcamera.confを開いて

```
[program:camera]
command=sudo python /home/pi/camera.py
numprocs=1
redirect_stderr=true
stdout_logfile=/var/log/camera.log
user=pi
autorestart=true
```

:wqで保存
あとはsupervisorctlコマンドで reread, addしてあげればよいです

```
$ sudo supervisorctl reread
$ sudo supervisorctl add camera
```

これで、タクトスイッチを押せば、ホームディレクトリに test.jpgが生成される

これでひとまずデジカメ機能は完成なので後は自分で画像の保存先や名前など好きなように設定してあげればよい

