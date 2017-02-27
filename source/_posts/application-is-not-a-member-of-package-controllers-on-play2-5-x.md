title: play2.5.xでApplication is not a member of package controllersが起こる
date: 2016-08-14 16:42:26
tags:
- Scala
- Play Framework
---


結論から言うと、Play2.5.xではcontrollersでobjectではなくclassで定義してあげないといけないみたい
(2016年5月10時点)

ついでにPlay Frameworkの使い方を少し捕捉してみた

<!-- more -->

---
# ScalaでPlay Frameworkを始めてみた
---

(情報も少ないしめっちゃ難しいので苦戦してる)

下記ページに、Hello Worldを表示するアプリケーションの最小限の構成についての説明があったのでやってみた
[PlayではじめるScalaなWebアプリケーション](http://catcher-in-the-tech.net/2300/)

自分はPlay Frameworkのversionは2.5.3を用いています

が、いざrunしてみたところ
Compilation error
type Application is not a member of package controllers
GET / controllers.Application.index

みたいなのが表示されてしまったので解決方法と、Playのバージョンが異なるので変更点を

---
# objectの代わりにclassを使う
---

検索してみたら、色々と言及されてる
[type “” is not a member of package controllers play framework](http://stackoverflow.com/questions/33769851/type-is-not-a-member-of-package-controllers-play-framework)

[Play Framework For Scala: Compilation error[type Application is not a member of package controllers]](http://stackoverflow.com/questions/30543960/play-framework-for-scala-compilation-errortype-application-is-not-a-member-of)

[Plya2-Scala/2.3 と 2.4 の違い](http://yakinikunotare.boo.jp/orebase2/play2_scala/diff_23_24)


作成した`app/controllers/Application.scala`の中身は
```scala
package controllers
 
import play.api.mvc._
 
object Application extends Controller {
 
  def index = Action {
    Ok("Hello World!")
  }
}
```

となってるが、少なくともPlay2.5.3では、Controllerはclassとして定義してあげないといけない
そこで、下記のようにobjectをclassと書き換えてあげればok

```scala
package controllers
 
import play.api.mvc._
 
class Application extends Controller {
 
  def index = Action {
    Ok("Hello World!")
  }
}
```


これでコンパイルが通るようになる
http://localhost:9000/
にアクセスして、Hello World!
が表示されたら成功

---
# sbtではなくactivatorを使用する
---

Play 2.5.xでは、ビルドシステムとしてsbtではなくactivatorを使ったほうが便利？っぽい様子
Hello World作成のために[参考にしたページ](http://catcher-in-the-tech.net/2300/)では
$ sbt run
とsbtを用いているが

自分はactivatorを用いている

とりあえずactivatorでも問題なく動いたので大丈夫っぽい

---
# 以下は捕捉
---

参考ページの内容を色々と試してみた

### 柔軟なrouting

ページ内で
`:id`を用いることでクライアントIDを受け取るようなルートを定義する
`*name`を用いることで、複数の/をまたいでパスを動的にする
というのがあったので
ちょっと改変してやってみた

```sh conf/routes
GET / controllers.Application.index
GET /hello controllers.Application.hello
GET /name/:id controllers.Application.name(id: String)
GET /your/*names controllers.Application.show(names)
```



```scala app/controllers/Application.scala
package controllers

import play.api.mvc._

class Application extends Controller {
  def index = Action {
    Ok("Hello World!")
  }

  def hello = Action {
    Ok("World!")
  }

  def name(id: String) = Action {
    Ok("Hello! Your ID is " + id)
  }

  def show(names: String) = Action {
    Ok("Hello! " + names)
  }
}
```


として
`http://localhost:9000/name/12345`にアクセスすると
Hello Your ID is 12345

`http://localhost:9000/your/hoge/huga`にアクセスすると
Hello! hoge/huga

と表示されたら成功

---
# templateを使ってみる
---

単純なテンプレートに関して
サイトでは
app/views/Application/index.scala.html

というパス構成で使用していたが、どうもディレクトリとしてApplicationとすると具合が悪いみたいなので

app/views/index.scala.html

に変更したら単純なテンプレートのやつでちゃんと表示された

---
#タイプセーフなテンプレートのところで躓いてしまう
---

下記画像のように

object Application is not a member of package controllers
Note: class Application exists, but it has no companion object.

と表示されてしまう

<img src="/2016/08/14/application-is-not-a-member-of-package-controllers-on-play2-5-x/error.jpg" width="300px" height="200px">

どうやらobjectがないからダメと言われるみたい

下記を参考にして、実行のためにobjectを定義してみる
http://bluesky.holy.jp/blog/bluesky/2014/03/20140318.html

先に言うと、下記のようにobjectを定義してもコンパイルエラーになる

```scala app/controllers/Application.scala
package controllers

import play.api.mvc._

object Application extends Application {

}

class Application extends Controller {

  case class Item(name: String, price: Integer)

  def index = Action {
    Ok(views.html.index("test typesafe template")(List(Item("hoge", 1), Item("fuga", 2))))
  }
}
```

実行してみると、ブラウザで
type mismatch;
 found   : Application.this.Item
 required: controllers.Application.Item

といわれてしまう

<img src="/2016/08/14/application-is-not-a-member-of-package-controllers-on-play2-5-x/error2.jpg" width="300px" height="200px">

controllers.Application.Itemにしましょうねみたいな意味だと思われるので、Itemのところを書き換えてあげる


```scala app/controllers/Application.scala
package controllers

import play.api.mvc._

object Application extends Application {

}

class Application extends Controller {

  case class Item(name: String, price: Integer)

  def index = Action {
    Ok(views.html.index("test typesafe template")(List(controllers.Application.Item("hoge", 1), controllers.Application.Item("fuga", 2))))
  }
}
```

これで無事に表示された！


ちなみに、objectとclassの名前が両方共Applicationなのでどっちが何に影響してるかわかりにくいので名前を変更してみた結果
app/controllers/Application.scala
app/views/index.scala.html
conf/routes

の中身をそれぞれ下記の用にしてあげたら、テンプレートがちゃんと表示された


```scala app/controllers/Application.scala
package controllers

import play.api.mvc._

object Application extends Hogefuga {

}

class Hogefuga extends Controller {

  case class Item(name: String, price: Integer)

  def index = Action {
    Ok(views.html.index("test typesafe template")(List(controllers.Application.Item("hoge", 1), controllers.Application.Item("fuga", 2))))
  }
}
```

```html app/views/index.scala.html
@import controllers.Application.Item
@(title: String)(items: List[Item])

@display(item: Item) = {
  @item.name (@item.price)
}
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
  <title>@title</title>
</head>
<body>
  <h1>Welcome @title</h1>
  @for(item <- items) {
    @display(item)
  }
</body>
</html>
```

```txt conf/routes
GET / controllers.Hogefuga.index
```


これを見る感じ、routingはclassで行って
importなどはそれを元にobjectで行うようにみえる

オブジェクト指向言語なのであたりまえなのかな、Play難しい






