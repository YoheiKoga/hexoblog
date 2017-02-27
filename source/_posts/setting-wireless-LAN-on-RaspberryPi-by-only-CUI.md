title: CUIだけでラズベリーパイの無線LAN設定を行う
date: 2016-08-14 15:49:53
tags:
- RaspberryPi
---

## RaspberryPiを無線ＬＡＮでネットに接続するための覚書

以下のサイトを参考に行った
[Raspberry Piを無線LAN化](http://d.hatena.ne.jp/pasela/20121224/raspi_wlan)
[Raspberry Piに無線LANのUSBアダプタをつける](http://denshikousaku.net/raspberry-pi-wifi-lan-usb)

また、Buffaloの無線受信機だと発熱が困るという問題があって(実際自分も使ってみたらかなりの熱を発していた)
以下のPLANEXの無線受信機を用いた。こいつは発熱しなくてよい
[PLANEX 無線LAN子機](http://www.amazon.co.jp/%E7%84%A1%E7%B7%9ALAN%E5%AD%90%E6%A9%9F-USB%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF%E3%83%BC%E5%9E%8B-433Mbps-X10-10%E5%AF%BE%E5%BF%9C-GW-450S/dp/B00J8CUCLS)

以下手順を書いておく

ラズベリーパイのCUIコンソール上で

```
$ sudo iwlist wlan0 scan | grep ESSID
```
で、アクセスポイントの名前を確認

自分のWiFi接続環境のアクセスポイントの名前が出てくるか確認する

```
$ sudo ifdown wlan0
```
で、wlan0による無線接続を一旦切っておく
こうしないと後々エラーがおきてしまう

```
$ sudo iwconfig wlan0 essid 接続先のSSID key s:接続先のキー
```
で接続を設定しておく。
例えば、essid(無線ＬＡＮのアクセスポイントの名前)が myHomeWiFi で
WiFi接続のパスワードが myWiFiPassword だった場合

```
$ sudo iwconfig wlan0 essid myHomeWiFi key s:myWiFiPassword
```

とコンソールコマンドを打ち込む

```
$ sudo iwconfig wlan0
```
で確認してみて、例えば

```
wlan0     IEEE 802.11bgn  ESSID:"myHomeWiFi"  
          Mode:Managed  Access Point: Not-Associated   Tx-Power=20 dBm   
          Retry  long limit:7   RTS thr:off   Fragment thr:off
          Power Management:on
```

のように、ESSIDのところにアクセスポイントの名前が表示されていたら成功

次に、 /etc/wpa_supplicant/wpa_supplicant.conf
を設定する

以下のページを参考にした
[Raspberry Pi + 無線LAN セットアップ](http://qiita.com/daicham/items/e24ce76f8815d339d070)

先ほどと同じで例えば
essid(無線ＬＡＮのアクセスポイントの名前)が myHomeWiFi で
WiFi接続のパスワードが myWiFiPassword だった場合

```
$ sudo sh -c "wpa_passphrase myHomeWiFi myWiFiPassword >> /etc/wpa\_supplicant/wpa_supplicant.conf"

$ sudo cat /etc/wpa_supplicant/wpa_supplicant.conf
```

とすることで、wpa_supplicant.confに設定が反映される

この後
```
$ sudo ifdown wlan0
$ sudo ifup wlan0
```

とすると、ネットに繋がる(はず)

つながらない場合は

```
$ sudo dhclient wlan0
```

とするとIPアドレスがふられてネットに繋がるはず
```
$ ifconfig
```
で確認できる


## 次に、固定IPを振っていく

固定IPを振っていないと、毎回IPアドレスが変わってしまうので
ssh接続する時などにちょっと不便になるので設定

```
$ sudo vi /etc/network/interfaces
```
をすると

```
auto lo
iface lo inet loopback

auto eth0
allow-hotplug eth0
iface eth0 inet manual

auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

auto wlan1
allow-hotplug wlan1
iface wlan1 inet manual
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

```

みたいなのが出てくるので

```
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
#iface wlan0 inet manual
iface wlan0 inet static

address 192.168.1.200
netmask 255.255.255.0
gateway 192.168.1.1


#wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

と書き換えて保存。
(基本的にnetmaskは 255.255.255.0 と設定してやれば問題なさげ)

addressは、最後の数字だけを好きな数字にしてあげればよい
例えば、今接続されてる無線ＬＡＮのアドレスの調べ方は
Macだと下記サイトに書かれている方法で確認して
[Mac - IPアドレスを確認する方法](http://pc-karuma.net/mac-ip-address/)

使用しているWiFiのIPアドレスが例えば
192.168.1.5

だったとしたら、5のところを好きな数字にしてあげればよいので

address 192.168.1.200

のようにしてやればよい。

gatewayの確認方法は[ここ](http://buffalo.jp/download/manual/bro150/qa/qa/buf1528.html)を参考にして、例えば

192.168.1.0
だとしたら

gateway 192.168.1.0
と設定してやる。


wlan1はまぁ消しても消さなくてもどっちでもよい、使わないので気になるなら消して良いと思われる

後は
```
$ sudo ifdown wlan0
$ sudo ifup wlan0
```
をすればよいはず

うまく設定出来ているかは

```
$ ifconfig
```
で確認できる





