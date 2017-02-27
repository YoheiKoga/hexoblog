title: Rubyのputsメソッドで学ぶdocumentの読み方(初歩の初歩)
date: 2016-08-03 09:52:42
tags:
- Ruby
- プログラミング
---

プログラムを学んでいく上でdocumentとか読めないといけないけど
いかんせんどうやって読むのかがまずわからない

なんか作法に則って記述されているんだろうけど、プログラム独特の書き方なので
単純に日本語を読むようにはいかないっぽいので
動きが分かってるメソッドを読み解いてみる

<!-- more -->

<br>
<br>

## putsで学ぶdocument(reference)の読み方 on Ruby

このへんを参考に
http://d.hatena.ne.jp/mailishmaid/20061026/1161851986

まず、putsのreferenceを引いてみる
http://docs.ruby-lang.org/ja/search/version:2.3.0/query:puts/

まず先頭5件みたいなのがでてくる

1.  Kernel.#puts(*arg) -> nil
2.  IO#puts(*obj) -> nil
3.  Zlib::GzipWriter#puts(*str) -> nil
4.  StringIO#puts(*obj) -> nil
5.  OpenSSL::Buffering#puts(*objs) -> nil

順に見ていってみる

<br>
<br>

1. Kernel.#puts(*arg) -> nil

---
(引用)

- 引数と改行を順番に、標準出力$stdoutに出力します
引数がなければ改行のみを出力します

---

```rb
puts "hello"
```

とすると、標準出力(例えばTerminalのコンソールなど)に`hello`と出力されて、続けて改行されているので
改行コードも一緒に出力されている(これは文字としては出力されないのでぱっと見はわからない)

<br>
<br>

## モジュール関数ってリンクがあるけどモジュール関数とは？

そもそもモジュールとは

---
(引用)

- Rubyにはモジュールというクラスによく似た機能がある。クラス定義のように任意の式やメソッド定義を記述できるが
 以下の点でクラスとは異なる
   - インスタンスを生成することは出来ない
   - 継承することは出来ない
 モジュールの用途としては主に以下のようなものが挙げられる
   - 名前空間を作る
   - モジュールのメソッドを、あるクラスのインスタンスメソッドとして取り込む
   - モジュールのメソッドを、あるオブジェクトの特異メソッド(クラスメソッド)として取り込む
   - モジュール関数を定義して使う

---

とある
```
module モジュール名
  hogehoge
end
```

みたいに定義されているやつ
で、
Kernel.#puts(*arg) -> nil
って書いてあるように
module Kernel
で定義されているので、モジュール関数なんだなってことかな

これは[リンク先](http://docs.ruby-lang.org/ja/2.1.0/class/Kernel.html)を見てみると

---
(引用)

- 全てのクラスから参照できるメソッドを定義しているモジュール。 Object クラスはこのモジュールをインクルードしています。
Object クラスのメソッドは実際にはこのモジュールで定義されていま す。これはトップレベルでのメソッドの再定義に対応するためです。

---

と書かれているのでひとまず全てのクラスから呼び出すことが可能なんですよって意味に捉えればいいのかな

じゃあ次のIOってなに？

<br>

## IO#puts(*obj) -> nil

インスタンスメソッドと書かれている

インスタンスメソッドはクラスメソッドと並列で語られることが多い
こちらがわかりやすかったかな
[Rails初心者から中級者になったと感じたきっかけ](http://nigohiroki.hatenablog.com/entry/2014/03/12/010344)

---
(引用)

- クラスメソッドはクラスからそのまま呼び出し、インスタンスメソッドはクラスのインスタンスから呼び出します。

---

とのこと
リンク先では Dogクラスを用いて説明されていたが
ひとまず

<br>

### クラスメソッドの場合
・Dogのうち一番年齢の高いDogを取り出すメソッド
・Dogのうちオスのみを取り出すメソッド

は以下のようになる

```ruby
class Dog < ActiveRecord::Base
    # 一番年齢の高いDog
    def self.get_oldest
        self.order("age DESC").limit(1)
    end

    # オスのみを取り出す
    def self.get_male
        self.where(:sex => "male")
    end
end
```

クラス全体の中から何かを取り出す場合などに利用される

<br>

### インスタンスメソッドの場合

・とあるDogの年齢、性別をHashで取り出すメソッド
・とあるDogの親のDogを取り出すメソッド(親が子のIDを持っているケース)

```ruby
class Dog < ActiveRecord::Base
    # 年齢、性別
    def get_profile
        { :age => self.age, :sex => self.sex } 
    end

    # 親を取り出す
    def get_parent
        Dog.where(:child => self.id)
    end
end
```

「あるインスタンスの何かを取り出す」場合などに利用されます。

とある

よし、わからん！

こっちをよんでみよう
http://www.bokukoko.info/entry/2015/09/12/Ruby_%E3%81%AE_%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E3%81%AE%E8%A7%A3%E9%87%88_(%E4%B8%AD%E7%B4%9A%E8%80%85%E5%90%91%E3%81%91)

<br>

```ruby 
class A
  def instance_method
    puts "from a."
  end
end

module B
  def instance_method
    puts "from b."
  end
end

class C < A
  include B

  def instance_method
    puts "from c."
  end
end

c = C.new
c.instance_method # どうなる?
```

おそらく from c. ってコンソールに出てくるんだろうこれはなんとなくわかるやってみよう


うん、 from c.って出力された

---
(引用)

- もしC内になかった場合は次にモジュールBの中を探しに行く。そこにもなかったら親クラスのAを探しに行く。

---

とのこと


あ、この記事わかりやすいかも


```ruby
class A
  def a
    puts "a called."
  end
  
  def self.b
    puts "b called."
  end
end

obj = A.new

def obj.c
  puts "c called."
end

obj.a 
A.b
obj.c
```

これを実行すると

```

a called.
b called.
c called.

```


となる

RubyはCのように、作ったオブジェクトからしか呼べないメソッドを定義することができる、これが特異メソッドと呼ばれる



ちなみに

```ruby
def obj.c
  puts "c called."
end

def objc
  puts "c called."
end


obj.c   #=> classmethod.rb:45:in `<main>': undefined local variable or method `obj' for main:Object (NameError)
objc    #=> c called.
```

のように、"."で区切ってしまうとメソッドと認識されてしまうので、objが定義されていない場合はNameErrorが吐き出される

<br>
<br>

ふむ


で

---

(引用)

- Ruby初心者の方は、クラスメソッドは A.class_method というような形でクラスから直接メソッドを呼べるもの、という認識だと思う。
ただこれもRuby的には オブジェクトからメソッドを呼び出している 、というに過ぎない。
ここがRubyにおいて重要な概念なんだけど、 クラスもオブジェクトである ということが言えるのだ。
これをぱっとわかる人はまずいないと思う。ということでサンプル。

---

```ruby
class A
end

puts A.class
p A.methods

a = A.new
puts A.new.class
p a.methods
```

これで a と A が確認できる

<br>

さっきの例に戻ると

---
(引用)

- def obj.c はobjに対しての特異クラスであった。ということは、 この def self.b というのは、 Aクラスに対しての特異メソッドになるのだ！
だからこそ、A.bと呼ぶことができるのである。

---

つまり・・・？

def self.b はAクラスの特異メソッドになり、クラスから直接呼び出すことができるのでクラスメソッドとも呼べるみたいな感じで

A を new して作成した obj に対して定義したメソッド def obj.c は objに対する特異メソッド、なのかな？


で、ここまで来ると、下の２つの表記は同じことを示すのだということがわかる(はず)

classがobjを継承して、その中でインスタンスメソッドの c を定義する場合

```ruby

class A
end

obj = A.new

class << obj
  def c
  puts "c called."
  end
end
```


オブジェクトから直接インスタンスメソッド c を定義する場合

```ruby

class A
end

obj = A.new

def obj.c
  puts "c called."
end
```

<br>
<br>


ということは・・・ class A.b みたいなやつっていきなり定義できるのかな？
よく考えたらdefの書き方がわからんから無理か・・・

だからクラスメソッドって self を使って定義するんだなー

じゃあクラスメソッドの有用性ってなんなんだろ？インスタンスメソッドじゃだめなのかな?



で


これでいうと

---
(引用)

- Object#extend も理解できる。 a.extend(b) は aの特異クラスとしてbを追加するメソッド だ。

---

へー、extendってメソッドがあるんだ
で、bを特異クラスとして追加してくれるんだ・・・

自分で書いてもよさそうな気もするけどまぁ

例)インスタンスオブジェクトの場合

```ruby
module D
  def d
    puts "from d."
  end
end

class A
end

a = A.new
a.extend(D)
a.d # => "from d."
```


例)クラスオブジェクトの場合

```ruby
module B
  def b
    puts "from b."
  end
end
class A
  A.extend B # extend B と書いてもok.
end

A.b #=> "from b."
```



結局、インスタンスメソッドもクラスメソッドも結局はオブジェクトから呼び出すメソッドなので
実際は小難しく考える必要はないかなまぁ

話は飛んだけど、つまり、Rubyのリファレンス読むの難しい・・・

がこうやって読めるようにならないといけないのでがんばる










