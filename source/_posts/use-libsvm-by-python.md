title: pythonでlibsvmを使えるようにする
date: 2016-07-20 15:55:50
tags:
- python
- machine learning
---

Interface 2016年7月号の [「ラズパイで始める人工知能コンピュータ」](http://www.cqpub.co.jp/hanbai/books/MIF/MIF201607.htm)
のなかでlibsvmを使ってみる部分が出てきて

とりあえず練習がてらMacでPython使ってやろうと思ったけど
なんかややこしかったのと初めてsys.path.append('')について知ったのでメモ

<!-- more -->

# libsvmをダウンロード、インストールする

installは下記サイトを参考にした

[Installing libsvm-3.1 for Python on Mac OS X 10.6](https://ie.u-ryukyu.ac.jp/tnal/archives/1837)

[libsvmのインストール & Matlab用インタフェースのコンパイル](http://halemaumau.blogspot.jp/2016/05/libsvm-matlab.html)


Xcodeやcommand line tools(makeとか)はインストール済みとする
El Capitanなら以下が参考になると思われる
http://qiita.com/keneo/items/1772adc2ebbde229fb71

自分の環境はMac OSX 10.11.5で、PythonはAnaconda 2.3.0(Python 3.4.5)を使用

基本的に参考サイト通りにダウンロードしてコンパイルしてやればよい
自分の場合はTerminalで以下のようにしてやった

(2016年7月20日の時点ではlibsvm-3.21だった)

```
$ wget http://www.csie.ntu.edu.tw/~cjlin/cgi-bin/libsvm.cgi?+http://www.csie.ntu.edu.tw/~cjlin/libsvm+tar.gz
$ tar xvfz libsvm-3.21.tar.gz
$ cd libsvm-3.21
$ make
$ cd python
$ make
```

途中でエラーなど出ず、libsvm-3.21のディレクトリに`libsvm.so.2`というファイルが生成されていたら成功(のはず)

で、ディレクトリの名前をlibsvm-3.21からlibsvmに変えてやった(これは好みなのでやらなくてもよい)
さらに適当な場所に移動させてコマンドのパスを通してやればどこからでも使うことが出来るようになる

自分はPATHを通すのに.bash_profileに記載した
例えば、`/Users/username/project/libsvm`というディレクトリ構成にしたかったら

```
$ mv libsvm-3.21 /Users/username/project/libsvm
$ vim .bash_profile

#.bash_profileに下記を追加
export PATH=$PATH:/Users/username/project/libsvm

$ source .bash_profile
```
で、後はコマンドでsvm-train, svm-predict, svm-scaleが使えたら成功
例えばsvm-trainとターミナルに打ち込むと以下のようになる

```
$ svm-train
Usage: svm-train [options] training_set_file [model_file]
options:
-s svm_type : set type of SVM (default 0)
	0 -- C-SVC		(multi-class classification)
	1 -- nu-SVC		(multi-class classification)
	2 -- one-class SVM
	3 -- epsilon-SVR	(regression)
	4 -- nu-SVR		(regression)
-t kernel_type : set type of kernel function (default 2)
	0 -- linear: u'*v
	1 -- polynomial: (gamma*u'*v + coef0)^degree
	2 -- radial basis function: exp(-gamma*|u-v|^2)
	3 -- sigmoid: tanh(gamma*u'*v + coef0)
	4 -- precomputed kernel (kernel values in training_set_file)
-d degree : set degree in kernel function (default 3)
-g gamma : set gamma in kernel function (default 1/num_features)
-r coef0 : set coef0 in kernel function (default 0)
-c cost : set the parameter C of C-SVC, epsilon-SVR, and nu-SVR (default 1)
-n nu : set the parameter nu of nu-SVC, one-class SVM, and nu-SVR (default 0.5)
-p epsilon : set the epsilon in loss function of epsilon-SVR (default 0.1)
-m cachesize : set cache memory size in MB (default 100)
-e epsilon : set tolerance of termination criterion (default 0.001)
-h shrinking : whether to use the shrinking heuristics, 0 or 1 (default 1)
-b probability_estimates : whether to train a SVC or SVR model for probability estimates, 0 or 1 (default 0)
-wi weight : set the parameter C of class i to weight*C, for C-SVC (default 1)
-v n: n-fold cross validation mode
-q : quiet mode (no outputs)
```


# pythonで使えるようにする

pythonで使うには、libsvmを設置した場所のPATHをスクリプト内に指定してやればよい

参考
http://kannokanno.hatenablog.com/entry/20130503/1367571825


つまり、スクリプト内でimportする時以下のようにすればよい
```py
# coding:utf-8

import sys
sys.path.append('/Users/username/project/libsvm/python/')
from svm import *
from svmutil import *
```

このスクリプトを動かしてみて、エラーがでなければ成功

これでlibsvmが使えるようになった