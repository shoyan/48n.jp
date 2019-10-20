---
layout: post
title: "GitHubのリポジトリを監視するGitMonitor"
date: 2016-07-27 13:37:18 +0900
comments: true
categories: git
description: "
GitHubのリポジトリを監視するGitMonitorというサービスを紹介します。このサービスはGithubのリポジトリを監視して、そのリポジトリに行った操作をダッシュボードで確認することができます。"
---

GitHubのリポジトリを監視する[GitMonitor](https://gitmonitor.com/)というサービスを紹介します。
このサービスはGithubのリポジトリを監視して、そのリポジトリに行った操作をダッシュボードで確認することができます。
また、Flowdock、Slack、Emailに通知することも可能のようです。

以下は、masterに直接pushしたときのログです。

![git-monitor-image](/images/git_monitor.png)

GitMonitorは様々なルールが設定でき、そのルールに該当したものがダッシュボードに表示されます。
例えば、LGTMがないPull Requestがマージされたときや、リストに定義していない人がマージしたときに通知することもできるようです。

操作の制限ができるわけではなく、ルールに該当したときにダッシュボードに通知されるだけのようです。

30日間は無料で使えて、それ以降は有料プランとなります。
有料プランは3つあって、Small($10 / mon)、Medium($20 / mon)、Large($35 / mon) が選べます。
監視するリポジトリの数がプランによって違います。
