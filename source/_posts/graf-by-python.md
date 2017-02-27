title: pythonのグラフ描画について
date: 2016-08-14 16:30:52
tags:
- python
---

# 基本的にはmatplotlibが良い

技術評論社から出ている[『データサイエンティスト養成読本　機械学習入門編』](http://gihyo.jp/book/2015/978-4-7741-7631-4)を読んでみても、グラフ化の第一選択肢は`matplotlib`みたいです

> "matplotlibは、Pythonでグラフや図などを描画するためのライブラリです。
データサイエンスの世界で、データを可視化したい時に威力を発揮します"
														(特集2 Pythonによる機械学習入門 より引用)

ネットで検索してみても、どうやらmatplotlibが良く使われているような印象を受けます

[MATLABユーザのためのPythonグラフ作成ツールmatplotlib入門](http://myenigma.hatenablog.com/entry/2015/08/30/223559)
[matplotlibでリアルタイム描画](http://qiita.com/hausen6/items/b1b54f7325745ae43e47)
[matplotlibを使ってPythonでグラフを表示する](http://symfoware.blog68.fc2.com/blog-entry-1416.html)
[matplotlib入門](http://bicycle1885.hatenablog.com/entry/2014/02/14/023734)

さらに、派生したものでSeabornという綺麗なグラフが描けるものもあるみたいです
[簡単に美しいグラフ描画ができるPythonライブラリSeaborn入門](http://myenigma.hatenablog.com/entry/2015/10/09/223629)


MacやLinuxなどパソコンをそのまま用いるなら上記のもので
グラフ化を行うのが良いみたい

　　
　　

## センサーからの値をRaspberryPiなどを用いてグラフ化する場合さらに色々なもの(ライブラリ)が用いられている

センサをラズパイなどで取得する場合はさらに色々なのがあって
　　
　　

### matplotlibを用いたもの
[Raspberry piで大気圧と温度を記録してグラフにして他のPCからグラフを見る(メモ)](http://d.hatena.ne.jp/beiz23/20140726/1406378550)

[USBマイクの音量をリアルタイムプロットする on raspberry pi 2 B](http://iroha112233.hatenablog.com/entry/2016/03/24/231620)
　　
　　

### muninを用いたもの

[気圧センサBMP085をRaspberryPiに接続しグラフ表示](http://blog.bnikka.com/raspberrypi/bmp085raspberrypi.html)
[muninで温度湿度のグラフを表示させたい](http://bey.jp/?p=15709)
　　
　　

### Google Chartsを用いたもの

[Rapsberry Piで気温のログ＋Google Chartsでグラフ化](http://atelier-orchard.blogspot.jp/2014/02/rapsberry-pigoogle-charts.html)
　　
　　

### smbusを用いたもの

[センサの記録をグラフ化](http://make.bcde.jp/raspberry-pi/%E3%82%BB%E3%83%B3%E3%82%B5%E3%81%AE%E8%A8%98%E9%8C%B2%E3%82%92%E3%82%B0%E3%83%A9%E3%83%95%E5%8C%96/#i-4)　　
　　

### pubnubを用いたもの

[Raspberry pi + pubnub で温度と湿度のリアルタイムグラフ表示](http://www.itbook.info/web/2015/07/raspberry-pi-pubnub-%E3%81%A7%E6%B8%A9%E5%BA%A6%E3%81%A8%E6%B9%BF%E5%BA%A6%E3%81%AE%E3%83%AA%E3%82%A2%E3%83%AB%E3%82%BF%E3%82%A4%E3%83%A0%E3%82%B0%E3%83%A9%E3%83%95%E8%A1%A8%E7%A4%BA.html)
　　
　　

### dygraphsを用いたもの

[RaspberryPiとDS18B20で温度を計ってdygraphsでグラフ化](http://dkpyn.com/blog/2344)


などが引っかかってきました


# 外部ライブラリを用いたもの

### Node.js + ccchart.js

[Raspberry Piとセンサーでヘルス情報の取得（心拍数編）](http://bitwave.showcase-tv.com/raspberry-pi%E3%81%A8%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC%E3%81%A7%E3%83%98%E3%83%AB%E3%82%B9%E6%83%85%E5%A0%B1%E3%81%AE%E5%8F%96%E5%BE%97%EF%BC%88%E5%BF%83%E6%8B%8D%E6%95%B0%E7%B7%A8%EF%BC%89/)



# 外部サービスを用いたもの

### Home Assistant

[メモ：Home Assistant + Raspberry Pi + センサー でホームオートメーションしたい #1](http://qiita.com/spicemanjp/items/59032bad6c93c5ac28ab)

### IoT.kyoto VIS + AWS DynamoDB

[RaspberryPiでセンサーデータをfluentdを使ってDynamoDBに送って可視化してみよう＜上級編＞](http://qiita.com/Ichiro_Tsuji/items/05a3f84b1d6c8d6e21d7)

このあたりどれを使うか見極めたら、さらに調べてみるのがいいと思われる

