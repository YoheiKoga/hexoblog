title: mbed Nucleo STM32F401始め
date: 2016-04-05 10:35:02
tags:
- 電子工作
- C++
- mbed
---

# mbedでHello World

今更ながらmbedを扱ってみることに、ひとまずHello Worldをやってみる
格安boardであるmbed Nucleoがあったのでそれで練習

下記ページを参考にした
[mbedではじめよう](http://make.bcde.jp/armmbed/mbed%E3%81%A6%E3%82%99%E3%81%AF%E3%81%97%E3%82%99%E3%82%81%E3%82%88%E3%81%86/)

PCの環境はMac OSX 10.9です

必要なもの
・mbed(なんでもいいですが今回はNucleoを使ってみた)
・PC(なんでもいいですが今回はMacを使ってみた)
・[USB Aオス-miniBオス](http://www.amazon.co.jp/ELECOM-USB%E3%82%B1%E3%83%BC%E3%83%96%E3%83%AB-A-miniB%E3%82%BF%E3%82%A4%E3%83%97-PSP%E5%AF%BE%E5%BF%9C%E3%80%91-U2C-GMM15BK/dp/B003I4F78K)(amazonでもパーツショップでもそのへんの電気屋サンでもなんでもいいので手に入れておく)

# まずはつないでみてみる

STM32F401 Nucleoを箱から取り出してみると、ボードしかありません
パソコンとの接続にはUSBのminiBが必要となります

で、つないでみるとディスクとしてマウントされます
<img src="/2016/04/05/mbed-Nucleo-STM32F401始め/mbedNucleo.jpg" width="300px" height="200px" >

ディスクの中にmbed.htmというファイルがあるはずなので
ブラウザで開きます(たぶんブラウザにファイルをドラッグ＆ドロップしてやればOKです)

そしたら、右側に Open mbed Compiler ってのがあるはずなのでクリックすると

<img src="/2016/04/05/mbed-Nucleo-STM32F401始め/mbedPage.jpg" width="300px" height="200px">

IDE(統合開発環境)が開きます

# プログラムを書き込んでみる

んで、まずはhello_worldをシリアルコンソールに出すサンプルプログラムをインポートしてあげれば動くのが確認できます

mbedのメニューバーから「インポート」をクリックして
「プログラム」タブにした状態で「Nucleo\_hello\_world」とやってやればサンプルプログラムが出てくるので

<img src="/2016/04/05/mbed-Nucleo-STM32F401始め/mbedIDE.jpg" width="300px" height="200px">


ダブルクリックしてやれば、プログラムがインポートされます
あとは、メニューバーの「コンパイル」をクリックしてやれば、コンパイルがうまくいけば
ファームウェアがダウンロード出来るので
デスクトップなどに一旦保存してあげます
で、その後ドラッグ&ドロップでNucleoディスクにプログラムを書き込んであげます
(直接Nucleoディスクにダウンロードするとなぜか動きませんでした)


後は、Macの場合はターミナルでscreenコマンドを打てばいいので

```
$ screen /dev/tty.usbmodem1423 9600
```

のように打ってあげると、5秒に一回 hello world と表示されるはずです

(/dev/tty.usbmodem1423 の部分は、tty. 以降が環境によって異なると思われますので)

```
$ ls /dev/tty.usb*
```

と打ち込んで出てきたやつを使用してください

macでUSBの穴(ソケット)を1つしか使ってない状態ではおそらく1つしか出てこないので

これでmbedの簡単な使い方が出来たので後はプログラムを書いていったり、他の人のソースをインポートしたりすれば
マイコンの練習が出来ます!


ちなみに、screenコマンドだと改行の位置がどんどんずれていってると思いますが
これはscreen側の改行コードが CR+LF の設定になってるため(たぶん)なので

Nucleo\_hello\_worldのプログラムだと2箇所、14行目と17行目の \n となってるところを \n\r としてやればよいです

```
#include "mbed.h"

//------------------------------------
// Hyperterminal configuration
// 9600 bauds, 8-bit data, no parity
//------------------------------------

Serial pc(SERIAL_TX, SERIAL_RX);
 
DigitalOut myled(LED1);
 
int main() {
  int i = 1;
  pc.printf("Hello World !\n\r");		/*ここの行*/
  while(1) { 
      wait(1);
      pc.printf("This program runs since %d seconds.\n\r", i++);		/*ここの行*/
      myled = !myled;
  }
}
```







