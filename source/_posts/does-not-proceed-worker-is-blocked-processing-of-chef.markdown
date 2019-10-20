---
layout: post
title: "Chefのworkerが詰まって処理が進まなくなった"
date: 2016-05-20 17:40:56 +0900
comments: true
categories: chef
description: "chefを実行したらWARN: Chef client 24431 is running, will wait for it to finish and then run.というメッセージがでて、処理が進まない。2つのchef-clientが立ち上がっている状態。"
---

chefを実行したらこんなメッセージがでた。


```
WARN: Chef client 24431 is running, will wait for it to finish and then run.

```

前回実行したChefが途中で詰まってしまい、ウンともスンとも言わなくなったので Ctrl + Cでとめたが、プロセス自体は残ってしまっている。

こんな感じで2つchef-clientのworkerが立ち上がっている状態。


```
[shoyan@server01 ~]$ ps aux | grep chef
root     24373  0.0  0.1 178168  2992 ?        Ss   10:22   0:00 sudo -p knife sudo password:  chef-client -S http://127.0.0.1:18889
root     24426  0.0  3.1 262164 61072 ?        Sl   10:22   0:01 /opt/chef/embedded/bin/ruby /usr/bin/chef-client -S http://127.0.0.1:18889
root     24431  0.0  7.1 1078932 138052 ?      Sl   10:22   0:03 chef-client worker: ppid=24426;start=10:22:49;
root     26701  0.0  0.1 178168  2996 pts/3    Ss+  10:50   0:00 sudo -p knife sudo password:  chef-client -S http://127.0.0.1:18889
root     26754  0.0  3.1 261996 60896 pts/3    Sl+  10:50   0:01 /opt/chef/embedded/bin/ruby /usr/bin/chef-client -S http://127.0.0.1:18889
root     26759  0.0  3.0 261996 57760 pts/3    Sl+  10:50   0:00 chef-client worker: ppid=26754;start=10:50:38;

```

1時間ほど経過しても何も進まないので、プロセスをkillしてみた。

TERMシグナルを送ってみたが、反応なし。


```
# kill -s TERM 24431

```

killシグナルを送るとプロセスが終了し、ペンディング状態となっていたChefが実行された。


```
# kill -s KILL 24431

```
