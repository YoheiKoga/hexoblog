title: Particle Electronを使ってみる
date: 2016-08-14 15:53:58
tags:
- 電子工作
---

# Particle Electron
Particle Electronを入手したので、公式のチュートリアル(Particle GUIDE)を読んでみる
https://docs.particle.io/guide/getting-started/intro/electron/

気になるところをメモ

### Electron3G本体は US$69

月$2.99で1MBまで、さらに1MB追加ごとに$1.99
https://store.particle.io/?product=particle-electron&utm_source=Proto&utm_medium=Button&utm_content=Electron&utm_campaign=Buy#electron-3g-americas

```
$3 per month for 1MB and 99 cents for every megabyte afterwards
```

ちなみに、購入したらSIMカードが入ってるので、オンライン上でクレジットカード登録すれば直ぐに使い始めれます便利!
https://docs.particle.io/guide/getting-started/start/electron/

その他電池(リチウムイオンポリマー(Lipo))とかUSBケーブルとかアンテナとか必要なものは最初から同封されてるのでこれだけでもまずは始めれます

2Gの通信規格が使えたらもう少し安いが、日本では3Gの通信規格しか電波が使えないので3Gを選ぶように



### 消費電力は 800mA ~ 1800mA
データシートを眺めてみると

https://docs.particle.io/datasheets/electron-datasheet/

GSM module (3Gモジュール) が だいたい800mA ~ 1800mA消費するようです
```
u-Blox GSM module on the Electron alone can consume a peak of 800mA to 1800mA
```

消費電流は180mAから1.8Aまで(5V)で変化するが、ディープスリープモードだと130μAの消費になる
```
Typical current consumption is around 180mA and upto 1.8A transients at 5VDC. In deep sleep mode, the quiescent current is 130uA (powered from the battery alone).
```

付属の電池が2000mAh(2000mAの電力を消費するデバイスを一時間使用可能)なので、ずっと使い続けると1日持たないのかな、という感じ
(スマホや携帯と似たような間隔ですね)
このあたりはGPSモジュールやWiFiモジュールや3G通信の課題かな

ちなみにUSBは最大500mAしか供給できないので、基本的には電池を接続したまま使用するということになるようです
3Gモジュールの電源はUSB以外にbattery(Lipo、リチウムイオンポリマー)を接続しておく必要がある
ということはバッテリーはそこそこで切れてしまうかもだけど
充電は簡単

```
Most USB ports can supply only a maximum of 500mA, but the u-Blox GSM module on the Electron alone can consume a peak of 800mA to 1800mA of current during transmission. In order to compensate of this deficit, one must connect the LiPo battery at all times when powering from a traditional USB port. The Electron will intelligently source power from the USB most of the time and keep the battery charged. During peak current requirements, the additional power will be sourced from the battery. This reduces the charge-discharge cycle load on the battery, thus improving its longevity.
```

実際、Lipoをくっつけた状態でUSBと接続させたら赤色のLEDが点灯して、充電中になった
この状態でももちろん使用可能

### Getting Started
https://docs.particle.io/guide/getting-started/start/electron/

USB接続すると充電状態になる
で、充電状態だと小さなほうのLEDが赤く光る

```
ONBOARD POWER MANAGEMENT
The Electron charges its own battery!
If the small red LED is on, the battery is charging
When the LED turns off, the battery is fully charged
```

基板上のMODEボタンを1回押すと、LEDが緑色に点灯して接続強度を5段階で示してくれる
(緑色の点滅回数で最大5回)

```
DISPLAY SIGNAL STRENGTH!
Press MODE once quickly when the Electron is breathing cyan
The signal strength (RSSI) will be shown in a 0-5 green blinks, 5 being the strongest
```


MODEボタンを2回素早く押すと、LEDが白く光って数秒後消灯する(SOFT POWER DOWN モード)
再起動させたい場合は、RESETスイッチを押す
SOFT POWER DOWNモードでの消費電流は約0.13mAである
単純に2000mAhの電池なので、満充電後だと繋ぎっぱなしでも約15384時間は持つ(約640日)
イメージがサイトにあった
https://docs.particle.io/guide/getting-started/modes/electron/#listening-mode

```
SOFT POWER DOWN

Tap MODE twice quickly, then the LED will show white, and then it will turn off in a few seconds
To turn it back on tap RESET once
You can use use soft power down instead of unplugging the battery or power
This uses a deep sleep mode for the Electron, and will still use 0.13mA
```


### OVERVIEWより

particleの月の通信容量は初期設定だと5MBが上限に設定されている(無限にデータを上げないように配慮されている)
ダッシュボードで容量を変更することもできる(最初は練習がてらなので、不安なら上限を落としておいたほうが良いかもしれない)
変更は Dashboardのbillingタブを開いて、estimated costの横にある「・・・」をクリックすると出てきます
https://dashboard.particle.io/user/billing

参考画像
<img src="/2016/08/14/particle-electron-beginning/datalimit.png" width="400px" height="300px">


```
OVERVIEW

Each SIM card will be billed a base rate which includes 1 MB of data
The base rate covers you up to 1.0MB, additional MB are billed at a cheaper rate than the base
We bill your base rate at beginning of a period, additional MB at the end, so you'll often see both
Base and additional MB rates are based on your country and Zone
You can set a data limit for each SIM. It defaults to 5MB on new SIMs, but you can change it in the Dashboard
Data limits are soft maximums; we only charge you for the number of MB used, rounded up, and we'll cut off usage as quickly as we have updated metering from your carrier
If a SIM goes over the limit, it'll be paused and won't be able to use more data until the beginning of the next period or you raise the data limit
You can use the Dashboard to manage SIMs, billing, and see data usage
See the full Electron Billing Guide
```



### iphone / Androidアプリの使い方
https://docs.particle.io/guide/getting-started/tinker/electron/

ピンの名前部分をタップすることで配線を設定できる
解除する場合はピンを長押しする
```
To begin, tap any of the pins. A menu will pop up showing the functions that pin has available, tap a function name to select the pin functionality. You can reset the pin function by long-pressing it. Each pin can have up to four possible functions:
```


最大の電圧は3.3Vなので注意
```
digitalWrite: Sets the pin to HIGH or LOW, which either connects it to 3.3V (the maximum voltage of the system) or to GND (ground). Pin D7 is connected to an on-board LED; if you set pin D7 to HIGH, the LED will turn on, and if you set it to LOW, it will turn off.

analogWrite: Sets the pin to a value between 0 and 255, where 0 is the same as LOW and 255 is the same as HIGH. This is sort of like sending a voltage between 0 and 3.3V, but since this is a digital system, it uses a mechanism called Pulse Width Modulation, or PWM. You could use analogWrite to dim an LED, as an example.

digitalRead: This will read the digital value of a pin, which can be read as either HIGH or LOW. If you were to connect the pin to 3.3V, it would read HIGH; if you connect it to GND, it would read LOW. Anywhere in between, it'll probably read whichever one it's closer to, but it gets dicey in the middle.

analogRead: This will read the analog value of a pin, which is a value from 0 to 4095, where 0 is LOW (GND) and 4095 is HIGH (3.3V). All of the analog pins (A0 to A7) can handle this. analogRead is great for reading data from sensors.
```

間違っても5Vなどを入力しないこと
(最初のチュートリアルではあまりそんなことは無さそうです)


# firmwareの書き込み方

### USB経由で
https://docs.particle.io/guide/tools-and-features/cli/electron/#flashing-over-serial-for-the-electron
まずは、マシンとUSBでElectronと接続しておく

1.
Electronをlistening modeにしておく
Modeボタンを3秒間押しっぱなしにする。LEDが青色に点滅する
https://docs.particle.io/guide/getting-started/modes/electron/#listening-mode

2.
particle cli をnpmでインストールしておく(要node.js)
インストールしている場合はこの手順は不要
https://docs.particle.io/guide/tools-and-features/cli/electron/#installing

$ particle login
で、自分のparticleと連携できるようにしておく

さらに、dfu-utilとopensslをインストールしておく
http://community.particle.io/t/tutorial-particle-cli-on-mac-osx-26-sep-2015/5225

要 rubyおよびHomebrew(HomebrewをインストールするのにRubyが、dfu-utilをインストールするのにHomebrewが必要)
$ brew install dfu-util

opensslは以下のようにインストールする
参考
http://qiita.com/Chrowa3/items/b04e772be959cdda9ac3

3. 
particle web IDEのコードをコンパイルして、firmwareをインストールしておく
「< >」みたいなマークをクリックすると、REMOVE APP とか CREATE NEW APP とかのボタンがあるサイドテーブルが出てくるので
そこのクラウドボタン(雲のマーク)をクリックすると、コンパイル後 firmware.bin のインストールが始まるので、自分のパソコンの適当なディレクトリに保存しておく

4.
firmware.binを保存したディレクトリまで(Macの場合はterminalで)移動して、下記のコマンドを実行する
$ particle flash --serial firmware.bin

```
! PROTIP: Hold the SETUP button on your device until it blinks blue!
? Press ENTER when your device is blinking BLUE 
```

と出てくる。「今からファームウェア書き込むけどちゃんとリスニングモードになってる？OKだったらENTERキーを押してね！」
って意味なのでENTER キーを押す


これで書き込みが出来る...はずだが、できない・・・

--- 2016年3月25日追記
とりあえずフォーラムで同じ現象が起きてて質問してる人がいたのでそれで解決した
https://community.particle.io/t/solved-can-upgrade-electron-ota-but-not-from-cli/20209

解決1. DFU Modeで書き込みを行う

まずは試しにDFU経由での書き込みを行ってみる

```
$ particle flash --usb firmware.bin

running dfu-util -l

!!! I was unable to detect any devices in DFU mode...

> Your device will blink yellow when in DFU mode.
> If your device is not blinking yellow, please:

1) Press and hold both the RESET/RST and MODE/SETUP buttons simultaneously.

2) Release only the RESET/RST button while continuing to hold the MODE/SETUP button.

3) Release the MODE/SETUP button once the device begins to blink yellow.


Error writing firmware...No DFU device found

```
DFU Modeになってないので書き込みできませんよ、みたいなのが出てくるので
これにならってDFU Modeに以降する

RESETボタンとMODEボタンを同時押しすると、LEDが消灯する
↓
RESETボタンだけ離すと、一旦LEDが紫色に点滅した後、黄色に点滅する
↓
黄色に点滅したら、MODEボタンを離す

これでDFU Modeに移行するので、後は書き込みコマンドを実行する

```
$ particle flash --usb firmware.bin 

...
Flash success!
```
最後に Flash seccess!と出れば成功!
なんかよくわからないけどParticle側のバグかなんかなのかな・・・しばらくはこの方法でしのごう



### over the air(OTA), インターネット経由で

Particle IDEを開いた状態でイナズママークみたいなやつ(Flash)をクリックすると出来ます
注意点としては、通信に数KBかかるので、データ量をコントロールしたいならUSB経由のほうがいいみたいです


以上ひとまず現状ここまで





