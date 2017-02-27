title: HaskellのStackでdoctestを行うとQuickCheck使用時にエラーが出る
date: 2016-08-26 11:38:48
tags:
- Haskell
---

結論から言うと、実行時にexecをつけて実行してやればQuickCheck使用時のエラーが出なくなった

```
$ stack exec doctest DoctestSample.hs 
```

Haskellを色々とやってみている
[CentOSでHaskellプログラミング始め](http://yoheikoga.github.io/2016/08/14/haskell-on-centos/)


で、2015年からHaskellを始めるにはstackというツールを使ってやるのがよいということでやってみているが

書籍ではまだcabalコマンドなどが出てきて、stackではエラーとなることもあるみたい

<!-- more -->

<br>

## doctestとQuickCheck
---

[関数プログラミング実践入門](https://www.amazon.co.jp/%E9%96%A2%E6%95%B0%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AE%9F%E8%B7%B5%E5%85%A5%E9%96%80-%E2%94%80%E2%94%80%E7%B0%A1%E6%BD%94%E3%81%A7%E3%80%81%E6%AD%A3%E3%81%97%E3%81%84%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%9F%E3%82%81%E3%81%AB-WEB-PRESS-plus/dp/4774169269)
のdoctestのところをやってみている


doctest自体はひとまず下記stackコマンドで入れてみた
(もしかしたらプロジェクトの依存性などあるからcabalファイルに記述したほうがいいかもしれないがまだよくわからない・・・)

```
$ stack install doctest
```


これで、doctestコマンドが使えるので書籍通りにdoctestを使ってみればいい

<br>

## QuickCheckでエラーになる
---

そのままQuickCheckも試してみようとすると、エラーになってしまう

下記ファイルをdoctestするとして

```hs DoctestSample.hs
module DoctestSample where

-- | 文字列中のスペースの個数
--
-- >>> countSpace ""
-- 0
-- >>> countSpace "abracadabra"
-- 0
-- >>> countSpace "Hello, World!"
-- 1
-- >>> countSpace "    "
-- 4
--
-- prop> countSpace s == sum [1 | c <- s, c == ' ']
--

countSpace :: String -> Int
countSpace = length . filter (' ' ==)
```


<br>

普通にdoctestコマンドを実行すると以下のようにエラーになった


```
$ doctest DoctestSample.hs 
### Failure in DoctestSample.hs:14: expression `countSpace s == sum [1 | c <- s, c == ' ']'

<interactive>:47:3:
    Not in scope: ‘polyQuickCheck’
    In the splice: $(polyQuickCheck (mkName "doctest_prop"))

<interactive>:47:3:
    GHC stage restriction:
      ‘polyQuickCheck’ is used in a top-level splice or annotation,
      and must be imported, not defined locally
    In the expression: polyQuickCheck (mkName "doctest_prop")
    In the splice: $(polyQuickCheck (mkName "doctest_prop"))
Examples: 5  Tried: 5  Errors: 0  Failures: 1
```


なんだと思って調べてみると以下がヒットした
[QuickCheck does not work](https://github.com/sol/doctest/issues/122)

`doctest`コマンド単体で行うとエラーになるので`stack exec doctest`コマンドでやると良いよ、とのことなので実行してみる

<br>

```
$ stack exec doctest DoctestSample.hs 
Examples: 5  Tried: 5  Errors: 0  Failures: 0
```


成功した!
良かった