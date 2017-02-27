title: Drupalデスクトップ開発環境のAcquia Dev Desktopを入れる(エラーが出るので回避する)
date: 2016-09-16 16:51:00
tags:
- Drupal
---

Drupalに入門してみてる

[Drupalをローカル環境にインストールしてみる](https://yoheikoga.github.io/2016/09/07/how-to-use-drupal/)


前回はローカル環境をMAMPで行っていたけど

Drupalは開発環境としてクラウドやデスクトップツールを提供している[Acquia](https://www.acquia.com/)という会社のツールがあるので

デスクトップ開発環境としてAcquia Dev Desktopを入れてみる


ちなみに、Acquia Dev Desktopにdrupal8を入れる際になんかインストールの段階で下記のようなエラーが出るが

`Error The website encountered an unexpected error. Please try again later.`


Acquia経由でDrupalをダウンロードするのではなく、公式サイトから落としてきたやつを使えば解決できそうなので

そうするように注意

<!-- more -->

<br>

# インストール

インストールは下記公式のstepsを参考にした

[Installing Acquia Dev Desktop](https://docs.acquia.com/dev-desktop/install)


英語だが下記Youtubeも参考になる
[Drupal 8 Beginner, Lesson 5: Installing Acquia's Dev Desktop](https://www.youtube.com/watch?v=4kvYfxOR3GY&list=PLtaXuX0nEZk9MKY_ClWcPkGtOEGyLTyCO&index=5)


まずは下記ページでツールをダウンロードする
https://dev.acquia.com/downloads

Macの場合はMAC DOWNLOADを、Windowsの場合はWIN DOWNLOADを選択

<br>

Linuxの場合はコマンドラインからインストールするのかな？

おそらく下記がやりかた

https://docs.acquia.com/acquia-drupal/install/other


自分はMacなのでMAC DOWNLOADからダウンロードした


### インストーラーを実行していく

ダウンロードしたらInstaller.appが出て来るので

ダブルクリックで開く(これはインストーラーなのでアプリケーションフォルダに入れないように注意)


あとはどんどん進んでいけば良い、基本的にずっとNextをクリックしていけばよいが

install locationsが出てきたらすきなディレクトリ(フォルダ)を選択する

初期状態だと下記のようにデスクトップが選択されているので好きなところを選ぶ


<img src="/2016/09/16/install-acquia-dev-desktop/install.jpg" width="300px" height="200px">


とりあえずデフォルトでやってみる

次に、進むと、portの設定があり

何かconflict(設定がかぶる)場合があるなら変更するが

基本的にはそのままで大丈夫だと思われる(ここは各自の設定による)のでそのままNextしていく

installが始まるのでしばし待つ

終わったらボタンがFinishになるのでなるのでfinishしてアプリを立ち上げる

これでインストールは終わり


<br>

# とりあえずscratchをやってみる

Acquia Dev Desktopアプリを開くと、三種類のボタンが出てきて

- Scratch: Drupalをインストールして始める？

- existing Drupal site located on my computer: 既にパソコンの中にDrupalが入ってるならこれ

- existing Drupal site hosted on Acquia Cloud: Acquiaのクラウドに入ってるDrupalがあるならこれ


なのでとりあえず新しくDrupalをいれてやってみる

Youtubeのレッスンを参考にした

[Drupal 8 Beginner, Lesson 5: Installing Acquia's Dev Desktop](https://www.youtube.com/watch?v=4kvYfxOR3GY&list=PLtaXuX0nEZk9MKY_ClWcPkGtOEGyLTyCO&index=5)


一番上のscratchを選択する

<br>

### Drupal8を選ぶ

---

**注意**

どうやら、Acquia経由でDrupal8をインストールしようとしたらよくわからないエラーが起きてしまうので

Drupal自体は自分でダウンロードしたのをやってみましょう！

以下は一応流れに沿ってやってみてるけど飛ばすべし

---


************************ ここから飛ばす

次に色々と選択肢が出て来るが

2016年9月現在はDrupal8が一番新しいのでDrupal8を選ぶ

<img src="/2016/09/16/install-acquia-dev-desktop/install2.jpg" width="300px" height="200px">


Lightningはサイト構築のためのフレームワークでドラッグ・アンド・ドロップなどでサイトを構築できる？ものっぽい

[Lightning](https://www.drupal.org/project/lightning)

[Drupal 8 Quick Start: An Overview of Lightning](http://www.slideshare.net/AcquiaInc/drupal-8-quick-start-an-overview-of-lightning)


これは今度触ってみよう

<br>

### Drupal8をインストール


Drupal8をクリックしたら次にフォルダ構成などが出てくるので適宜変更する

自分は 

Local codebase folderの部分のdrupal-8.x.xみたいになってたところをdrupal-8に

Local site name の部分をdrupal-8に


あとはそのままでFinishをクリックするとDrupalのダウンロードが始まる


### Localサイトに進む

インストールが終わると、画面が切り替わってデータベースとwebサーバーが起動した状態の画面になるので

一番上の`Local site`のアドレス(たぶん http://drupal-8.dd:8083 みたいになってるはず)をクリックする

ブラウザが開いて、下記画像みたいなページが出てきたら成功

<img src="/2016/09/16/install-acquia-dev-desktop/install3.jpg" width="300px" height="200px">


で、continueをクリックすると・・・なんか失敗する・・・？

`Error The website encountered an unexpected error. Please try again later.`

とか出てきて続かない・・・


## 日本語でインストールしようとしてるからダメなのかな？

英語でやってみる

=> やっぱりだめ

## 自分のローカルに既にあるDrupalでやってみる

[以前入れていたDrupal](https://yoheikoga.github.io/2016/09/07/how-to-use-drupal/)でやってみる

やりかたがわからん・・・


********************************* ここまでとばす


<br>

---

# Drupalのダウンロードは自分でやる

公式サイトからDrupalだけ落としてきてやってみる

=> なんか出来た!!

というわけでこれでやってみよう

[公式サイト](https://www.drupal.org/project/drupal/releases/8.1.8)からDrupalをダウンロードする

tar.gzでもzipでもいいが自分はMacなのでtar.gzのほうを選択した

で、解凍したやつをどこか適当なディレクトリに入れておく


Acquiaで、新しくDrupalを入れるボタンのうち真ん中の

- existing Drupal site located on my computer

のやつを選択する

で、一番上で先程Drupalをいれたフォルダを指定して、データベース名などは好きなように設定する

あとは、Acquiaが立ち上がったらLocal siteのとこのアドレスをクリックして開いて

指示に従っていけば良い


Local databaseの名前は先程設定したやつなので

Acquia Dev Desktopを開いたところにかいてあるはずなのでそれを参照

データベースのユーザー名は好きなようにしておくがデフォルトでいいんじゃないかな


後はインストールが終わるのを待ってしまえばサイトが開くはず

