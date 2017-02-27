title: Drupalをローカル環境にインストールしてみる
date: 2016-09-07 18:13:08
tags:
- プログラミング
- Drupal
---

# Drupalとは
---

CMSのひとつ
CMSというと、Wordpressが有名だが(世界シェアも1位っぽい)

世界的なオープンソースのCMSシェアではDrupalは第3位
[CMS Market Share](http://www.opensourcecms.com/general/cms-marketshare.php)


けっこう色々なサイトに使われていて、アメリカのホワイトハウスのサイトやNASAのサイトは
Drupalで作られているらしい

[Drupal(ドゥルーパル)というCMSを知っていますか？](http://knowledge.sakura.ad.jp/beginner/1974/)

[Drupalとは](http://www.webgogo.jp/archives/node/409)

<!-- more -->

# Drupalは中規模から大規模なサイトに向いている?
---

wordpressは個人向けのブログやサイトに向いている中で

Drupalはカスタマイズ性が高く、プログラミング無しでも結構構築していけて
中規模から大規模なサイトに向いてるらしい

[世界の50サイトにひとつはDrupal！](http://www.dgcircus.com/drupal/cms)

[Drupal 開発者から見た Drupal 向きのサイトとそうでないサイト](https://drupal.studio-umi.jp/blog/Website-types-Drupal-good-with-bad-with)


普通に企業サイトを作りたいとか、どんどん更新していくようなサイトはDrupalが向いてるみたい

反対に、小規模なサイト(ブログのみ)とか一切更新が無いサイトの場合はDrupalを使わずとも
他のツールを使ったほうが簡単で低コストという感じかな

というわけでひとまずインストールまでやってみる

<br>

# Drupalをつかってみる
---

とりあえずローカル環境にDrupalをインストールしてやってみる

今回はローカル環境でMAMPを使った(入ってたので)けど、Drupalには[Acquia](https://www.acquia.com/)という開発環境をセットアップして用いるのが

推奨されてるみたいなのでそっちを用いたほうが良いと思われる

### MAMPを使う

MAMPの使い方は省略する(下記リンクなどを参照)
http://pc-karuma.net/mamp-install-mac/

ちょっと古いが、MAMP3.4を使用している(アップデートせねば・・・)

で、MAMPにDrupal用にデータベースを作成する

MAMPを立ち上げてスタートページを開く
(何故か「オープンWebStartの」という変な日本語になってるがここをクリック)


<img src="/2016/09/07/how-to-use-drupal/MAMP1.jpg" width="300px" height="200px">


ブラウザでMAMPのスタートページが開くので、phpMyAdminを開く

<img src="/2016/09/07/how-to-use-drupal/MAMP2.jpg" width="300px" height="200px">


メニューがいくつかならんでるので(データベースとかSQLとか状態とか)
データベースをクリック

↓

データベース名を適当に入れる。今回はdrupal-site01とした。
照合順序は`utf8_general_ci`を選択する

↓

作成ボタンをクリック

これでMAMPの準備は出来たので、次にDrupalのインストール手順に進む

<br>

### Drupalをインストール

MAMPで設定したディレクトリ下に、下記サイトからダウンロードしたDrupalファイルフォルダを入れておく

https://www.drupal.org/download

2016年9月現在、最新版はDrupal 8.1.8だった(2016年7月に新しくでたやつらしい)

で、MAMPが起動している状態でブラウザからlocalhostに接続する

例えば自分だと、`localhost/CMS/drupal`という構成にした(Drupalのファイルを解凍した後、フォルダ名をdrupalに変更した)

多分Drupalをインストールしますみたいな画面が出てくるので(画像撮り忘れた)
後は指示に従って進んでいく

下はインストールプロフィールを選ぶ場面
とりあえず標準で次へ

<img src="/2016/09/07/how-to-use-drupal/install1.jpg" width="300px" height="200px">


必要条件の検証が出る
自分の場合はPHP OPcodeのキャッシングが無効になってたが
その他が大丈夫だったのとまぁローカルな環境なのでとりあえず先に進んだ


データベースの設定では

データベースはMySQLを選択

データベース名: MAMPで設定したデータベース名を入れる。例えば先ほどは`drupal-site01`としたのでそれを入力する

データベースのユーザ名とパスワードはMAMPの設定のやつを入れる
(MAMPのスタートページで確認可能)
自分は初期状態のrootのままでやってる

ので、下記画像のようになるはず

<img src="/2016/09/07/how-to-use-drupal/install2.jpg" width="300px" height="200px">

サイトのインストールが始まるので暫し待つ

インストールが終わったら、あとはサイトの情報
(ユーザー名とかメールアドレスとかタイムゾーンとか)
が出てくるので適宜好きなように設定して次へ進む

これで設定が完了した!

こんな画面が出たら成功っぽい

<img src="/2016/09/07/how-to-use-drupal/install3.jpg" width="300px" height="200px">


後は使ってみるのみ
