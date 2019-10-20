---
layout: post
title: "GitHubにpushしたらDockerイメージを自動ビルドする"
date: 2016-08-03 13:42:38 +0900
comments: true
categories: docker
description: "Docker HubとGitHubを使ってDockerイメージの自動ビルドを行う方法を紹介します。自動ビルドを設定することで、常に最新のイメージがDocker Hubに用意され、DockerfileやREADMEも自動的にDocker Hubで公開されるようになります。"
---

Docker HubとGitHubを使ってDockerイメージの自動ビルドを行う方法を紹介します。

## 自動ビルドのメリット

自動ビルドのメリットは以下です。

- 常に最新のイメージがDocker Hubに用意される
- Dockerfileが公開され使う人が安心できる
- READMEも常に最新のものがDocker Hubに公開される

Docker Hubを利用するには事前にアカウントの作成が必要です。
Docker Hubのアカウントの作成は以下を参考にしてください。

- [Create a Docker Hub account & repository](https://docs.docker.com/engine/getstarted/step_five/)

## Docker Hubで自動ビルドの設定をする

すでにDockerfileがGitHubで管理されていることを前提に話しを進めます。

Docker Hubにログインしたら「Profile > Settings > Linked Accounts & Services.」を選択します。

GitHubを使うのでGitHubのパネルを選択し、認証をしてください。
認証が成功すると、以下のような画面になります。

![docker-auto-build-01](/images/docker-auto-build-01.png)

認証が済んだら、Docker Hubにリポジトリを作成します。

「 Create > Create Automated Build」を選択します。
![docker-auto-build-02](/images/docker-auto-build-02.png)

すると以下の画面が表示されます。

![docker-auto-build-03](/images/docker-auto-build-03.png)

GitHubを選択すると 「Users/Organizations」とリポジトリを選択できる画面になるので、自動ビルドを行うリポジトリを選択します。

Createボタンを押すと登録され、Docker Hubに表示されるようになります。

![docker-auto-build-04](/images/docker-auto-build-04.png)

![docker-auto-build-05](/images/docker-auto-build-05.png)

リポジトリの「Build Settings」でビルドの設定ができます。

Triggerボタンを押すとビルドが始まります。

![docker-auto-build-06](/images/docker-auto-build-06.png)

ビルドの状態は「Build Details」で確認できます。


![docker-auto-build-07](/images/docker-auto-build-07.png)

Queuedはビルド待ちのステータス。
Buildingはビルド中のステータスです。

GitHubにpushすればBuild Settingsで設定したブランチが自動的にビルドされます。

以上、簡単ですがDockerの自動ビルドの設定方法の紹介でした。
英語版ですが、詳しい方法が書いてあるのでこちらも参考にしてください。

* [Automated Builds on Docker Hub](https://docs.docker.com/docker-hub/builds/)
