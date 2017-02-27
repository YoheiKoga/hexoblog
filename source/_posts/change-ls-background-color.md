title: MacのTerminalでls -Gした時の背景色を変更する
date: 2016-07-19 12:44:36
tags:
- shell
- terminal
---

Macのターミナルで `$ ls -G` した時の色が見にくい場合がある(El Capitanのせいなのかな、Marvericsでは普通だった気がするが)

特に、シリアル通信などで接続デバイスを確かめたい時などに
`$ ls -G /dev/tty*`とやっていたのだが、下のようにどうにも背景色に茶色のような黄土色のような色がついて見づらい

<img src="/2016/07/19/change-ls-background-color/terminal.jpg" width="300px" height="200px">


ので、これを変更してやる
以下のリンク先を参考にした
[Terminalで「ls」したら「ls -G」が実行されるようにして、色も設定する。](http://taoru.hateblo.jp/entry/20120418/1334713778)


<!-- more -->

結論としては、shellの環境変数の設定のところに下記のような記述をして設定をしてやればよい
(自分は.bash_profileに設定した、.bashrcでも構わないので好きな所に設定する)
(ついでに、`$ ls` コマンドだけで色付けされるように `ls -G` のaliasを設定してやった)

```bash .bash_profile
alias ls='ls -G'
export LSCOLORS=gxfxcxdxbxegexabagacad
```

# LSCOLORSを変更してやればよい

lsに関して、manコマンドを打ってやるとLSCOLORの部分は以下のようになっている

```bash
$ man ls

LSCOLORS        The value of this variable describes what color to use for which attribute when colors are enabled with CLICOLOR.  This
                     string is a concatenation of pairs of the format fb, where f is the foreground color and b is the background color.

                     The color designators are as follows:

                           a     black
                           b     red
                           c     green
                           d     brown
                           e     blue
                           f     magenta
                           g     cyan
                           h     light grey
                           A     bold black, usually shows up as dark grey
                           B     bold red
                           C     bold green
                           D     bold brown, usually shows up as yellow
                           E     bold blue
                           F     bold magenta
                           G     bold cyan
                           H     bold light grey; looks like bright white
                           x     default foreground or background

                     Note that the above are standard ANSI colors.  The actual display may differ depending on the color capabilities of the
                     terminal in use.

                     The order of the attributes are as follows:

                           1.   directory
                           2.   symbolic link
                           3.   socket
                           4.   pipe
                           5.   executable
                           6.   block special
                           7.   character special
                           8.   executable with setuid bit set
                           9.   executable with setgid bit set
                           10.  directory writable to others, with sticky bit
                           11.  directory writable to others, without sticky bit

                     The default is "exfxcxdxbxegedabagacad", i.e. blue foreground and default background for regular directories, black
                     foreground and red background for setuid executables, etc.

````

ひとまず、アルファベットの組み合わせで設定すればいいことがわかる
デフォルトは `exfxcxdxbxegedabagacad` となっているがこれは http://d.hatena.ne.jp/edvakf/20080413/1208042916 によると
以下のような意味となっている


```bash
#	    Type	            Foreground	Background
1,2	    Directory	        blue	    (default)
3,4	    Symlink	        magenta	    (default)
5,6	    Socket	        green	    (default)
7,8	    Pipe	        brown	    (default)
9,10	    Executable	        red	    (default)
11,12	    Block	        blue	    cyan
13,14	    Character	        blue	    brown
15,16	    Exec. w/ SUID	black	    red
17,18	    Exec. w/ SGID	black	    cyan
19,20	    Dir, o+w, sticky	black	    green
21,22	    Dir, o+w, unsticky	black	    brown
```
(引用: [Terminal.appでlsのファイル色を変える](http://d.hatena.ne.jp/edvakf/20080413/1208042916))


つまり、アルファベットを22個並べて都度変更してやればよいとのこと
例えば、Directory(フォルダとか)だと、デフォルトの設定はForegroundは`e`でBackgroundは`x`なので
青文字にバックグラウンドカラーはデフォルトとなっている


最初の問題だったttyの背景色が茶色なのは、14番目のCharacterが`d`となっておりbrownに設定されているため
見難くなっていたので
リンク先の設定も踏まえて、以下のような並び(14番目はデフォルトの`x`で設定)で設定してやればよい

`export LSCOLORS=gxfxcxdxbxegexabagacad`

これを.bash_profileなり.bashrcなりshellの設定に入れてあげて再読み込みをすると、ちゃんと
背景色が消えて以下のようになって見やすくなった
<img src="/2016/07/19/change-ls-background-color/terminal2.jpg" width="300px" height="200px">
