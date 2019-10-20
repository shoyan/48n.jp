---
layout: post
title: "PHPコーディング規約とサポートするツール"
date: 2016-03-17 00:41:01 +0900
comments: true
categories: "PHP"
---

## この記事の内容
PHPのコーディング規約とそれをサポートするツールの紹介です。  
座学形式で説明していきます。

概要を知りたい場合は以下のスライドを参照ください。
<script async class="speakerdeck-embed" data-id="1d8ea4db4cd746b0857ab03889710555" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>

### はじまり、はじまり
みなさん、こんにちは。
PHPが大好きなプログラマことshoyanです。

今回、PHPのコーディング規約とそれをサポートするツールの紹介をしたいと思います。

### なぜ必要か
まずはなぜコーディング規約が必要なのでしょうか？

みんなに質問してみる  
しばらく沈黙

目のあった人に質問する  
なんか適当に答えてくれる

ありがとうございます。
正解です。
補足として、なぜコーディング規約が必要になったかを説明します。

そもそもコーディング規約は一人で開発する場合には必要ありません。
二人以上で開発を行う場合に必要となってきます。

たとえば、太郎くんとドナルドさんが二人で開発をしていたとします。
太郎くんがコードレビューを依頼しました。
30分後に確認するとコメントがついていました。

1行で書いていたif文のコードにドナルドさんからコメントがついていました。  
少し下にスクロールすると、if文のあとにスペースが必要だとコメントが入っていました。  
少し下にスクロールすると、else ifのスペースは不要だとコメントが。  
また更にその下には改行は不要とのコメントが...  
なんと全部で30個のコメントがついていたのです。  
30個のコメントの27個はコーディングスタイルに関するコメントでした。  

さて、このレビューは有益でしょうか。
お互いにとってあまり有益ではありませんね。

じゃあ、どうしましょう。

そこでコーディング規約です。
コーディング規約という共通のルールを作ることにより、お互いが納得できるコードを書く指標をつくります。

さて、コーディング規約に則ったコードを書くぞ！となったとしましょう。
では、PHPのコーディング規約を探してみましょう。
ところが、PHPの本家にはコーディング規約のドキュメントがありません。  

ということは、共通ルールがないということになります。
PHPの本家はコーディングルールなんて知ったこっちゃない。
開発者が勝手にやってくれ。ということです。

その要望に答えてPHPにはコーディング規約が乱立しています。
たとえば、以下のような感じです。

* Flow Framework
  - http://flowframework.readthedocs.org/en/stable/TheDefinitiveGuide/PartV/CodingGuideLines/PHP.html
* WordPress
  - https://make.wordpress.org/core/handbook/best-practices/coding-standards/php/
* ZendFramework
  - http://framework.zend.com/manual/1.12/en/coding-standard.coding-style.html
* PEAR
  - https://pear.php.net/manual/en/standards.php
* FuelPHP
  - http://fuelphp.com/docs/general/coding_standards.html

このようにフレームワークごとにコーディングルールが定められています。

1つのフレームワークであれば問題はありません。
しかし、2つのプロジェクトがあって、別々のフレームワークを使っていたらどうでしょう。
開発者はそれぞれのフレームワークの規約を切り替えながら開発をしないといけません。
これは考えるだけでも面倒くさそうですね。

### PSR
このような現状をなんとかしようと策定されたのがPSRです。  
PSRはPHP Standards Recommendations の頭文字をとったものです。  
日本語にするとPHP標準勧告です。  
PHP Framework Interop Groupが策定しています。  

### PSRのゴール
> We're a group of established PHP projects whose goal is to talk about commonalities between our projects and find ways we can work better together.

要するにフレームワークに依存しないルールを作って、どのプロジェクト（どのフレームワークを使っているプロジェクト）でも
同じようにコードを書くことをできるようにしましょうということです。(意訳)

ちなみにPSRには状態が色々あって、正式なものはACCEPTEDです。
ほかにも、DEPRECATED、REVIEW、DRAFTがあります。
以下のページで見ることができます。
http://www.php-fig.org/psr/

StatusがACCEPTEDなPSRは1〜7があります。
その中のPSR1とPSR2をここでは紹介します。
PSR1はBasic Coding Standardが定義してあります。
PSR2はCoding Style Guideで、PSR1を拡張したものです。
詳細は以下のページをご覧ください。

* http://www.php-fig.org/psr/psr-1/
* http://www.php-fig.org/psr/psr-2/

### PHP Code Snifferについて
PHPのコーディング規約をチェックするツールはいくつかありますが、ここではPHP_CodeSnifferを紹介します。

Snifferは嗅覚性探知機という意味です。

たとえば、捜索犬はsniffer dog 、ガス探知機はgas snifferと英語でいいます。
要するにPHP Code探索機です。

PHP Code Snifferにはツールが2つあります。
1つめはphpcsです。

phpcsはコーディングスタイルのチェックをするツールで、PHP Code Snifferを略したものです。
コーディングスタイルを指定することができます。
指定できるコーディングスタイルは、MySource, PEAR, PHPCS, PSR1, PSR2, Squiz and Zend です。
また、コーディングルールを自分で設定することもできます。

実行コマンド


```
phpcs --standard=PSR2 check_file or check_dir/

```

2つめはphpcbfです。

PHP Code Beautifier and Fixerを略したツールです。  
これは自動でコードを修正してくれるツールです。

実行コマンド


```
phpcbf --standard=PSR2 check_file or check_dir/

```

PHP Code Snifferのいいところは、チェックと修正のツールがわかれているところです。
それにより、CIによるチェックとその修正が簡単に行えます。

PHP Code Snifferの詳しい使い方はWikiを参照ください。  
https://github.com/squizlabs/PHP_CodeSniffer/wiki'

### コーディング規約の運用

コーディング規約を運用していくにあたっての問題点はなんだと思いますか？

しばらく沈黙

「コーディング規約が存在してもあまり守られない」ことです。

多分、みんな納得

では、どうすればコーディング規約を守れるようになるでしょうか。

CIでチェックするのが1番です。

コーディング規約に違反しているコードがあればエラーにします。
そうすればコーディング規約を違反しているコードがmasterにマージされることはありません。

コーディング規約の導入はプロジェクトの初期から始めるのがベストですが、
途中からでも自動修正ツールを使えば大半は修正することができます。
また、CIのプラットホームが最近は充実しているのでチェックの自動化も容易に行えます。


実際に導入した記事を書きましたので、こちらも参考にしてください。

- [修正した部分だけPHP構文チェックをする](/blog/2016/06/23/php-syntax-check/)

