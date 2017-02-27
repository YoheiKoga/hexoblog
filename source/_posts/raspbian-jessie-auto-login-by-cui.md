title: Raspbian JessieでCUIから自動ログイン設定
date: 2016-08-14 16:51:17
tags:
- RaspberryPi
---


Raspbian Jessieでは、GUIから自動ログインの設定などできるが
CUIではファイルのどこをいじっていいか分からなかったので調べた

以下のリンクを参考にした
[Automatic root login in Debian 8.0 (console only)](http://superuser.com/questions/969923/automatic-root-login-in-debian-8-0-console-only)

## /lib/systemd/system/getty@.service をいじる

/lib/systemd/system/getty@.service ファイルをいじってやればよい

```sh /lib/systemd/system/getty@.service
ExecStart=-/sbin/agetty --noclear %I $TERM
```

という行があるので、例えばデフォルトのpiユーザーでログインする場合は以下のように書き換えてあげる

```sh /lib/systemd/system/getty@.service
ExecStart=-/sbin/getty/ --noclear -a pi %I $TERM
```

とする
尚、 -a pi としているのは --autologin pi の意味らしい


