title: How to put pictures and change sizes on your Hexo Blog
date: 2016-04-05 14:53:59
tags:
- Hexo
---


# _config.yml configuration

change `post_asset_folder: true` in your _config.yml

_config.yml

```
post_asset_folder: true
```

and check your blogs permalink
maybe the default is like this

```
permalink: :year/:month/:day/:title/
```

for example, this blog title is `"How to put pictures and change sizes on your Hexo Blog"`

in this case, write img tag like under sheme is good for change image size

```
<img src="/2016/04/05/How-to-put-pictures-and-change-sizes-on-your-Hexo-Blog/example.jpg" width="300px" height="200px">
```

like this
<img src="/2016/04/05/How-to-put-pictures-and-change-sizes-on-your-Hexo-Blog/example.jpg" width="300px" height="200px">







