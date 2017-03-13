title: get started PolSARpro by sample data
date: 2017-03-03 17:35:03
tags:
- PolSARpro
---


I tried to use [PolSARpro](https://yoheikoga.github.io/tags/PolSARpro/)

let's get started PolSARpro by using sample data

My computer environment is Linux Lite 32bit

<!-- more -->

in this article, using this sample(link is where I got samples)

- [ALOS](http://www.alos-restec.jp/staticpages/index.php/service-sampledata-04)


ALOS: choose level 1.1(L1.1)

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos1.jpg" width="300px" height="200px">

<br>


and files decompressed are in a directory as you like

I make directory, which name is "sampledata_polsar", under PolSARpro application folder

like below

<img src="/2017/03/03/use-sample-data-by-polsarpro/samples.jpg" width="300px" height="200px">


<br>

next, try to make images

so open PolSARpro application

<br>

## ALOS

1. set Environment
- Data Processing Level 1.1 -> Dual - Pol -> JAXA - CEOS Format

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos2.jpg" width="300px" height="200px">

<br>

- click Environment

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos3.jpg" width="300px" height="200px">

<br>

- open "Main Input Directory" and choose PolSARpro directory(not psr_plr11 directory)
- click Save & Exit
- you can see "NO DATA FILES FOUND ~..." window, choose NO 


<img src="/2017/03/03/use-sample-data-by-polsarpro/alos4.jpg" width="300px" height="200px">

<img src="/2017/03/03/use-sample-data-by-polsarpro/nodatafiles.jpg" width="300px" height="200px">

<br>

- click Import
- open SAR Leader File, so choose LED-... file under psr_plr11 directory
- click Check Files -> OK
- click Read Header
- click OK -> OK

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos5.jpg" width="300px" height="200px">

<br>

- click Import -> Extract -> Full Resolution
- choose OutPut directory, I selected under psr_plr11 directory
- click Run

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos6.jpg" width="300px" height="200px">

<br>


and after waiting minutes, extracted an image of ALOS

in this way, the size is very little(about 3%), so you can expanding

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos7.jpg" width="300px" height="200px">

<img src="/2017/03/03/use-sample-data-by-polsarpro/alos8.jpg" width="300px" height="200px">


For ALOS, that's all.

<br>


