---
layout: post
title: "データベース固有の関数の使用を避けるべき理由"
date: 2019-04-25 16:21:18
updated: 2019-04-25 16:21:18
comments: true
tags: ソフトウェアアーキテクチャ
---
こんにちは、しょーやんです。

私はエンジニアとして12年目で、現在はエンジニアチームのリーダーとしてWebアプリケーションのシステムを開発しています。キャリアとしては、SIerで1年、ベンチャー企業で3年、300人程度の企業で5年、現在は6000人規模の会社でエンジニアとして働いています。

前提として、ここで話すことはアプリケーション設計に関する話しです。データベースの関数自体の使用を否定しているわけではありません。データベース固有の関数を使う前に少し考えてみましょうという話しです。まずは、データベース固有の関数を使うリスクについて説明します。

## データベース固有の関数を使うリスク

データベースには固有の関数が用意されています。MySQLだとDATE_FORMATやNOW、SUMといったような関数です。これらを利用するのは便利ですが、安易な利用はおすすめしません。相応の理由がない限りは避けるべきです。理由は、データベース固有の関数を利用すると移植性が失われてしまうからです。

多くの人はデータベースを変更することはほとんどないだろうと考えます。本当にそうでしょうか。

例えば、開発環境ではSQLite、プロダクション環境ではMySQLを使うということは特段珍しいことではありません。
トランザクション境界を分離できないようなインテグレーションテストを実行するときはどうでしょうか。

ユーザーの情報を取得するAPIのインテグレーションテストがあるとします。このテストをクリアするためには、事前にユーザーのデータをデータベースに登録しておくことが必要です。そうでなければユーザーのデータを検証することができません。
テスト用のデータベースを共有で利用していた場合、一時的に作成されたテストデータが他のテストに影響するようになるでしょう。この問題は複数のテストが同時に実行されるようなCI環境になると顕著に現れます。解決策の1つとしてプロダクション環境と同じデータベースが含まれたイメージを作成するという手がありますが、複雑なイメージファイルを作成することは、なるべくなら避けたいところです。

## データベースはドアノブである

ロバート・C・マーティンは著書「Clean Architecture」で、データベースはあくまで道具の1つであり、アーキテクチャの中心になるものではないと言っています。データベースは家のドアノブのようなものであり、アーキテクチャ的にはどうでもよいのです。ドアノブに家の設計を合わせることはしないでしょう。データベースがドアノブのようなものであれば、データベースに依存しないようにアプリケーションを実装するのは当然のことのように思えます。

RailsやSpringのような現在のフレームワークは、データベースを抽象化して扱えるような仕組みを提供しています。多くの場合、それはORMとして提供されており、利用するドライバーの設定を変更するだけでデータベースを変更することが可能です。

アプリケーション設計の側面から考えると、データベース固有の関数は避けるべきです。データベース固有の関数を利用する必要が場合は、基本的なCRUDで同じことができないかを検討しましょう。さもなければ、たった1つのデータベース固有の関数のせいでアプリケーションの移植性は失われてしまいます。

## 原則としてアプリケーション側で対応する

多くのアプリケーションは基本的なCRUDで構築することが可能です。少しの手間を省くためにデータベース固有の関数（例えばMySQLのREPLACE）を利用することはデメリットの方が大きくなる可能性があります。よく見られるアンチパターンはNOWの多様です。時刻はアプリケーション側で取得できます。アプリケーション側で対応できるものはアプリケーションの機能で対応しましょう。

## データベース固有の機能に頼った方がいい場合

データベース固有の機能に頼った方がいい場合も存在します。例えば、位置情報を扱うような場合です。位置情報を扱う場合、PostgreSQLの拡張であるPostGISを利用した方が少ない労力で実装することができるでしょう。このように明確なアドバンテージがある場合はデータベース固有の機能を利用しない理由はありません。

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4048930656&linkId=bd16a1851920993a41c2031b32cd6769"></iframe>
