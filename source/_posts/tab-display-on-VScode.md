title: Visual Studio Codeでタブディスプレイ表示
date: 2016-06-22 15:50:35
tags:
- プログラミング
---

Visual Studio Code (VScode)を使ってて結構便利だけど

Sublime Textみたいにタブ表示(タブディスプレイ)があると便利だと思ったのだが
現時点のversion 1.2 ではその機能は無いっぽい


と思って探してたら、現在開発中のバージョンに搭載されている模様
stack overflowで見つけた

[Is there a tabbed view for VSCode?](http://stackoverflow.com/questions/30943062/is-there-a-tabbed-view-for-vscode)


[Insiders release(開発中？)](http://code.visualstudio.com/insiders)のバージョンでは搭載されているみたいで
一応ダウンロードも出来るし設定もできるけど
2016年7月ごろにその機能を搭載したproduct版(おそらくバージョン1.3)で搭載されるとのことなので

1.3版もしくは現段階でinsider版をダウンロードした場合は下記方法で使用可能


まずアプリを立ち上げる
(ひとまずInsider版で試してみる)

Macだと、`command + ,` もしくはメニューのCode-Insiderから基本設定->ユーザー設定でユーザー設定画面を開いて

settings.jsonのファイルに

"workbench.editor.showTabs": true

を記載する
これだけで、タブ機能がつく

参考リンクのstack overflow内だと `"workbench.showEditorTabs": true` にしろと書いているが
規定の設定を見てみたらshowEditorTabsじゃなくてshowTabsになっていたので
products版になった場合は一応確認する必要あり


tab表示機能があるとmarkdownでメモ代わりに使えるのでとても便利でありがたい
