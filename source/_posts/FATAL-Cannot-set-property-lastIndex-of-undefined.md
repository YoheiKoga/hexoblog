title: HexoでFATAL-Cannot-set-property-lastIndex-of-undefinedと表示された
date: 2016-08-14 16:21:43
tags:
- Hexo
---

Hexoを使ってて、突然エラーが発生してHexoコマンドが使えなくなった

エラーを見てみると

```
FATAL Cannot set property 'lastIndex' of undefined
```

みたいなのが表示されていた

下記リンク先を参考にしてみると
https://github.com/hexojs/hexo/issues/1786
https://github.com/hexojs/hexo/issues/1627

どうやら、_config.yaml内のauto_detectをfalseにしてやればいいみたい

highlightのところでインデントされていると思うので、一部抜粋すると下記のようになるはず

```yaml _config.yaml
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:
```


これで解決するが、詳しくはなんなのかわかってないのでもう少し調べないと・・・



