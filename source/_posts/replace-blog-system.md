---
title: ブログシステムをHexoにしました
date: 2019-10-31 18:30:30
tags: 
  - Hexo 
  - ブログ 
  - 静的サイトジェネレーター
description: ブログシステムをHexoにしました。Hexoのメリットや他のフレームワークであるGatsubyやVuePressと比較した結果などをまとめました。
---

ブログシステムを<a href="https://hexo.io/" target="_blank" class="outbound">Hexo</a>にしました。ブログデザインも新規で作成しました。ブログシステムはそのうちリプレースしたいなと常々思っていたのですが、なかなかこれといったものがなく時間が経ってしまいましたが、ついにリプレースすることができました。

## 以前のブログシステム

以前のブログシステムはOctopressを使っていました。このOctopress、いくつか問題点がありました。

### 動作が遅い

Markdownで書いてそれをhtmlに変換する仕組みなのですが、その速度が遅いです。
記事数が少ない場合はそれほど気にならなかったのですが、100記事くらいになってくると明らかに遅くなってきます。
現在では、プレビューモードで変換するのに10秒程度かかってしまい、気持ちよく記事が書けない状態でした。

### メンテナンスされていない
2015年で開発が止まっています。新機能や改善もないですし、機能に問題がある場合も修正がされません。開発が止まっているものは使うべきではありません。今から使うのはやめたほうがいいでしょう。

## なぜHexoにしたのか

### Node.jsで書かれているから

HexoはNode.jsで書かれています。
最近はNode.jsを使うことが多いので、Node.jsで書かれているブログシステムを使いたかったのです。

### 静的サイトジェネレーターだから

ブログシステムには大きく分けて静的サイトジェネレーターとデータベースを使ったブログシステムがあります。Hexoは静的サイトジェネレーターと言われるシステムです。静的サイトジェネレーターはMarkdown等の形式で書かれたファイルをhtmlに変換するソフトウェアです。

静的サイトジェネレーターのメリットは表示速度が早い、セキュリティリスクがほとんどない、他のシステムに移行しやすいなどのメリットがあります。個人ブログであれば静的サイトジェネレーターがおすすめです。

### 人気のあるブログシステムだから

Node.jsで書かれていて人気のあるブログシステムはHexoとGatsby、あとは最近VuePressも人気が出てきているようです。
他にどんなソフトウェアがあるかはこちらで確認することができます。
* <a href="https://github.com/topics/static-site-generator" target="_blank" class="outbound">static-site-generator · GitHub Topics</a>

人気のあるソフトウェアは開発が活発で便利な機能も多く、使いやすいことが多いです。

### 現在も開発が行われているから

Hexoは現在も開発が行われています。2019-10-14にバージョン4.0のリリースが行われました。

* <a href="https://hexo.io/news/2019/10/14/hexo-4-released/" target="_blank" class="outbound">Hexo 4.0.0 Released | Hexo</a>

## Hexo vs Gatsby vs VuePress

Node.js環境の静的サイトジェネレーターはHexo、Gatsby、VuePressの3つが人気です。GatsbyとVuePressは次の理由で採用しませんでした。

### Gatsbyを採用しなかった理由

<a href="https://www.gatsbyjs.org/" target="_blank" class="outbound">Gatsby</a>はReactベースの静的サイトジェネレーターです。Reactに慣れている人であればいいかもしれませんが、私がReactに詳しくないのもあり、Gatsbyは学習コストが高いという印象です。あえてその学習コストを払ってGatsbyを使うメリットが見当たらないので採用を見送りました。

### VuePressを採用しなかった理由

<a href="https://vuepress.vuejs.org/" target="_blank" class="outbound">VuePress</a>がメジャーバージョンになったということもあり、VuePressも試してみました。情報が少なくまだまだ使いづらいというのが正直なところで、VuePressを使うメリットが見当たりませんでした。
ブログではなく、ドキュメントなどのシステムとして使うならありかもしれません。
一応、サンプルコードをGitHubにあげているので興味のある方は参考にしてみてください。
* <a href="https://github.com/shoyan/vuepress-sample" target="_blank" class="outbound">vuepress-sample</a>

### 結論

特にこだわりがなくブログを楽に構築したいということであれば、Hexo一択ではないかと思います。最後にHexoを使うメリットについて説明します。

## Hexoを使うメリット

### ブログの基本機能が簡単に作成できる

Hexoでのブログ構築は5つのコマンドを実行するだけです。

```bash
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
$ hexo server
```

`hexo server` を実行後に`http://localhost:4000` にアクセスするとブログが表示されます。

### テーマの作成がしやすい

Hexoにはテーマ作成をサポートするツールが用意されています。
<a href="https://www.npmjs.com/package/generator-hexo-theme" target="_blank" class="outbound">generator-hexo-theme</a>を使えば簡単にブログテーマの雛形を用意することができます。
ブログテーマは自作したのですが、その雛形はgenerator-hexo-themeで作成しました。

### CI/CDも簡単

masterにマージしたら自動的にデプロイする設定を入れています。そのあたりも公式のマニュアルが用意してあり、手順にそって設定していくだけでCI/CD環境を作ることができます。私はGitHub Pagesを使っており、サーバー費用0円でブログを運営しています。コストパフォーマンスは最高ですね。

* <a href="https://hexo.io/docs/github-pages.html" target="_blank" class="outbound">GitHub Pages | Hexo</a>

## まとめ

Hexoは学習コストが低く、テーマ作成もわりと簡単にできるので楽にブログを作りたい人におすすめです。Node.js環境でブログを作りたいときは検討してみてはいかがでしょうか。
