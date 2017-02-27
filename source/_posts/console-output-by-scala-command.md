title: scalaコマンド実行でコンソールに出力されたりされなかったり
date: 2016-09-01 14:26:01
tags:
- Scala
---

## scalaコマンドでコンソール出力するには明示的に指定してやる必要がある

[ゼロから学ぶScala](https://www.amazon.co.jp/%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E5%AD%A6%E3%81%B6Scala-%E9%A3%AF%E7%94%B0-%E5%81%A5%E4%BA%8C-ebook/dp/B01HI7BCT0)
を読んでる

~~コンパイルとかめんどくさい~~ scalaコマンドで直接実行したいと考えているので
いつもscalaコマンドでscalaファイルを直接実行しているが
printlnなどでコンソール出力する時に、コンソールに出力される時とされない時がある(コンパイルは通ってる)

scalacコマンドでコンパイルしたclassファイルを実行すればもちろん出力されるのだがなんとなくやりたくないので
なんで出たり出なかったりするかググッて見ると下記がヒットした

[Scala ソースコードをコマンドから直接実行する](http://symfoware.blog68.fc2.com/blog-entry-181.html)


結論から言うと、objectを明示的に実行してやらなければならないらしい

<!-- more -->

例えば以下のscalaファイルはscalaコマンドで実行しても出力されないが

```scala Sample.scala
object Sample{
    def main(args:Array[String]) {
        val p1 = new Person
        val p2 = new Person
        p1.name = "佐藤"
        p1.age = 24
        p2.name = "田中"
        p2.age = 38
        p1.introduce()
        p2.introduce()
    }
}

class Person {
    var name = ""
    var age  = 0
    def introduce() {
        println("私は" + name + ", 年齢は" + age + "歳です")
    }
}
```

以下のように、Sample objectのmainを明示的に指定してやるとコンソール出力される

```scala Sample.scala
object Sample{
    def main(args:Array[String]) {
        val p1 = new Person
        val p2 = new Person
        p1.name = "佐藤"
        p1.age = 24
        p2.name = "田中"
        p2.age = 38
        p1.introduce()
        p2.introduce()
    }
}

class Person {
    var name = ""
    var age  = 0
    def introduce() {
        println("私は" + name + ", 年齢は" + age + "歳です")
    }
}

// 明示的にSample.mainを指定してやる。引数はnullまたはargs
Sample.main(null)
Sample.main(args) //=> argsでも大丈夫

```


<br>

## 何でそうなるのか
---

以下のコードは、明示的にobjectを指定してやらなくれも `scala Sample.scala` でコンソールに結果が表示される

```scala Sample.scala
object Sample{
  def main(args: Array[String]): Unit = {
 

    val a = 5.0
    val b = 9.0
    val c = 5.0
    val d = 8.0
    val e = 10.0
    val f = 5.0

    println("Circle Area is: " + getArea(a))
    println("Triangle Area is: " + getArea(b, c))
    println("Trapezoid Area is: " + getArea(d, e, f))
  }

  def getArea(x: Double): Double = {
    x * x * 3.14
  }

  def getArea(x: Double, y: Double): Double = {
    x * y / 2
  }

  def getArea(x: Double, y:Double, z:Double): Double = {
    (x + y) * z / 2
  }
}
```

実行結果は以下になる

```
$ scala Sample.scala

Circle Area is: 78.5
Triangle Area is: 22.5
Trapezoid Area is: 45.0
```


しかし、他のobjectやclassを追加すると、途端に実行されなくなってしまう
例えば以下のコードである

```scala Sample.scala


object Sample{
  def main(args: Array[String]): Unit = {
 

    val a = 5.0
    val b = 9.0
    val c = 5.0
    val d = 8.0
    val e = 10.0
    val f = 5.0

    println("Circle Area is: " + getArea(a))
    println("Triangle Area is: " + getArea(b, c))
    println("Trapezoid Area is: " + getArea(d, e, f))
  }

  def getArea(x: Double): Double = {
    x * x * 3.14
  }

  def getArea(x: Double, y: Double): Double = {
    x * y / 2
  }

  def getArea(x: Double, y:Double, z:Double): Double = {
    (x + y) * z / 2
  }
}

object Sample02 {
    def main(args:Array[String]) {
        val p1 = new Person
        val p2 = new Person
        p1.name = "佐藤"
        p1.age = 24
        p2.name = "田中"
        p2.age = 38
        println(p1.introduce())
        println(p2.introduce())
    }
}

class Person {
    var name = ""
    var age  = 0
    def introduce(): String = {
        "私は" + name + ", 年齢は" + age + "歳です"
    }
}
```

実行してみると、コンパイルは通るがコンソール出力はされない

これを、出力されるようにするにはobjectのSampleとSample02を明示的に指定してやらないといけない





```scala Sample.scala
```scala Sample.scala


object Sample{
  def main(args: Array[String]): Unit = {
 

    val a = 5.0
    val b = 9.0
    val c = 5.0
    val d = 8.0
    val e = 10.0
    val f = 5.0

    println("Circle Area is: " + getArea(a))
    println("Triangle Area is: " + getArea(b, c))
    println("Trapezoid Area is: " + getArea(d, e, f))
  }

  def getArea(x: Double): Double = {
    x * x * 3.14
  }

  def getArea(x: Double, y: Double): Double = {
    x * y / 2
  }

  def getArea(x: Double, y:Double, z:Double): Double = {
    (x + y) * z / 2
  }
}

object Sample02 {
    def main(args:Array[String]) {
        val p1 = new Person
        val p2 = new Person
        p1.name = "佐藤"
        p1.age = 24
        p2.name = "田中"
        p2.age = 38
        println(p1.introduce())
        println(p2.introduce())
    }
}

class Person {
    var name = ""
    var age  = 0
    def introduce(): String = {
        "私は" + name + ", 年齢は" + age + "歳です"
    }
}

Sample.main(null)
Sample02.main(null)
```


どうやらobjectが複数あったりclassが定義されている場合は暗黙的なmainの実行が行われないみたい
なので、自分でどのオブジェクトのメソッド(基本的にmain)を実行するか与えてあげなければならないっぽい



