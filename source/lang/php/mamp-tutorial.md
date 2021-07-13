---
title: 'MAMPチュートリアル'
date: 2021-07-08 11:47:55
updated: 2021-07-13 17:47:55
photos:
- /images/mamp/mamp-banner.png
description: この記事ではPHPの開発環境であるMAMPの使い方を説明します。MAMPを導入することでPHPの開発環境を簡単に準備することができます。
---

<img src="/images/mamp/mamp-banner.png" alt="MAMPバナー" title="MAMPバナー">

この記事ではPHPの開発環境であるMAMPの使い方を説明します。MAMPを導入することでPHPの開発環境を簡単に準備することができます。

本記事はMacを対象として解説しています。Windowsをお使いの方は、適宜Windows環境に置き換えて進めてください。

## MAMPとは

MAMPとはMacOSとWindowsで利用できる、開発環境です。Apache、Nginx、PHP、MySQLが同梱されており簡単にインストールすることができます。色々と知らない単語が出てきたかもしれませんが、ここでは単語1つ1つを理解しなくても大丈夫です。PHPの開発環境が簡単に準備できるツールだと理解すればOKです。

### 各単語の概要

- PHP: サーバサイドで動作するプログラミング言語
- Apache、Nginx: Webサーバと呼ばれるプログラムで、ブラウザからのリクエストを受けとり、対応するデータを返す
- MySQL: データベースと呼ばれるプログラムで、データを保存したり取り出したりすることができる

<p class="memo">
Apache、Nginx、MySQLなどのプログラムはソフトウェアと呼ばれます。
</p>

## MAMPのインストール

MAMPはインストーラーを使ってインストールを行います。まずは、インストーラーをダウンロードします。お使いのPCに対応したインストーラーをダウンロードしてください。

- <a href="https://www.mamp.info/en/downloads/" target="_blank" class="outbound" rel="noopener">MAMPのダウンロード</a>

クリックするとインストーラーがダウンロードされます。

<img src="/images/mamp/mamp01.png" alt="MAMPインストーラー" title="MAMPインストーラー">

### MAMPのインストール手順（Mac版）

Mac版の手順です。バージョンによって手順が変わる可能性がありますが、基本的には続けるをクリックしていけばインストールが完了するようになっています。

<img src="/images/mamp/mamp-install01.png" alt="MAMPインストール手順" title="MAMPインストール手順">
<img src="/images/mamp/mamp-install02.png" alt="MAMPインストール手順" title="MAMPインストール手順">
<img src="/images/mamp/mamp-install03.png" alt="MAMPインストール手順" title="MAMPインストール手順">
<img src="/images/mamp/mamp-install04.png" alt="MAMPインストール手順" title="MAMPインストール手順">

この画面が表示されればインストールは完了です。

<img src="/images/mamp/mamp-install05.png" alt="MAMPインストール手順" title="MAMPインストール手順">

## MAMPの起動(Mac版)

LaunchpadからMAMPアプリを起動します。MAMPとMAMP PROの2つがインストールされますが、MAMPの方を使います。

<img src="/images/mamp/mamp-app.png" alt="MAMPの起動" title="MAMPの起動">

MAMPアイコンをクリックするとコントロールパネルが表示されます。

<img src="/images/mamp/mamp-panel.png" alt="MAMPコントロールパネル" title="MAMPコントロールパネル">

## MAMPの設定（Mac版）

Mac版のMAMPは8888ポートでWebサーバ起動する設定になっています。開発がしやすいようにポートの設定を変更します。

MAMPコントロールパネル左上のPreferencesボタンをクリックすると設定パネルが開きます。

<img src="/images/mamp/mamp-preferences.png" alt="MAMP設定パネル" title="MAMP設定パネル">

1. 「Ports」タブをクリックします。
2. 「80 & 3306」をクリックします。

<img src="/images/mamp/mamp-preferences-default.png" alt="MAMP設定パネル" title="MAMP設定パネル">

すると、「Apache Port」と「Nginx Port」が80に、「MySQL Port」が3306に変更されます。OKボタンを押して変更を完了します。

<img src="/images/mamp/mamp-preferences-port.png" alt="MAMP設定パネル" title="MAMP設定パネル">

## MAMPコントロールパネルの操作

MAMPコントロールパネルの操作について説明します。

### Webサーバの起動

MAMPコントロールパネル右上のStartボタンをクリックするとWebサーバが起動します。

<img src="/images/mamp/mamp-start.png" alt="MAMPコントロールパネル" title="MAMPコントロールパネル">

Webサーバが起動するとMAMPコントロールパネル右上のStartボタンが緑色になります。

<img src="/images/mamp/mamp-started.png" alt="起動後" title="起動後">

### サーバーの停止

MAMPコントロールパネル右上のStopボタンをクリックするとWebサーバが停止します。

<img src="/images/mamp/mamp-started.png" alt="起動後" title="起動後">


## ドキュメントルート
Webサーバのセキュリティの仕組みとして、ドキュメントルートという仕組みがあります。ドキュメントルートは開発をしていく上でも理解しておく必要があるため、ここで説明しておきます。

ドキュメントルートとは外部に公開する場所です。ドキュメントルート配下に置かれたファイルのみがサーバの外部に公開されます。
ということは、ドキュメントルートに置かれたファイル以外は外部からアクセスができないということです。
インターネットは不特定多数のユーザーが利用しており、適切にアクセス制限を施す必要があります。そのための仕組みがドキュメントルートです。

### MAMPのドキュメントルート確認(Mac版)
MAMPにもドキュメントルートがあります。デフォルトは `/Application/MAMP/htdocs/` がドキュメントルートに設定されています。ドキュメントルートはMAMPのコントロールパネルで確認ができます。

<img src="/images/mamp/mamp-panel-document-root.png" alt="ドキュメントルート" title="ドキュメントルート">


### MAMPのドキュメントルートをFinderで開く

MAMPのコントロールパネルからドキュメントルートをFinderで開くことができます。

1. 左上のPreferencesをクリックして、設定画面を開く
2. Serverタブをクリックする
3. 「Open in Finder」をクリックする

<img src="/images/mamp/mamp-preferences-document-root.png" alt="ドキュメントルート設定" title="ドキュメントルート設定">

### MAMPのドキュメントルートを変更する

MAMPのドキュメントルートを変更することもできます。
「choose...」ボタンをクリックするとFinderが起動して、ドキュメントルートを変更することができます。ここでは、ドキュメントルートを変更することはしませんが、変更できるということを認識していただければと思います。

<img src="/images/mamp/mamp-preferences-document-root-change.png" alt="ドキュメントルート設定" title="ドキュメントルート設定">

## PHPファイルの実行

それでは、PHPファイルを実行してみましょう。まずは、PHPファイルを作っていきます。
`/Application/MAMP/htdocs/` ディレクトリに `first.php` を作ります。次のコードをコピーして`first.php`にペーストしてください。

```php
<?php
  echo "Hello World!";
?>
```

ブラウザで次のURLにアクセスしてみましょう。
`http://localhost/first.php`

次のように「Hello World!」と表示されればPHPが動作しています！

<img src="/images/mamp/hello-world.png" alt="実行結果" title="実行結果">

## まとめ

MAMPの設定とPHPの実行のチュートリアルは以上です。
MAMPは無料でお手軽にPHP開発環境を構築できるツールです。ぜひ、使ってみてください。
