---
layout: post
title: "AllowGroupsの問題でsshログインできない"
date: 2016-05-06 13:33:16 +0900
comments: true
categories: linux
description: "sshログインできないので/var/log/secureのログを見てみたところ、none of user's groups are listed in AllowGroupsとメッセージがでていました。
その場合はAllowGroupsを追加することによってログインできるようになります。"
---

sshログインできないので `/var/log/secure` のログを見てみました。
以下のメッセージがでていました。


```
Apr 28 14:11:14 server01 sshd[31264]: User shoyan from example.jp not allowed because none of user's groups are listed in AllowGroups
Apr 28 14:11:14 server01 sshd[31265]: input_userauth_request: invalid user shoyan
Apr 28 14:11:14 server01 sshd[31265]: Connection closed by 192.168.1.1

```

`none of user's groups are listed in AllowGroups` と書いてあるので、groupの問題らしい。

`/etc/ssh/sshd_config`をみると、AllowGroups にいなかったのでユーザーのグループを追加しました。

sshd_configを変更したら、設定を反映させるためにreloadします。


```
# service sshd reload
sshd を再読み込み中:                                       [  OK  ]

```

AllowGroupsにユーザーのグループを追加することによってログインできるようになりました。


### 参考文献

- http://open-groove.net/linux/sshd-restart/
- https://www.howtoforge.com/community/threads/cannot-ssh-with-shell-user.52800/i
