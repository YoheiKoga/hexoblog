title: VirtualBoxにLinux Liteを入れる
date: 2017-02-28 08:41:49
tags:
- Linux
---

Macにローカル開発環境を整えるときはVirtualBox + Vagrantが便利で

[dotinstall](http://dotinstall.com/lessons/basic_localdev_mac_v2)にはWindowsで設定する場合もMacで設定する場合もあって

便利でわかりやすい

<br>

ただ、今回はLinuxのGUIを使用したかったので軽量のやつを直接VirtualBoxに入れてみることにした

軽量のLinuxを色々探しているところ、LubuntuやLinux Liteが良さそうだったので

Linux Liteを入れてみる

今回は諸事情により32bitを入れるが、64bitでもubuntuやLubuntuでも基本的な入れ方は同じみたい

<!-- more -->

<br>

### 環境

Mac OS Sierra version 10.12.3 
VirtualBox version 5.1.14


<br>

## VirtualBoxをダウンロード

下記公式サイトからダウンロードする

https://www.virtualbox.org/

ダウンロードしたらpackageを開くとインストーラーが立ち上がるので

指示通りに進むとインストールできます、詳しくはdotinstallを参照するとわかりやすい

<br>

## Linux Liteのisoをダウンロード

[Linux Lite公式ダウンロードページ](https://www.linuxliteos.com/download.php)から

好きなisoイメージ(Linux OS本体みたいな感じ)やつを選ぶ

2017年2月現在、最新版は3.2で

自分はそれの32bit版を選んだ(特に理由がなかったらいまだと64bit版を選んだほうが良さそう)


<img src="/2017/02/28/linux-in-virtualbox/linuxlitedownload.jpg" width="300px" height="200px">

上記画像の32bitか64bitかどちらかのDOWNLOADを選んで好きなディレクトリ(フォルダ)に保存する

<br>


## VirtualBoxにisoイメージを読み込ませる

VirtualBoxを起動させると

"Oracle VM VirtualBox マネージャー"

というのが立ち上がるので、「新規」をクリックする

名前とオペレーティングシステムを選ぶのでそれぞれ

- 名前: 適当につける(自分はMyLinuxLiteにした)
- タイプ: Linux
- バージョン: Ubuntu(自分のダウンロードしたisoイメージのbit数のほうを選択する、自分は32bitを選択した)

<img src="/2017/02/28/linux-in-virtualbox/linuxlite2.jpg" width="300px" height="200px">


<br>


次にメモリーサイズを選ぶ、Linux Liteは最低512MB必要らしいのでそれ以上ならかまわない

ひとまず1024MBあてておく


<img src="/2017/02/28/linux-in-virtualbox/linuxlite3.jpg" width="300px" height="200px">


<br>

次に仮想のハードディスク容量を作成する

とりあえず最初は新しく作っておけば良いので「仮想ハードディスクを作成する」を選択する

ハードディスクのタイプはVDIを選択する

<img src="/2017/02/28/linux-in-virtualbox/linuxlite4.jpg" width="300px" height="200px">

<img src="/2017/02/28/linux-in-virtualbox/linuxlite5.jpg" width="300px" height="200px">


物理ハードディスクにあるストレージは、まぁ可変サイズを選択しておく

サイズはあとでも変更可能だが、一手間かかるので十分な容量を確保しておく
(自分のマシンのハードディスク容量を使用する？のであまり割り当てすぎると困ると思われる)

自分は諸事情でかなり大きめに使うと考えられたので40GB割り当てた

普通だとだいたい20GBもあれば十分余裕だと思われる

<img src="/2017/02/28/linux-in-virtualbox/linuxlite6.jpg" width="300px" height="200px">

<img src="/2017/02/28/linux-in-virtualbox/linuxlite7.jpg" width="300px" height="200px">


<br>


## 起動する

ここまでしたら、MyLinuxLiteが作成できているので

MyLinuxLiteを選択した状態で「起動」をクリックする

すると最初に、「isoイメージを選択してください」みたいな画面が立ち上がるので、先程ダウンロードした

Linux Liteのisoイメージを選択して「start」

<img src="/2017/02/28/linux-in-virtualbox/linuxlite8.jpg" width="300px" height="200px">


<br>

少し待つと、"Welcome to Linux Lite"みたいな画面が立ち上がる

まずはこの画面のアップデートなどはいらないので右上の×ボタンで閉じる

<img src="/2017/02/28/linux-in-virtualbox/linuxlite9.jpg" width="300px" height="200px">


<br>

デスクトップの左上に「Install Linux Lite」というアイコンがあるのでダブルクリックで開く

<img src="/2017/02/28/linux-in-virtualbox/linuxlite10.jpg" width="300px" height="200px">

インストーラーが立ち上がるので、まずはEnglishを選択した状態でContinue


<img src="/2017/02/28/linux-in-virtualbox/linuxlite11.jpg" width="300px" height="200px">


<br>

インストール中にアップデートするかどうかと、その他のソフト入れる？

って聞かれるのでチェックを入れてContinue

<img src="/2017/02/28/linux-in-virtualbox/linuxlite12.jpg" width="300px" height="200px">


<br>

インストールのタイプを選択する

一番上の「Erase disk and install Linux」を選択してInstall Nowをクリック

<img src="/2017/02/28/linux-in-virtualbox/linuxlite13.jpg" width="300px" height="200px">

Write the change to disk?

みたいなのが出てくるのでContinueをクリック

<br>

Where are you?と聞かれるので自分の地域を選ぶ

日本だったらTokyoを選択してContinue

<img src="/2017/02/28/linux-in-virtualbox/linuxlite14.jpg" width="300px" height="200px">


<br>

キーボードを選択する

自分はUSタイプなので「English(US)」を選択

日本語(JIS)キーボードだったら「日本語」を選択すればいけるはず

すぐ下にType here to test your keyboardとあるので、言語を選択して

正しくタイプできているか試してみると良い

<img src="/2017/02/28/linux-in-virtualbox/linuxlite15.jpg" width="300px" height="200px">


<br>

名前を決めてパスワードを入力する

名前は適当でよい(そのままでもよい)

また、自分はログインする時のパスワードが面倒だったので"Log in automatically"にチェックを入れたが

基本的には"Require my password to log in"にしておいたほうがセキュリティ的には良い

<img src="/2017/02/28/linux-in-virtualbox/linuxlite16.jpg" width="300px" height="200px">


<br>


以上でインストールが始まるのでひとまず待つ

インストールが終わったら再起動しますか？みたいなのが出るので「Restart Now」をクリック

インストールメディアを取り出してくださいみたいな画面が出るのでエンターキーを押す

<img src="/2017/02/28/linux-in-virtualbox/linuxlite17.jpg" width="300px" height="200px">

<br>

と、再起動が始まってLinux Liteが立ち上がる!

これでLinux Liteが使えるようになったのであとはお好みで使っていく



