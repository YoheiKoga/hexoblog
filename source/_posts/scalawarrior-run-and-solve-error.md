title: scalawarrior-run-and-solve-error
date: 2016-11-07 11:36:51
tags:
- Scala
---

ゲーム感覚でscalaを学習できるscalawarrior
[scalawarrior/scalawarrior](https://github.com/scalawarrior/scalawarrior)
scalawarriorを実行しようとしたら自分の環境ではエラーが出たので

メモ

<!-- more -->

# git cloneしてrun

下記コマンドで好きなディレクトリにscala warriergit cloneして、ディレクトリ移動してrunした

```
$ git clone git@github.com:scalawarrior/scalawarrior.git
$ cd scalawarrior
$ ./activator run
```

ここで下記エラーが出た

```
/Users/username/.sbt/0.13/plugins/plugins.sbt:2: error: eof expected but ';' found.
addSbtPlugin("org.ensime" % "ensime-sbt-cmd" % "0.1.2")
```

pluginにensimeをいれてるがそれのeofが見当たらないとか言われた

検索すると下記stackoverflowがヒット

http://stackoverflow.com/questions/18432683/error-eof-expected-how-to-use-idea-and-eclipse-plugins-together-in-sbt


blank lineを入れろというので、.sbt/0.13/plugins/plugins.sbtを書き換えた

最初、自分の環境だと
```
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.0.0-M12")
addSbtPlugin("org.ensime" % "ensime-sbt-cmd" % "0.1.2")
addSbtPlugin("org.ensime" % "sbt-ensime" % "0.6.0")
```

のようにしていたのを

```
addSbtPlugin("io.get-coursier" % "sbt-coursier" % "1.0.0-M12")

addSbtPlugin("org.ensime" % "ensime-sbt-cmd" % "0.1.2")

addSbtPlugin("org.ensime" % "sbt-ensime" % "0.6.0")
```

のように、addSbtPliginごとに空白行を入れてあげた

<br>

これで最初のエラーは解決したが次にまた`./activator run`すると下記エラーが出た

```
java.lang.NoSuchMethodError: sbt.Keys$.excludeDependencies()Lsbt/SettingKey;
```

これも検索すると下記issuesがヒットした

https://github.com/alexarchambault/coursier/issues/245


<br>

どうやらsbt.versionが異なってるから起きるエラーみたい・・・？

このへんまだよくわからないがひとまず解決策として

scalawarrior/project/build.properties内のsbt.version=0.13.9というふうに修正した

```
-- sbt.version=0.13.5

++ sbt.version=0.13.9
```

これで`./activator run`したらちゃんとscalawarriorが起動したよかった







