title: AWSでWordPressを構築する際に調べたこと
date: 2016-09-14 13:35:52
tags:
- wordpress
---

WordPressで簡単なポータルサイトを作ろうと思って

AWSを使ってみようと思ってるが、サービス内容が多すぎて何が何やらわからない・・・

ので調べてみた

下記内容の結論は、小規模なポータルサイトを作る場合に向けたものを想定


*** 注意 ***

ただし、これらの方法ではまだhttps対応はしてないのでhttps対応させるためにはをもう少し調べないといけない

[グーグル、「Chrome」でHTTP接続を「安全でない」として警告表示へ](http://japan.zdnet.com/article/35088797/)

<!-- more -->

<br>

---
# AWSでWordpressやるなら EC2 だけでひとまずいいのかな
---

ひとまず、[公式のチュートリアル](https://aws.amazon.com/jp/getting-started/tutorials/launch-a-wordpress-website/)のとおりにやってみて

EC2内にWordpress用のイメージ(Bitnami)があったのでやってみてたけど

SSH接続は出来ない設定だったのとどうもデータベースもEC2内で完結しているようで

<br>

同じ問題にあたってる方もいらっしゃたようですし

[AWSでSSHログイン失敗からのWordpress設置](http://syake.hatenablog.com/entry/2016/08/15/234613)

それがいいのかどうかすらわからなかったので調べてみた

<br>

---
# まず作ってみるならEC2にAMIのbitnamiで、Linuxも触れたい＆余裕があるならEC2 + RDS
---

結論から言うと、ひとまず最低限AWSでWordpressを稼働させるためには後々のことも考えると

EC2 + RDS 

の組み合わせで作ると良い、みたい

現時点(2016年9月)では下記リンクを参考につくればいいんじゃないかな

[AWS Amazon EC2上でWordPressを動作させる方法 – EC2のインスタンス作成からMySQLの設定まで](https://zxcvbnmnbvcxz.com/amazon-ec2wordpress/)


ただし、Linuxをちょっと扱うことになるのと
RDSの使用料金もかかるみたいなので

そういうのがわからない場合はAMIである

Bitnamiを使うと何も考えずWordPressを立ちあげれるので良さそう


<br>

以下は調べていったメモ書き

<br>

---
# いろいろある
---

AMI(Amazon machine image)にもいろいろあって

Amazon Linuxとか、そもそもwordpressを構築するためにあるBitnamiとかAMIMOTOとかあるけど

よくわからないので調べてみた

<br>

参考サイト

### Amazon公式チュートリアル

[WordPress Web サイトの起動](https://aws.amazon.com/jp/getting-started/tutorials/launch-a-wordpress-website/)

[チュートリアル: Amazon Linux を使った WordPress ブログのホスティング](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/hosting-wordpress.html)

<br>

### その他別のサイト

[AWS Amazon EC2 + Amazon RDSを使ってWordPressを構築する](https://tsuchikazu.net/aws_amazon_ec2_rds_wordpress/)

[AWS設定やった手順まとめ](http://totutotu.hatenablog.com/entry/2015/08/30/AWS%E8%A8%AD%E5%AE%9A%E3%82%84%E3%81%A3%E3%81%9F%E6%89%8B%E9%A0%86%E3%81%BE%E3%81%A8%E3%82%81)

[0から始めるAWS入門：概要](http://qiita.com/hiroshik1985/items/6433d5de97ac55fedfde)

[【AWS】WordPressのインストールとRDS、S3の連携](http://qiita.com/hisayuki/items/d4bd05fa152b1e54a43d)

[AWSでWordPress構築 (EC2+RDS+ELB)](http://qiita.com/yarakaki/items/b104392ed7d742186631)

[WordPressを5分でセットアップする方法：Amazon AWS編](http://do-you-linux.com/blog/2014/08/25/post-3695/)


<br>

調べててわかったのは、色々な単語も出てくるので

まずはドットインストールあたりでAWSの必要な用語や知識をさらっていくのがいいのかもしれない

[ドットインストール Amazon Web Services入門](http://dotinstall.com/lessons/basic_aws)

あとは、なんとなく単語がわかってきてもう少し詳しく知りたい場合は下記qiita記事をおっかけていくのも良さそう

[0から始めるAWS入門：概要](http://qiita.com/hiroshik1985/items/6433d5de97ac55fedfde)

<br>

---
# Wordpress構築済のAMIはどうなのか
---

AMIにBitnamiというのがあって、Wordpress構築済であるやつ

AWSでWordPressを作成する際の[公式チュートリアル](https://aws.amazon.com/jp/getting-started/tutorials/launch-a-wordpress-website/)でもこれが使われてる

ただ、これでいいのかわからないので調べてると下記サイトがヒットして

[AWS Amazon EC2 + Amazon RDSを使ってWordPressを構築する](https://tsuchikazu.net/aws_amazon_ec2_rds_wordpress/)

それによると、WordpressのAMIを使用するのは

- メリット
    - ものすごく簡単に構築できる

- デメリット
    - EC2のAMIで完結してしまうのでDBはEC2内に構築されるしRDSを使用するものでない点

ということである

スケールアップしない(大規模なサイトではない)場合だったら別にWordpressのAMIでもいいのかな？

という感触

もちろん後からRDSを使いたい場合はちゃんと乗り換えもできるっぽい(後述)

<br>

---
# WordPress構築済のAMI
---

なんか検索してるとAMIMOTOってやつとbitnamiというのが出てくる

## AMIMOTO

AMIMOTOはWordPressのクラウドホスティングサービスらしい
(管理してくれるところってイメージ？かな)

公式サイト
[AMIMOTO](https://ja.amimoto-ami.com/)

AMIMOTOは色々裏でやってくれてパフォーマンスも良くだいたい月2000~3000円ぐらいかかるのかな?

[網元HMV（AWS EC2 t2.micro + WordPress）の個人ブログ向けエコな運用とお値段](http://gatespace.jp/2014/09/29/amimoto-aws-ec2-t2-micro-cost/)

[お安くAMIMOTOを利用する（うちのブログは月額０円？）](http://gatespace.jp/2015/12/03/amimoto-save-cost/)

年間払いとかそういう仕組でやっていけば、普通のレンタルサーバよりもちょっとクオリティの良いものが
それより安く使えるとかできそう

## bitnami

bitnamiもクラウドホスティングサービスみたい

公式サイト
[bitnami](https://bitnami.com/)

特徴としては、2つあって
1. ひとつのWordpressで複数のサイトを簡単に持てる
2. オープンソースなのでイメージ使用料無料

とのことらしい

参考
[WordPressを5分でセットアップする方法：Amazon AWS編](http://do-you-linux.com/blog/2014/08/25/post-3695/)

小規模サイトだとひとまず試しにはこっちがいいのかな？

<br>

---
# RDSって？
---

Amazon Relational Database Service (Amazon RDS)のことらしい

手間のかかるデータベースの管理タスクを代わりに行ってくれるらしい

なんかいろいろ面倒みてくれるってのはいいけど実際どうなんだろこれ？

必要あるのかな

=> ただただWordPressを使いたいだけ、という場合なら無くても良いっぽい

とりあえず下記記事がRDSの内容について述べててくれている

[http://dev.classmethod.jp/cloud/aws/cm-advent-calendar-2015-aws-re-entering-rds/](http://dev.classmethod.jp/cloud/aws/cm-advent-calendar-2015-aws-re-entering-rds/)

<br>

---
# RDSを使う必要性は?
---

そもそもRelational Database(RDB)とは

> データベースの構造の一つで、1件のデータを複数の属性の値の組として表現し、組を列挙することでデータを格納していく方式
属性を列、、組を行とする表(テーブル)の形で示されることが多い
最も普及している方式で、単にデータベースといった場合はRDBであることが多い


ということは・・・Wordpressを使うとして、Amazon RDSを使わないとむしろどうなるの？

=> どうやら自分でDBを構築しないといけなくなるらしい、もしくは構築済のAMIを使う

[(DB・サーバー構築編)世界一丁寧なAWS解説。EC2を利用して、RailsアプリをAWSにあげるまで](http://qiita.com/naoki_mochizuki/items/22cfbf4bf7ec95f6ac1c)


小規模なサイトであるならWordpressの乗ったAMIでも良さそうだけど、自分で構築するのは面倒だな(管理とか)

もし自分でDBを構築させてやってた場合は

AWSのEC2でアプリケーションを動かしていて、RDSにDBを移行させている人がいたので下記参照

[Amazon RDSへWordPressのDBを移行する方法](http://toach.click/rds-wordpress/)

<br>

---
# その他調べてて出てきた用語
---

### S3

Amazon Simple Storage Serviceのこと

[Amazon S3 とは何ですか？](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/Welcome.html)

Amazonで使えるストレージ(データの保存先)のことで、ポータルサイトぐらいだと

特にデータが圧迫することも無さそうなので必要ないかな

ただ、メリットとしては

>EC2はWordpressのみが乗っているAPサーバとし、データベースやファイル保存先を別にすることでEC2は簡単に複製・削除が可能になります

(引用: [【AWS】WordPressのインストールとRDS、S3の連携](http://qiita.com/hisayuki/items/d4bd05fa152b1e54a43d))

とのことなので、なんか色々サイトを複製したいとか言うことがある場合はこういう手段もありっぽい

<br>

### ELB

Elastic Load Balancing 

エラスティックロードバランサーのこと

Elastic Load Balancing (https://aws.amazon.com/jp/elasticloadbalancing/)

・・・って何？

どうやら負荷をEC2間で分散させて軽減するためのもの？っぽいが

とりあえずロードバランサーは

> ロードバランサとは、外部から送られてくるデータや処理要求を、同等に機能する複数の装置に振り分けて
一台あたりの負荷を抑える装置。単にロードバランサという場合は、ネットワーク上でサーバの負荷を分散するサーバロードバランサを指すことが多い

(引用: [IT用語辞典 ロードバランサ](http://e-words.jp/w/%E3%83%AD%E3%83%BC%E3%83%89%E3%83%90%E3%83%A9%E3%83%B3%E3%82%B5.html))

らしい

やはり小規模なポータルサイトぐらいだと、そんなにアクセスが集中するみたいなことも無さそうなので

特には必要ないかと思われる

ちなみに、2016年8月ぐらいに新しいロードバランサーであるALBが発表されたらしい

[【新機能】新しいロードバランサー Application Load Balancer（ALB）が発表されました](http://dev.classmethod.jp/cloud/aws/alb-application-load-balancer/)


<br>

## PuTTY (Windowsの方のみ関係ある)

freeのSSHとTelnetのclientらしい
http://www.chiark.greenend.org.uk/~sgtatham/putty/

Macだとターミナルがあるから必要ないかな