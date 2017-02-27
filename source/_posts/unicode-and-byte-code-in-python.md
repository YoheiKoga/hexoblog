title: Pythonはバイト列やunicode文字列が認識される
date: 2016-08-14 16:40:03
tags:
- python
---


参考
http://www.tohoho-web.com/python/types.html

pythonのバージョンは3.4.3を使用

printした時に、コンソールに b とか u とか出てくることがあるけど
これは文字の型を表しているっぽい

例えば[シリアル通信の例](http://yoheikoga.github.io/2016/08/14/serial-communication-by-python/)だと


```python
# coding:utf-8

import serial
import types

ser = serial.Serial(
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
  print(ser.readline())
```

だと、コンソール上に

b'$GPRMC,,,V,,,,,,,,N*48\r\n'

みたいに表示される
これを防ぐには、printする時にdecode('utf-8')してやればよい

print(ser.readline().decode('utf-8'))

