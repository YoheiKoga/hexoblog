title: Raspbian jessieからRaspbian jessie Pixelにアップグレードする
date: 2017-02-07 16:42:25
tags:
- RaspberryPi
---

Raspbian jessieから Raspbian jessie Pixelにアップグレードしてみる

<!-- more -->

<br>

が、検索して出て来る方法ではなんかうまくいかず

`$ sudo apt-get install -y rpi-chromium-mods` のところでエラーがでてつまづいてしまう

検索してると下記リンク先が出てきた
http://raspberrypi.stackexchange.com/questions/55643/cant-install-rpi-chromium-mods-from-pixel-upgrade


リンク先通りに
/etc/apt/sources.list.d/raspi.list を下記のように変更する

```
deb http://archive.raspberrypi.org/debian/ jessie main ui 
```

最後にuiをつけないといけないっぽい

で、下記公式サイト通りにアップデート作業する

https://www.raspberrypi.org/blog/introducing-pixel/

```
$ sudo apt-get update
$ sudo apt-get dist-upgrade
$ sudo apt-get install -y rpi-chromium-mods
$ sudo apt-get install -y python-sense-emu python3-sense-emu
$ sudo apt-get install -y python-sense-emu-doc realvnc-vnc-viewer
```

で、rebootしたら無事にPixelにアップグレードされました

てか軽い、デスクトップも綺麗になってるしこれは良さそう
