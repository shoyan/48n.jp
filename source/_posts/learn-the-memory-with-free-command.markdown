---
layout: post
title: "freeコマンドで確認するOSのメモリ情報"
date: 2016-05-08 22:22:45
updated: 2016-05-08 22:22:45
comments: true
tags: Linux
description: "サーバーのメモリ情報はシステムの空きメモリと使用メモリの量を表示するfreeコマンドを使って確認することができます。
この記事を読むことでfreeコマンドの表示内容が理解できるようになり、OSのメモリ使用状況を把握できるようになります。"
---

サーバーのメモリ情報はシステムの空きメモリと使用メモリの量を表示する`free`コマンドを使って確認することができます。


```
[shoyan@server01 ~]$ free
             total       used       free     shared    buffers     cached
Mem:       1922324    1716832     205492          0     197248    1055352
-/+ buffers/cache:     464232    1458092
Swap:      4194300          0    4194300

```

-mオプションを使うとMB単位で表示させることができます。


```
[shoyan@server01 ~]$ free -m
             total       used       free     shared    buffers     cached
Mem:          1877       1676        200          0        192       1030
-/+ buffers/cache:        453       1423
Swap:         4095          0       4095

```

## まずどこを見るべきか

見るべきところは、3行目の `-/+ buffers/cache:        453       1423` の部分です。  
453MBが使用中で、1423MBは自由に使えるメモリがあります。

## 項目の説明

表示項目を確認していきます。

**total**: OSが認識している物理的なメモリサイズです。  
1877MBなので、およそ1.8GBです。  
RAIDカードやNICなどを装着しているときは、それらのためにメモリが使われるので実際の搭載メモリサイズよりも少なくなります。  
ですので、物理メモリとしては2GBなのですが、RAIDカードやNICでメモリが使用されてOSが認識しているメモリは1.8GBとなります。  

**used**: 使用しているメモリサイズです。  
バッファキャッシュとページキャッシュが含まれています。  
およそ1.6GBを使用しています。

**free**: 空きメモリサイズです。  
この値にはバッファキャッシュとページキャッシュが含まれていません。  
OSは使い続けるほどメモリにキャッシュを割り当てます。  
そのため、使い続けるほどfreeの値は0に近づきます。  
ですので、この値が少ないからといって空きメモリがないわけではありません。  
200MBのメモリがfreeとして表示されています。

**shared**: 共有メモリに割り当てられたメモリです。  
0なので共有メモリに割り当てられているメモリはありません。

**buffers**: バッファキャッシュに割り当てられたメモリです。  
バッファキャッシュとはブロックデバイス用のキャッシュです。  
192MBが割り当てられています。

**cached**: ページキャッシュに割り当てられたメモリです。  
ページキャッシュはファイルに対するキャッシュです。  
1030MBがページキャッシュとして割り当てられています。  

バッファキャッシュとページキャッシュという2つのキャッシュがでてきました。  
ここでは厳密にわけて考える必要はありません。  
バッファキャッシュとページキャッシュを合計したものがキャッシュとして使用されているメモリと考えます。  

**-/+ buffers/cache**: バッファキャッシュとページキャッシュを考慮したメモリです。  
1つ目の値がused、2つめの値がfreeです。

**used**: 2行目のusedからページキャッシュとバッファキャッシュを引いた値です。  
OSとアプリケーションが純粋に使用しているメモリサイズを表します。

実際に計算してみましょう。  
1676 - 192 - 1030 = 454  
ほぼあっていますね。

**free**: 2行目のfreeにページキャッシュとバッファキャッシュを足した値です。  
キャッシュに割り当てられているメモリを自由に割り当て可能なメモリと考えれば、この値が空きメモリサイズになります。

実際に計算してみましょう。  
200 + 192 + 1030 = 1422  
こちらもほぼあっています。

**Swap**: スワップに割り当てたサイズです。  
1つめの値がtotal、2つめの値がused、3つめの値がfreeです。

**total**: スワップに割り当てたディスクサイズです。  
**used**: 割り当てた中で使用中のサイズです。  
**free**: 割り当てた中で使用していないサイズです。

Swapのtotal値は4095MB、usedは0、freeは4095MBです。  
usedが0なのでスワップ領域は使われていないことになります。

## キャッシュはフリーメモリと考える

注意点として、freeの値がないからといってすぐにメモリ不足と判断することはできません。  
なぜなら、OSは処理の高速化のためにキャッシュを使うようになっているからです。  
時間の経過とともにキャッシュに割り当てられるメモリは増えていきます。  
キャッシュはフリーメモリと考えて問題ないようです。  

メモリの仕組みやキャッシュについて詳しく知りたい場合は以下の本をおすすめします。

<iframe src="http://rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=DBD1D1&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=ss_til&asins=479811703X" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>

## 参考文献
* 4.メモリ使用率(第5章 パフォーマンス管理～上級:基本管理コースII)
  - https://users.miraclelinux.com/technet/document/linux/training/2_5_4.html
