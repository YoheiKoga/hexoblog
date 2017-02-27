title: python3とscrapyを使ってスクレイピング始め
date: 2016-09-07 10:56:33
tags:
- python
---

scrapyというpythonのスクレイピングフレームワークが徐々に人気が出てきているようなので
試してみる

公式サイト
https://scrapy.org/

チュートリアル
http://doc.scrapy.org/en/latest/intro/tutorial.html


自分の環境は
MacOSX 10.11 El Capitan
Python 3.5.1 :: Anaconda 2.5.0
Scrapy 1.1.2


<!-- more -->

python3やpipが入ってる前提で書くと

<br>

# インストールは簡単で、pip installするだけ
---

```
$ pip install scrapy
```

anacondaをつかっているからか特にエラーが出なかったけど
エラーが出る場合もあるらしいので、その時は下記リンクを参考にする

http://qiita.com/kent_ocean/items/bd79e29d9ab165c90b0d

<br>

# 簡単なコードを書いてみる
---

公式サイトのweb spidersを書いてみる
https://scrapy.org/

適当なディレクトリで下記コードを記述

```py myspider.py
# coding: utf-8

import scrapy

class BlogSpider(scrapy.Spider):
    name = 'blogspider'
    start_urls = ['https://blog.scrapinghub.com']

    def parse(self, response):
        for url in response.css('ul li a::attr("href")').re('.*/category/.*'):
            yield scrapy.Request(response.urljoin(url), self.parse_titles)

    def parse_titles(self, response):
        for post_title in response.css('div.entries > ul > li a::text').extract():
            yield {'title': post_title}

```

で、下記で実行できる

```
$ scrapy runspider myspider.py
```


どうやら、指定したwebサイトをクローリングしてtitleを取得してくれているみたい
`{'title': 'This Month in Open Source at Scrapinghub March\xa02016 on'}`
みたいなのが出力されてた

あと同じディレクトリに`__pychash__`なるファイルが生成された

spiderはスクレイピング(webサイトから意図した情報を取得する)とクローリング(webサイトをリンクを辿っていき情報を取得する)の両方を行ってくれるclassみたい
http://doc.scrapy.org/en/latest/topics/spiders.html

<br>

### jsonファイルとして保存するspiderの例

overviewのとこにもう一個spiderの例があって
StackOverflowのtop voted questions(http://stackoverflow.com/questions?sort=votes)を取得するコード例らしいのでやってみる

```py stackoverflow_spider.py
# coding:utf-8

import scrapy

class StackOverflowSpider(scrapy.Spider):
    name = 'stackoverflow'
    start_urls = ['http://stackoverflow.com/questions?sort=votes']

    def parse(self, response):
        for href in response.css('.question-summary h3 a::attr(href)'):
            full_url = response.urljoin(href.extract())
            yield scrapy.Request(full_url, callback=self.parse_question)

    def parse_question(self, response):
        yield {
            'title': response.css('h1 a::text').extract_first(),
            'votes': response.css('.question .vote-count-post::text').extract_first(),
            'body': response.css('.question .post-text').extract_first(),
            'tags': response.css('.question .post-tag::text').extract(),
            'link': response.url,
        }
```

で、下記コマンドで実行するとstackoverflowのtop voteの情報が取得できる

```
$ scrapy runspider stackoverflow_spider.py -o top-stackoverflow-questions.json
```

で、`top-stackoverflow-questions.json`という名前のjsonファイルに保存される
このjsonファイルは整形されていないので、pythonコマンドでjson.toolを使って整形してやれば見やすくなる

```
$ python -m json.tool top-stackoverflow-questions.json > result.json
```

これは簡単すぎてスクレイピングライフが捗りそう


本当はフレームワークなので、使い方はもうちょっと調べて行きたい
(このままだとただのスクレイピングコードなので)

