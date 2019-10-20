---
layout: post
title: "Jekyllチュートリアル"
date: 2016-12-06 23:49:36 +0900
comments: true
categories: jekyll ブログ作成
---

最近、ブログをリニューアルしようとJekyllをちょこちょこ触っている。
ブログを作る上でJekyllで知っておいたほうが良いことをまとめていきたいと思う。

## Jekyllとは

Jekyllは静的なサイトを作ることに特化したツールだ。
Ruby製でGithub pagesでも利用されている。
内部的には[Liquid](https://github.com/Shopify/liquid/wiki)が使われている。
Liquidはテンプレートエンジンで変数や制御文(foreachとか)を使うことができる。
Jekyllはそれをラップしたものだ。
Jekyllの何が便利かというと、サイトの設定をymlファイルで管理したりJekyllコマンドでMarkdownからhtmlファイルを生成したりすることができるという点だ。
また、サードパーティ製のテーマを利用できたりもする。

## ドキュメント

Jekyllはドキュメントがなかなかしっかりしているので、公式のドキュメントを見るのがよい。
* https://jekyllrb.com/

日本語版も用意してある。
* https://jekyllrb-ja.github.io/

まずドキュメントを探してみて、わからなければググるという感じで自分は調べている。

## 目次

* Jekyllをはじめよう
* Jekyllの構成について
* Jekyllのテーマを作成する
