---
layout: post
title: "IntelliJでSpringBootのアプリケーションを作成する"
date: 2018-01-19 11:48:27 +0900
comments: true
categories: Java Spring
---
SpringBootのアプリケーションを作成する方法はいくつかありますが、今回はIntelliJで作成する方法を紹介します。

IntellJを起動してメニューバーから `File` > `New` > `Project...`を選択します。

左メニューよりSpring Initializrを選択してNextを押します。

![weather-nitify-slack](/images/create-spring-boot-project1.png)

Project MetaDataはアプリケーションごとに書き換えますが、今回はデモなのでデフォルトのままです。
Gradleを使うのでTypeをGradle Project を選択してNextを押します。

![weather-nitify-slack](/images/create-spring-boot-project2.png)

Dependenciesでは使いたいライブラリを選択できます。
今回は特に選択せず、Nextを押します。

![weather-nitify-slack](/images/create-spring-boot-project3.png)

Project nameはdemoで作成します。
Project locationでファイルを作成する場所を指定します。

![weather-nitify-slack](/images/create-spring-boot-project4.png)

Finishを押すとSpring Bootのアプリケーションが作成されます。
作成したファイルをGitHubにアップロードしています。

https://github.com/shoyan/spring-boot-demo
