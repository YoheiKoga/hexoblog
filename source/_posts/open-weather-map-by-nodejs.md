title: node.jsでOpenWeatherMap APIから天気情報を取得
date: 2016-08-14 16:49:17
tags:
- node
---


[GPSモジュールを使って現在地の天気情報をAPIで取得する](http://yoheikoga.github.io/2016/08/14/weather-in-the-area-now-on-by-gps-module/)でOpenWeatherMap APIを使って天気情報を取得してみたが

node.jsを使うとどんな感じになるのかやってみた

nodeのバージョンは、v0.12.7とv5.3.0で試してみたところどちらでもうまくいった

# HTTPモジュールでGETをしてあげればよい

下記あたりを参考にすると、http GETしてあげて、得られたJSONをパースしてあげればいいみたい
[Node.jsでHTTP GETしてJSONパースするメモ](http://qiita.com/n0bisuke/items/788dc4379fd57e8453a3)
[Node.js: GET リクエストを行う](http://blog.sarabande.jp/post/52044588736)

requestモジュールでもいいみたいだけどひとまず今回はhttpモジュールを使ってみる
[Node.js: request モジュールを使って GET リクエストを行う](http://blog.sarabande.jp/post/52095868617)

コードはシンプルで、下記のようになる
locationは自分の好きな場所を
APIKEYは自分の環境のを入れる

ファイル名は openWeatherMap.js とした

```js openWeatherMap.js
var http = require('http');

var location = "Fukuoka-shi,jp";
var units = 'metric';
var APIKEY = "{your api key}";

var URL = 'http://api.openweathermap.org/data/2.5/weather?q='+ location +'&units='+ units +'&appid='+ APIKEY;

http.get(URL, function(res) {
  var body = '';
  res.setEncoding('utf8');

  res.on('data', function(chunk) {
    body += chunk;
  });

  res.on('data', function(chunk) {
    res = JSON.parse(body);
    console.log(res);
  });
}).on('error', function(e) {
  console.log(e.message);
});
```


ターミナルなどのコンソール上で、実行してあげると

```sh
$ node openWeatherMap.js 

{ coord: { lon: 130.42, lat: 33.61 },
  weather: 
   [ { id: 803,
       main: 'Clouds',
       description: 'broken clouds',
       icon: '04d' } ],
  base: 'stations',
  main: 
   { temp: 23.58,
     pressure: 1009,
     humidity: 69,
     temp_min: 21.67,
     temp_max: 24.44 },
  visibility: 10000,
  wind: { speed: 2.6, deg: 330 },
  clouds: { all: 75 },
  dt: 1465380026,
  sys: 
   { type: 1,
     id: 7546,
     message: 0.0136,
     country: 'JP',
     sunrise: 1465330063,
     sunset: 1465381645 },
  id: 1863967,
  name: 'Fukuoka-shi',
  cod: 200 }
```

のようにJSONデータが得られる

特定の部分が欲しければ、openWeatherMap.js の19行目のresの部分をいじってあげれば良い

例えばmainの部分が欲しかったら,19行目の部分を
```js
console.log(res.main);
```

としてやれば良い

結果のJSONは以下のようになる

```
{ temp: 23.58,
  pressure: 1009,
  humidity: 69,
  temp_min: 21.67,
  temp_max: 24.44 }
```


