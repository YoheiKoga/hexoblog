title: 30分間React入門「いいねボタン」作成チュートリアルの`npm run build`でエラーが起きる
date: 2016-06-22 15:08:07
tags:
- React
---


下記チュートリアル

[30分間React入門「いいねボタン」作成チュートリアル](http://c16e.com/1510161700/)

をやってみてる
が、最初の `npm run build` のところでエラーを吐いてしまう

# 結論から書くと、以下のページを参考にしたらうまくいった

[Babel 6リリースで、ES7を使っているとwebpackが動かなくなる](http://qiita.com/suin/items/00f6dc9e910eaeb6476d)

現在使っているbabelのバージョンが6に上がっているので、どうもwebpackの設定を変更しないといけないみたい
なので、解決策としては、参考リンク先通り `babel-core` と `babel-preset-es2015` と `babel-preset-stage-0` をいれて

```
$ npm install babel-loader babel-core babel-preset-es2015 babel-preset-stage-0 --save-dev
```

webpack.config.js も下記のように書き換えてやれば良い

```js webpack.config.js
module.exports = {
  entry: __dirname + "/src/main.js",
  output: {
    path: __dirname + "/dist",
    filename: "like-button.js"
  },
  module: {
    loaders: [
			{test: /\.js$/, loader: "babel", query: {presets: ["es2015", "stage-0", "react"]}}
    ]
  }
};
```

<!-- more -->

自分の環境は

node: v6.2.2
npm: 3.10.2
babel-core: 6.10.4
babel-loader: 6.2.4

なのでbabel6系だと思われる



以下はエラー内容とかの流れを記載

# npm run buildのところでエラーが出てしまう

下準備: webpackでビルドを試す
まで行った後

```
$ npm run build
```

コマンドを試してみるとまず以下のようなエラーを吐き出す

```
ERROR in Cannot find module 'babel-core'
```

babel-coreが無いよって言われるので入れて実行してみる

```
$ npm install babel-core --save-dev
$ npm run build
```

以下のようなエラーが出てくる

```
ERROR in ./src/main.js
Module build failed: ReferenceError: [BABEL] /react-like-button/src/main.js: Using removed Babel 5 option: base.stage - Check out the corresponding stage-x presets http://babeljs.io/docs/plugins/#presets
    at Logger.error (/react-like-button/node_modules/babel-core/lib/transformation/file/logger.js:41:11)
    at OptionManager.mergeOptions (/react-like-button/node_modules/babel-core/lib/transformation/file/options/option-manager.js:288:20)
    at OptionManager.init (/react-like-button/node_modules/babel-core/lib/transformation/file/options/option-manager.js:488:10)
    at File.initOptions (/react-like-button/node_modules/babel-core/lib/transformation/file/index.js:223:65)
    at new File (/react-like-button/node_modules/babel-core/lib/transformation/file/index.js:140:24)
    at Pipeline.transform (/react-like-button/node_modules/babel-core/lib/transformation/pipeline.js:46:16)
    at transpile (/react-like-button/node_modules/babel-loader/index.js:14:22)
    at Object.module.exports (/react-like-button/node_modules/babel-loader/index.js:88:12)
```

なんかよくわからないがbabelがエラーを吐き出しているっぽい

[Babel 6リリースで、ES7を使っているとwebpackが動かなくなる](http://qiita.com/suin/items/00f6dc9e910eaeb6476d)
のエラーと同じだったので、これを使って解決する

`babel-core` と `babel-preset-es2015` と `babel-preset-stage-0` を入れて、webpack.config.jsを修正してやればよいので

```
$ npm install babel-core babel-preset-es2015 babel-preset-stage-0 --save-dev
```

でインストールした後に、webpack.config.jsの中身を以下のようにする

```js webpack.config.js
module.exports = {
  entry: __dirname + "/src/main.js",
  output: {
    path: __dirname + "/dist",
    filename: "like-button.js"
  },
  module: {
    loaders: [
			{test: /\.js$/, loader: "babel", query: {presets: ["es2015", "stage-0"]}}
    ]
  }
};
```


これであとは、実行してやればよい
```
npm run build

> test@1.0.0 build /react-like-button
> rm -rf dist/*.js && webpack

Hash: 7d9e547ff9006d37b4cd
Version: webpack 1.13.1
Time: 1228ms
         Asset     Size  Chunks             Chunk Names
like-button.js  1.42 kB       0  [emitted]  main
    + 1 hidden modules
```

できた！


・・・と思いきや、その後進んだ時に `npm run watch` したらエラーが出てきた

```
Module build failed: SyntaxError: 
```


どうやら、webpackの設定が不足しているみたい
当たり前で、reactを使ってやろうとしているのだからloaderにreactの設定を置いておかないといけない
よって、webpack.config.jsの設定は以下のようになる


```js webpack.config.js
module.exports = {
  entry: __dirname + "/src/main.js",
  output: {
    path: __dirname + "/dist",
    filename: "like-button.js"
  },
  module: {
    loaders: [
			{test: /\.js$/, loader: "babel", query: {presets: ["es2015", "stage-0"]}}
    ]
  }
};
```


これでコンパイルが通った!


