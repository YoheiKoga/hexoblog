title: Hexoで画像を表示させる方法
date: 2016-08-14 16:06:02
tags:
- Hexo
---


# 画像を挿入する

下記ページを参考にした
http://pagent.github.io/2014/12/03/hexo-image/
http://saucer-jp.github.io/2015/04/29/how-to-put-images-in-a-post/

タイトルで日本語を使えるように設定しているので、下記ページを参考に設定しておいてください

[hexoで日本語のパーマリンクを見れるようにしよう！](https://atani.github.io/2015/06/hexo%E3%81%A7%E6%97%A5%E6%9C%AC%E8%AA%9E%E3%81%AE%E3%83%91%E3%83%BC%E3%83%9E%E3%83%AA%E3%83%B3%E3%82%AF%E3%82%92%E8%A6%8B%E3%82%8C%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%97%E3%82%88%E3%81%86%EF%BC%81/)

が、大きさなども変更したかったのでさらなる方法も載せてます

<!-- more -->

Hexoで作成したフォルダ内の _config.ymlの設定で

post\_asset\_folderをtrueにする。

```
post_asset_folder: true
```

で、例えば
```
$ hexo new picture_test
```
とすると、picture\_test.mdの他にpicture\_testというフォルダが作成されるので
その中に適当な画像を入れる(example.jpgとする)
さらに、picture_test.md内で以下のようにしてやれば画像が生成される


// 画像が出力される
{% asset_img example.jpg %}

// テキストリンクが出力される
{% asset_link example.jpg 'サンプル画像です' %}

ただし、このままだとそのままの画像サイズで表示されてしまってちょっと不便

# 画像の大きさを変更したい時

色々調べたところ、htmlのimgタグを使ってやればいいみたい
一番都合よく表示されるのは、_config.yml内のpermalink通りの表示に従って自分でリンク先を指定してやるのがいいみたい
例えば、初期状態だと記事のpermalinkは
```
permalink: :year/:month/:day/:title/
```

のようになっているはずなので、この記事(2016/04/05に投稿)の場合
下記のように記載してやれば
```
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px">
```

下記のように表示されるはずです
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px">


デプロイする前のhexo serverではちょっと表示がおかしくなると思いますが(デプロイしないとパーマリンク先の画像フォルダが作成されないため)
デプロイしてやればちゃんと表示されるはずです


# 画像を中央だけでなくて左寄せや右寄せしたい

で、何故か初期状態だと中央表示になってしまうので、例えば以下のようにimgタグのオプションをいじってやれば位置を変えることができます
例えば `align="left"` を追加してやる。

```
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="left">
```
画像が左に寄ります
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="left">

が、このままだとこのように文章が画像の右側に回りこんでしまうので
<br style="clear:left;">



以下のようにして左寄せを解除した状態で投稿してあげます
```
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="left">
<br style="clear:left;">
```

回り込みが解除されるはずです
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="left">
<br style="clear:left;">
どうでしょう




右寄せしたかったら `align="right"`としてやればいいです
その際は clear:rightを 設定してあげるように
```
<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="right">
<br style="clear:right;">
```



<img src="/2016/08/14/show-picture-on-hexo/example.jpg" width="300px" height="200px" align="right">
<br style="clear:right;">

これで画像が投稿されるようになったよかった


