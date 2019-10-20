---
layout: post
title: "最短でビデオチャットを開発したい人へ"
date: 2018-08-07 14:23:15 +0900
comments: true
categories: 開発
---
ビデオチャットを最短で作りたいと考えている人への備忘録です。次の手順に沿って開発すると4時間程度でビデオチャットを開発できます。

1. [WebRTCの概要を把握する](#1-webrtcの概要を把握する)
2. [WebRTCのコードを動かしてみる](#2-webrtcのコードを動かしてみる)
3. [WebRTCの開発を支援するサービスを利用する](#3-webrtcの開発を支援するサービスを利用する)

## 1. WebRTCの概要を把握する

まずはWebRTCについての概要を把握しておくと、実装するときの理解が深まります。まずは、次の記事を読んでください。

* <a href="https://gist.github.com/voluntas/67e5a26915751226fdcf" target="_blank">WebRTC コトハジメ</a>

同じタイトルですが、Qiitaのこの記事もわかりやすいです。

* <a href="https://qiita.com/yusuke84/items/286f569d110daede721e" target="_blank">WebRTC コトハジメ</a>

## 2. WebRTCのコードを動かしてみる

上記の記事を読んだら、早速コードを動かしてみましょう。コードを実際に動かしてみることで、着実にWebRTCの概念を理解していくことができます。

次の記事で簡単にPCのカメラの映像をブラウザに表示できることが体感してください。

* <a href="https://html5experts.jp/mganeko/19728/" target="_blank">カメラを使ってみよう ーWebRTC入門2016</a>

次の記事でシグナリングの流れを掴みましょう。

* <a href="https://html5experts.jp/mganeko/19814/" target="_blank">手動でWebRTCの通信をつなげよう ーWebRTC入門2016</a>

## 3. WebRTCの開発を支援するサービスを利用する

本格的なビデオチャットを開発するのはなかなか大変です。その開発を楽にしてくれるサービスが世の中に存在します。私がおすすめするのはSkyWayというサービスです。このサービスを利用することでNAT越えなどの仕組みを自分で実装せずにすみます。日本語ドキュメントが整備されており、サービス自体も無料で試すことができます。

* <a href="https://webrtc.ecl.ntt.com/" target="_blank">SkyWay</a>

SkyWayを利用するにあたっては、次のスライドが参考になります。

* <a href="https://www.slideshare.net/iwashi86/skyway-how-to-use-skyway-webrtc" target="_blank">SkyWayを使いこなすために - How to use SkyWay (WebRTC)</a>

余談ですが、WebRTCのライブラリであるPeerJSはメンテされてないので使わない方がよいです。
* <a href="https://medium.com/@Tukimikage/peerjs%E3%82%92%E4%BB%8A%E5%BE%8C%E3%82%82%E4%BD%BF%E3%81%84%E7%B6%9A%E3%81%91%E3%82%8B%E3%81%AE%E3%81%AF%E5%8D%B1%E9%99%BA-8c3cf68d56a0" target="_blank">PeerJSを今後も使い続けるのは危険</a>
