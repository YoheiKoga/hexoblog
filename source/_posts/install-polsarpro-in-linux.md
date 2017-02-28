title: Install PolSARpro in Linux Lite / Linux LiteにPolSARproいれる
date: 2017-02-28 10:19:34
tags:
- PolSARpro
- Linux
---

> I write this article both English and Japanese
> この記事は日本語と英語で書きます

I tried to install PolSARpro5.0 in Linux Lite(one of the Lightweight Linux) 32bit

Linux LiteにPolSARPro5.0を入れてみた

<!-- more -->

<br>

## environment 開発環境

Linux Lite version 3.2 (32 bit)
memory: 4GB

I tried to installed in Linux on VirtualBox

もしVirtualBox上のLinuxに入れる場合はまず仮想環境を用意しておく

自分の場合は下記通りに行った

[VirtualBoxにLinux Liteを入れる](http://yoheikoga.github.io/2017/02/28/linux-in-virtualbox/)

日本語化しておく場合は下記を参考

http://superjeter007.blog.jp/archives/3651184.html

<br>

## libraries install/ライブラリのインストール

open the Command Line app and install Libraries and software which are necessery for PolSARpro

command lineアプリを開いて、PolSARproに必要なライブラリやソフトウェアをインストールしていく


<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro1.jpg" width="300px" height="200px">


```
$ sudo apt-get update
$ sudo apt-get upgrade -y
$ sudo apt-get install gnuplot tcl-dev tk-dev bwidget iwidgets4 itcl3 itk3 libtk-img
```

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro2.jpg" width="300px" height="200px">


<br>

## softwares install/ソフトウェアのインストール

softwares under those are necessery (someones are already in Linux)

下記ソフトが必要となる。いくつかはすでにLinuxに入っている

- PDF reader
    - Linux Lite already has /usr/bin/evince
    - Linuxにevinceというのが入っている
- GIMP viewer
    - Linux Lite already has /usr/bin/gimp
    - gimpはすでに入ってる
- IMAGE-MAGICK
    - Linux Lite already has /usr/bin/convert
    - convertがすでに入っている
- GOOGLE Earth
    - you have to install
    - インストールする必要がある
- MAP READY(asf_mapready)
    - you have to install
    - インストールする必要がある
- SNAP S1-TBX(s1tbx)
    - you have to install
    - インストールする必要がある

<br>

you can check to use which command there is software or not

gimpとかevinceとかconvertが入っているかはwhichコマンドで確認できる

```
linuxlitevm@linuxlitevm-VirtualBox:~$ which gimp
/usr/bin/gimp
linuxlitevm@linuxlitevm-VirtualBox:~$ which convert
/usr/bin/convert
linuxlitevm@linuxlitevm-VirtualBox:~$ which evince
/usr/bin/evince
```

<br>


next, install remain software

で、残りのソフトをインストールしていく

<br>

### GOOGLE Earth

installation proseeded by command line

command lineからインストールしていく

```
$ cd ~/Downloads
$ sudo apt-get install gdebi
$ sudo apt-get install libfontconfig1:i386 libx11-6:i386 libxrender1:i386 libxext6:i386 libgl1-mesa-glx:i386 libglu1-mesa:i386 libglib2.0-0:i386 libsm6:i386
$ wget http://dl.google.com/dl/earth/client/current/google-earth-stable_current_i386.deb
$ sudo gdebi google-earth-stable_current_i386.deb
```

if you success, be able to check by which command

成功したらwhich commandでチェックできる。/usr/bin/google-earthと出てきたら成功

```
$ which google-earth
/usr/bin/google-earth
```

<br>

### MapReady

install by command line

command lineからインストールしていく

```
$ sudo apt-get install git bison flex g++ gcc libgdal-dev libgtk2.0-dev libglade2-dev libgsl0-dev libproj-dev libtiff-dev libgeotiff-dev libshp-dev fftw-dev libcunit1-dev libfftw3-dev -y
$ cd ~/Downloads
$ git clone https://github.com/asfadmin/ASF_MapReady.git
$ cd ASF_MapReady
$ sudo ./configure
$ sudo make
$ sudo make install
```

<br>

if you success, you can see the sentence `The ASF tools Makefile is now fully prepared.` like under picture

まず、`$ sudo ./configure`に成功すると下記画像のように`The ASF tools Makefile is now fully prepared.`と出てくる

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro3.jpg" width="300px" height="200px">


<br>

after success `$ sudo make`, you can see "XXXXXXXXXXXX ASF MapReady Package Compiled! XXXXXXXXXXX"

`$ sudo make`に成功したら"XXXXXXXXXXXX ASF MapReady Package Compiled! XXXXXXXXXXX"と最後に出てくる

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro4.jpg" width="300px" height="200px">


finally, you do `$ sudo make install`

最後に、`$ sudo make install`するだけ

<br>

if you success install asf_mapready, you can check which command

asf_mapreadyのインストールに成功していたら、whichコマンドで確認できる

```
$ which asf_mapready 
/usr/local/bin/asf_mapready
```

<br>

### when fail `./configure` / もし`./configure`に失敗したら

after enter `sudo ./configure`, if you fail configuration, you can see like under that sentence

`sudo ./configure`コマンドをうって、もし失敗したら下記のような分が出てくることがある

```
$ sudo ./configure
...
library gsl not found

```

for example above, you have to install gsl library by apt-get command 

例えば上記の場合、gsl libraryをapt-getコマンドでインストールすればよい

```
$ sudo apt-get install libgsl0-dev
```

what name library you have to enter when install that, you should search on web

もしlibraryがないって言われたときは
ライブラリ名をなんと打ち込めばよいかなどは、その都度webで検索するなどが必要

<br>


### SNAP S1-TBX

you can install by command line, from github repository

githubリポジトリからもってきてコンパイルする

https://github.com/senbox-org/s1tbx

<br>

the step is below

下記ステップで行う

1. install Java8 and Maven, set PATH correctly
2. clone S1TBX
3. mvn install each snap

in github repository, written to set IntelliJ IDEA, but you don't need install IntelliJ IDEA to use PolSARpro

githubリポジトリにはIntelliJ IDEA(JavaのIDE)の設定方法もあるが、PolSARproには必要ないのでインストールしないで大丈夫


<br>

### 1. install Java8 and Maven

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```

on the way, you need to agree license term of oracle

途中、oracleのlicense termに同意してくださいみたいなのが出るので"Yes"を選択してエンター

<br>

check java version

javaのバージョンを確認する

```
linuxlitevm@linuxlitevm-VirtualBox:~$ java -version
java version "1.8.0_121"
Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
Java HotSpot(TM) Client VM (build 25.121-b13, mixed mode)
```

finally, setting JAVA_HOME PATH by below command

最後に、下記コマンドでJAVA_HOMEのPATHを設定する

```
$ sudo sh -c "echo 'JAVA_HOME=/usr/lib/jvm/java-8-oracle' >> /etc/environment"
$ sudo sh -c "echo 'JRE_HOME=/usr/lib/jvm/java-8-oracle/jre' >> /etc/environment"
```


check by cat command like below

catコマンドで設定できたか確認できる、下記のように出てくる。

```
$ sudo cat /etc/environment 
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
JAVA_HOME=/usr/lib/jvm/java-8-oracle
JRE_HOME=/usr/lib/jvm/java-8-oracle/jre
```

<br>

next, install Maven

次に、Mavenをインストールする

```
$ cd /usr/local
$ sudo wget http://ftp.riken.jp/net/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
$ sudo tar xzf apache-maven-3.3.9-bin.tar.gz
$ sudo ln -s apache-maven-3.3.9 apache-maven
$ sudo sh -c "echo 'export JAVA_HOME=/usr/lib/jvm/java-8-oracle' >> /etc/profile.d/apache-maven.sh"
$ sudo sh -c "echo 'export M2_HOME=/usr/local/apache-maven' >> /etc/profile.d/apache-maven.sh"
$ sudo sh -c "echo 'export MAVEN_HOME=/usr/local/apache-maven' >> /etc/profile.d/apache-maven.sh"
$ sudo sh -c "echo 'export PATH=\${M2_HOME}/bin:\${PATH}' >> /etc/profile.d/apache-maven.sh"
```

reboot Linux Lite

Linux Liteを再起動する

<br>

after reboot, check cat command and mvn version command, below is correct

再起動後、catコマンドとmvn versionコマンドで確認して下記ようだったらok

```
$ cat /etc/profile.d/apache-maven.sh 
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export M2_HOME=/usr/local/apache-maven
export MAVEN_HOME=/usr/local/apache-maven
export PATH=${M2_HOME}/bin:${PATH}

$ mvn -version
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T01:41:47+09:00)
Maven home: /usr/local/apache-maven
Java version: 1.8.0_121, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-oracle/jre
Default locale: ja_JP, platform encoding: UTF-8
OS name: "linux", version: "4.4.0-45-generic", arch: "i386", family: "unix"
```


<br>

### 2. clone S1TBX

clone S1TBX where you want. in my case, I make home directory

S1TBXをクローンしていく。ディレクトリはどこでもいいが、自分はhomeディレクトリに設置した

```
$ cd
$ mkdir SNAP
$ cd SNAP
$ git clone https://github.com/senbox-org/s1tbx.git
$ git clone https://github.com/senbox-org/snap-desktop.git
$ git clone https://github.com/senbox-org/snap-engine.git
$ git clone https://github.com/senbox-org/snap-installer.git
```

next, proseed `mvn install`. after each install, I add picture of command line

次に`mvn install`していく。終わったらそれぞれコマンドラインの写真を貼っておく

```
$ cd ~/SNAP/snap-engine
$ mvn install
```

it took about 1hr in my case especially "Watermask Operator Test"(Its time to take rest)

自分の場合はここで1時間ぐらいかかった、特に"Watermask Operator Test"みたいなところでかなり時間がかかった

after

成功したらBUILD SUCCESSと出て来る

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro5.jpg" width="300px" height="200px">



<br>

next snap-desktop


```
$ cd ~/SNAP/snap-desktop
$ mvn install -DskipTests
```


after

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro6.jpg" width="300px" height="200px">

<br>

```
$ cd ~/SNAP/s1tbx
$ mvn install -DskipTests
```

after

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro7.jpg" width="300px" height="200px">



<br>

at first, I tried to normal `$ mvn install` but I catch Test Failure.
so insted of that, I skip Test by `$ mvn install -DskipTests`

最初は普通に`$ mvn install`していたが、テストで失敗したので
代わりに`$ mvn install -DskipTests`でテストをスキップした。

<br>

so far, library and periferal software was installed. so next install PolSARpro

ここまでで、必要なソフトやライブラリはインストールできたので

次にPolSARproをインストールしていく

<br>

## install PolSARpro5.0

finally, install PolSARpro5.0 for Linux

最後に、PolSARpro5.0のLinuxバージョンをインストールする

```
$ cd ~/Downloads
$ wget https://earth.esa.int/documents/653194/1960708/PolSARpro_v5.0.4_Linux_20150607
$ mkdir polsar
$ mv PolSARpro_v5.0.4_Linux_20150607 polsar/PolSARpro.rar
$ cd polsar
$ unrar x PolSARpro
$ cd Soft
```

next, rewrite backslash(\) to slash(/) propery for Linux path in Compile_PolSARpro_v5_Linux.bat file

by vim editor(so if you don't have vim, `$ sudo apt-get install vim`)

次に、Compile_PolSARpro_v5_Linux.batファイル内のバックスラッシュ(\)を適切にスラッシュ(/)に書き換える

vimがあると楽なのでvimで置き換える(viでもできる),vimが入ってない場合は`$ sudo apt-get install vim`しておく

<br>

at first, Compile_PolSARpro_v5_Linux.bat file is like below

最初、Compile_PolSARpro_v5_Linux.batファイルは下記のようになっている

```sh Compile_PolSARpro_v5_Linux.bat
cd PolSARproSIM
echo "PolSARproSIM"
gcc -o PolSARproSim.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSim_ImgSize.exe PolSARproSim_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd PolSARproSIMgr
echo "PolSARproSIMgr"
gcc -o PolSARproSim_gr.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSimGR_ImgSize.exe PolSARproSimGR_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd PolSARproSIMsv
echo "PolSARproSIMsv"
gcc -o PolSARproSim_sv.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSimSV_ImgSize.exe PolSARproSimSV_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd SVM
echo "SVM"
g++ -Wall -g -Wconversion -O3 -c svm.cpp
g++ -Wall -g -Wconversion -O3 svm-predict.c svm.o -o svm_predict_polsarpro.exe -lm
g++ -Wall -g -Wconversion -O3 svm-train.c svm.o -o svm_train_polsarpro.exe -lm
g++ -Wall -g -Wconversion -O3 svm-scale.c svm.o -o svm_scale_polsarpro.exe -lm
gcc -g ../lib/PolSARproLib.c svm_classifier.c -o svm_classifier.exe -lm
gcc -g ../lib/PolSARproLib.c write_best_cv_results.c -o write_best_cv_results.exe -lm
gcc -g ../lib/PolSARproLib.c grid_polsarpro.c -o grid_polsarpro.exe -lm
gcc -g ../lib/PolSARproLib.c svm_confusion_matrix.c -o svm_confusion_matrix.exe -lm
cd ..

cd PolSARap
echo "PolSARap"
g++ -I ..\lib\alglib ..\lib\alglib\ap.cpp ..\lib\alglib\alglibinternal.cpp ..\lib\alglib\linalg.cpp ..\lib\alglib\alglibmisc.cpp ..\lib\alglib\solvers.cpp ..\lib\alglib\optimization.cpp PolSARap_Cryosphere_Decomposition.c -o PolSARap_Cryosphere_Decomposition.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Cryosphere_Inversion.c -o PolSARap_Cryosphere_Inversion.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Agriculture_Decomposition.c -o PolSARap_Agriculture_Decomposition.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Agriculture_Inversion_Dihedral.c -o PolSARap_Agriculture_Inversion_Dihedral.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Agriculture_Inversion_Surface.c -o PolSARap_Agriculture_Inversion_Surface.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Forest_Height_Estimation_Dual_Baseline.c -o PolSARap_Forest_Height_Estimation_Dual_Baseline.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Ocean.c -o PolSARap_Ocean.exe -lm
gcc -g -Wall ..\lib\PolSARproLib.c PolSARap_Urban.c -o PolSARap_Urban.exe -lm
cd ..

LIB_FILES="lib/PolSARproLib.c"
C_FILES=`find ./ \( -name "*.c" \) | egrep -v "lib" | egrep -v "PolSARproSIM" | egrep -v "PolSARproSIMgr" | egrep -v "PolSARproSIMsv"  | egrep -v "SVM"  | egrep -v "PolSARap" `
for file in $C_FILES
do
echo $file
gcc -g -Wall $LIB_FILES $file -o ${file%.c}.exe  -lm
done

chmod -R 777 *
```


<br>

after rewrite backslash correctly, below that 

正しく書き換えたものが下記になる

```sh Compile_PolSARpro_v5_Linux.bat
cd PolSARproSIM
echo "PolSARproSIM"
gcc -o PolSARproSim.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSim_ImgSize.exe PolSARproSim_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd PolSARproSIMgr
echo "PolSARproSIMgr"
gcc -o PolSARproSim_gr.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSimGR_ImgSize.exe PolSARproSimGR_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd PolSARproSIMsv
echo "PolSARproSIMsv"
gcc -o PolSARproSim_sv.exe PolSARproSim.c Allometrics.c Attenuation.c Branch.c c3Vector.c c33Matrix.c Complex.c Cone.c Crown.c Cylinder.c d3Vector.c d33Matrix.c Drawing.c Facet.c GraphicIMage.c GrgCyl.c Ground.c InfCyl.c JLkp.c Jnz.c Leaf.c LightingMaterials.c MonteCarlo.c Perspective.c Plane.c PolSARproSim_Direct_Ground.c PolSARproSim_Forest.c PolSARproSim_Procedures.c PolSARproSim_Progress.c PolSARproSim_Short_Vegi.c Ray.c RayCrownIntersection.c Realisation.c SarIMage.c Shuffling.c Sinc.c soilsurface.c Spheroid.c Tree.c YLkp.c -lm
gcc -o PolSARproSimSV_ImgSize.exe PolSARproSimSV_ImgSize.c -lm
gcc -o PolSARproSim_FE_Kz.exe ../lib/PolSARproLib.c PolSARproSim_FE_Kz.c -lm
cd ..

cd SVM
echo "SVM"
g++ -Wall -g -Wconversion -O3 -c svm.cpp
g++ -Wall -g -Wconversion -O3 svm-predict.c svm.o -o svm_predict_polsarpro.exe -lm
g++ -Wall -g -Wconversion -O3 svm-train.c svm.o -o svm_train_polsarpro.exe -lm
g++ -Wall -g -Wconversion -O3 svm-scale.c svm.o -o svm_scale_polsarpro.exe -lm
gcc -g ../lib/PolSARproLib.c svm_classifier.c -o svm_classifier.exe -lm
gcc -g ../lib/PolSARproLib.c write_best_cv_results.c -o write_best_cv_results.exe -lm
gcc -g ../lib/PolSARproLib.c grid_polsarpro.c -o grid_polsarpro.exe -lm
gcc -g ../lib/PolSARproLib.c svm_confusion_matrix.c -o svm_confusion_matrix.exe -lm
cd ..

cd PolSARap
echo "PolSARap"
g++ -I ../lib/alglib ../lib/alglib/ap.cpp ../lib/alglib/alglibinternal.cpp ../lib/alglib/linalg.cpp ../lib/alglib/alglibmisc.cpp ../lib/alglib/solvers.cpp ../lib/alglib/optimization.cpp PolSARap_Cryosphere_Decomposition.c -o PolSARap_Cryosphere_Decomposition.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Cryosphere_Inversion.c -o PolSARap_Cryosphere_Inversion.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Agriculture_Decomposition.c -o PolSARap_Agriculture_Decomposition.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Agriculture_Inversion_Dihedral.c -o PolSARap_Agriculture_Inversion_Dihedral.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Agriculture_Inversion_Surface.c -o PolSARap_Agriculture_Inversion_Surface.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Forest_Height_Estimation_Dual_Baseline.c -o PolSARap_Forest_Height_Estimation_Dual_Baseline.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Ocean.c -o PolSARap_Ocean.exe -lm
gcc -g -Wall ../lib/PolSARproLib.c PolSARap_Urban.c -o PolSARap_Urban.exe -lm
cd ..

LIB_FILES="lib/PolSARproLib.c"
C_FILES=`find ./ \( -name "*.c" \) | egrep -v "lib" | egrep -v "PolSARproSIM" | egrep -v "PolSARproSIMgr" | egrep -v "PolSARproSIMsv"  | egrep -v "SVM"  | egrep -v "PolSARap" `
for file in $C_FILES
do
echo $file
gcc -g -Wall $LIB_FILES $file -o ${file%.c}.exe  -lm
done

chmod -R 777 *
```

(line47, there are two backslash which is necessery, should not change slash them)

(47行目に、必要なバックスラッシュが2つあるのでそれはスラッシュに変更しないように注意)


```sh
# dont change below backslash, caution
C_FILES=`find ./ \( -name "*.c" \) | egrep -v "lib" | egrep -v "PolSARproSIM" | egrep -v "PolSARproSIMgr" | egrep -v "PolSARproSIMsv"  | egrep -v "SVM"  | egrep -v "PolSARap" `
```

<br>

and run Compile_PolSARpro_v5_Linux.bat

で、Compile_PolSARpro_v5_Linux.batを実行する


```
$ sudo chmod a+x Compile_PolSARpro_v5_Linux.bat
$ sudo ./Compile_PolSARpro_v5_Linux.bat
$ cd ..
$ sudo chmod a+x PolSARpro_v5.0.tcl 
$ sudo ./PolSARpro_v5.0.tcl
```

PolSARpro is launched by `$ sudo ./PolSARpro_v5.0.tcl`

`$ sudo ./PolSARpro_v5.0.tcl`をしてPolSARproが立ち上がったら成功

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro8.jpg" width="300px" height="200px">


<br>


Installation of PolSARpro was finished. but you have to link softwares which install then and PolSARpro

PolSARproのインストールはこれで終わりだが、下記ソフトを連携させないといけない

- PDF reader (evince)
- Google Earth
- GIMP VIEWER
- IMAGE-MAGICK
- MapReady
- SNAP S1-TBX


<br>

## link softwares

after enter PolSARpro, you can see picture below

you have to link PDF viewer

PolSARproをEnterすると、最初に下記画像のようなものが出てくる

PDF viewerと連携させないといけない

<br>


<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro9.jpg" width="300px" height="200px">

<div style="text-align:center;">click ok</div>

<br>

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro10.jpg" width="300px" height="200px">

<div style="text-align:center;">click Yes</div>

<br>

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro11.jpg" width="300px" height="200px">

<div style="text-align:center;">click open file icon</div>

<br>

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro12.jpg" width="300px" height="200px">

<div style="text-align:center;">
<span>1. enter "/usr/bin/evince"</span>
<span>2. click "Filter"</span>
<span>3. check "evince"</span>
<span>4. click "OK"</span>
</div>

<br>


<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro13.jpg" width="300px" height="200px">

<div style="text-align:center;">click Save & Exit</div>

<br>


<br>

after that, linking PDF reader is success

これで、PDFリーダーとリンクさせることができた


in same way, you can link `Google Earth, GIMP, IMAGE-MAGICK, MAP Ready, SNAP S1-TBX`

each software paths are below

同様にして`Google Earth, GIMP, IMAGE-MAGICK, MAP Ready, SNAP S1-TBX`とリンクさせる

それぞれのソフトウェアのpathは以下のとおり

- Google Earth: /usr/bin/google-earth
- gimp: /usr/bin/gimp
- ImageMagick: /usr/bin/convert
- ASF MapReady: /usr/local/bin/asf_mapready
- S1 SNAP: /home/linuxlitevm/SNAP/snap-desktop/snap-application/target/snap/bin/snap


<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro14.jpg" width="300px" height="200px">

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro15.jpg" width="300px" height="200px">

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro16.jpg" width="300px" height="200px">

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro17.jpg" width="300px" height="200px">

<img src="/2017/02/28/install-polsarpro-in-linux/polsarpro18.jpg" width="300px" height="200px">


<br>

that's all! You can start to use PolSARpro!

これで全部、PolSARproを使えるようになりました!
