title: RaspberryPiにusbメモリのID指定でマウントさせる
date: 2016-08-14 16:52:36
tags:
- RaspberryPi
- Linux
---


【やりたいこと】
cronとかでデータ保存の自動実行などする際、usbをつけっぱなしにして自動マウントにしていると
デバイス名やマウント先が稀に異なってきてしまって
例えばファイルの保存先が異なると最悪データを保存できないなどある(usb挿しっぱなしだと本当に稀だとは思われるが)

ので、デバイス名などを指定しておき確実に自分の指定した名前でマウントできるようにする

自分の実行環境は
RaspberryPi2, OS Raspbian Jessie

参考
[Raspberry PiでUSBに繋いだストレージ(USBメモリ・HDD・SSDなど)をマウントするちょっと素敵な方法](http://lil68060.hatenablog.com/entry/2015/07/01/034254)

基本的に上記参考URLと全く同じ方法で出来るのでちょっとしたメモ追記がてら記載

ちなみに、もし `$ sudo apt-get install usbmount` でusbの自動マウントパッケージを入れていると
目的のことができないので`$ sudo apt-get remove usbmount`でひとまずアンインストールしておくこと

<!-- more -->

# どのSCSIデバイスに認識されているか調べる

dfコマンドなどで、RaspberryPiのどのSCSIデバイスに認識されているか調べる
マウントされていない場合は、
$ ls /dev/sd*
とすれば、認識されているusbメモリが確認できる

例えば、sda1とかが出てくると思われる

# udevadmでデバイスIDの確認

udevとは userspace device management のことらしい
[udevによるデバイスの命名の固定化](http://d.hatena.ne.jp/qpSHiNqp/20110214/1297651801)

```
$ sudo udevadm info -q all -n /dev/sda

P: /devices/platform/soc/3f980000.usb/usb1/1-1/1-1.4/1-1.4:1.0/host0/target0:0:0/0:0:0:0/block/sda
N: sda
S: disk/by-id/usb-BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0
S: disk/by-path/platform-3f980000.usb-usb-0:1.4:1.0-scsi-0:0:0:0
E: DEVLINKS=/dev/disk/by-id/usb-BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0 /dev/disk/by-path/platform-3f980000.usb-usb-0:1.4:1.0-scsi-0:0:0:0
E: DEVNAME=/dev/sda
E: DEVPATH=/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.4/1-1.4:1.0/host0/target0:0:0/0:0:0:0/block/sda
E: DEVTYPE=disk
E: ID_BUS=usb
E: ID_INSTANCE=0:0
E: ID_MODEL=USB_Flash_Disk
E: ID_MODEL_ENC=USB\x20Flash\x20Disk\x20\x20
E: ID_MODEL_ID=0089
E: ID_PART_TABLE_TYPE=dos
E: ID_PATH=platform-3f980000.usb-usb-0:1.4:1.0-scsi-0:0:0:0
E: ID_PATH_TAG=platform-3f980000_usb-usb-0_1_4_1_0-scsi-0_0_0_0
E: ID_REVISION=3.00
E: ID_SERIAL=BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0
E: ID_SERIAL_SHORT=0AB15160A3036AC7
E: ID_TYPE=disk
E: ID_USB_DRIVER=usb-storage
E: ID_USB_INTERFACES=:080650:
E: ID_USB_INTERFACE_NUM=00
E: ID_VENDOR=BUFFALO
E: ID_VENDOR_ENC=BUFFALO\x20
E: ID_VENDOR_ID=0411
E: MAJOR=8
E: MINOR=0
E: SUBSYSTEM=block
E: UDEV_LOG=3
E: UDISKS_PARTITION_TABLE=1
E: UDISKS_PARTITION_TABLE_COUNT=1
E: UDISKS_PARTITION_TABLE_SCHEME=mbr
E: UDISKS_PRESENTATION_NOPOLICY=0
E: USEC_INITIALIZED=5560560
```

### sudo udevadm info -q all -n /dev/sdaに関して
info: 
Queries the udev database for device information stored in the udev database. It can also query the properties of a device from its sysfs representation to help creating udev rules that match this device.

udevデータベース内のデバイスインフォーメーションからのクエリ-(データの問い合わせや要求). 
デバイスにマッチするudevルール作成補助のsysfs表現からデバイスのプロパティも問い合わせる

つまり要するにデバイス情報を引っ張ってくるってこと

-q: query type.
  name,symlink, path, property, all.　がある

例えば
$ sudo udevadm info -q name -n /dev/sda
とすると
sda
だけが帰ってくる

-n(--name): 
  file名
  まぁ/dev/sdaを指定している


##で、必要な部分

ID_SERIAL=BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0
の部分が必要になる

自分はBuffaloのusbメモリを用いているのでこういう名前になってた

# usbをひっこぬいて、デバイスの固有の名前を考える

[参考リンク先](http://lil68060.hatenablog.com/entry/2015/07/01/034254)
だと、固有の名前は usbmem_backup となっていたのでそれに倣って
usbmem_backupとして設定していくことにする

# udevのルールを /etc/udev/rules.d 内に書く

/etc/udev/rules.d 内に、デバイスの名前を割り当てるためのルールファイルを書く
ファイル名の先頭に出来るだけ大きな2桁の数字をつける
(rules.d内では、数字の小さいものから実行されるため)

[udev ルールの書き方](http://mux03.panda64.net/docs/udevrules_ja.html)

たとえば、ルールファイル名を
98-usbmem_backup.rules

とする(自分の場合は、99は既に使われていた)

で、内容は以下のようにする

```sh 98-usbmem_backup.rules
SUBSYSTEM=="block",
ENV{ID_BUS}=="usb",
ENV{ID_SERIAL}=="BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0", 
SYMLINK+="usbmem_backup%n"
```

長いから行を分けているけど実際は1行で記述するらしい
ということは以下のようになるのかな

```sh 98-usbmem_backup.rules
SUBSYSTEM=="block", ENV{ID_BUS}=="usb", ENV{ID_SERIAL}=="BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0",  SYMLINK+="usbmem_backup%n"
```

ENV{ID_SERIAL}=="BUFFALO_USB_Flash_Disk_0AB15160A3036AC7-0:0"
のところは、自分だと Buffaloのusbメモリだったのでこうなったので自分の使ってるusbメモリの名前を
前述通り確認した名前のものを入れること


確かに、Raspi内あったもともとのファイルのは以下のようになっていた
```
$ sudo cat /etc/udev/rules.d/40-scratch.rules 
ATTRS{idVendor}=="0694", ATTRS{idProduct}=="0003", SUBSYSTEMS=="usb", ACTION=="add", MODE="0666", GROUP="plugdev"
```

ただ、もう一つファイルがあってそっちは
```
$ sudo cat /etc/udev/rules.d/99-com.rules 
SUBSYSTEM=="gpio*", PROGRAM="/bin/sh -c 'chown -R root:gpio /sys/class/gpio && chmod -R 770 /sys/class/gpio; chown -R root:gpio /sys/devices/virtual/gpio && chmod -R 770 /sys/devices/virtual/gpio'"
SUBSYSTEM=="input", GROUP="input", MODE="0660"
SUBSYSTEM=="i2c-dev", GROUP="i2c", MODE="0660"
SUBSYSTEM=="spidev", GROUP="spi", MODE="0660"
```
だった
改行がはいってても大丈夫なのかな？


# で、完了

usbメモリを挿して、確認してみると

```
$ ls /dev/usb*
/dev/usbmem_backup  /dev/usbmem_backup1
```

だったので、ちゃんと指定した名前で認識されているのがわかる

ので、後は適当なディレクトリにmount可能となる

```
$ sudo mount /dev/usbmem_backup1 /media
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        15G  4.2G  9.8G  30% /
devtmpfs        427M     0  427M   0% /dev
tmpfs            87M  260K   86M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           173M     0  173M   0% /run/shm
/dev/mmcblk0p1   56M   21M   36M  38% /boot
none            432M     0  432M   0% /sys/fs/cgroup
/dev/sda1       492M  397M   96M  81% /media
```

ちゃんとmountされた!
mountされた後の名前はちゃんと /dev/sda1 となるらしい
確かに確認してみたら
```
$ ls /dev/sd*
/dev/sda  /dev/sda1

$ ls /dev/usb*
/dev/usbmem_backup  /dev/usbmem_backup1
```

両方同時に存在していることになってるし、usbメモリを引っこ抜くとちゃんと両方共なくなることが確認された

ひとまずこれで、cronとかで自動実行する際も指定した名前のやつでmountしてやればいいので
確実に間違うことなくデバイスを認識させることができる


### ちなみに

[ラトックシステムSCSI用語解説](http://www.ratocsystems.com/products/subpage/scsi/scsi.html)

SCSIデバイス: 
  Small Computer System Interfaceのこと
  SCSIは規格
  物理的仕様(ケーブルやコネクタ、電気信号など)と論理仕様(機器間のやりとりの取り決め)からなります
  代表的なSCSIデバイス
    HDD, CD-ROM, MO, テープ, スキャナ, プリンタ, ZIP

IDEデバイス:
  Integrated Drive Electronicsの略
  フロッピーディスクなど
  今あるのかな?

らしい

