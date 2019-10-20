---
layout: post
title: "PMDでJavaのコードのバグをチェックする"
date: 2018-03-20 18:41:09 +0900
comments: true
categories: Java
---

Javaにおいてバグの要因となりそうなコードをチェックするツールとして、FindBugsがあります。しかし、FindBugsは2015年以降の開発が止まっているため、言語のアップデートに追従できていません。そこで、FindBugsの代替えとなる<a href="https://pmd.github.io/pmd-6.1.0/index.html" target="_blank">PMD</a>を紹介します。PMDを使えばバグの原因となりそうなコードを検知することができるため、不具合を未然に防ぐことが可能です。

## PMDとは

PMDは次の4つの問題があるコードを検知することができるツールです。

* Possible bugs - バグの要因となるコード
* Dead code - 使われていないコード
* Suboptimal code - 効率の悪いコード
* Overcomplicated expressions - 複雑な構文

PMD自体はJavaで実装されていますが、Java以外の言語にも対応しています。今回はJavaのコードを対象に使い方を紹介します。

## PMDのインストール方法

### Homebrewでインストールする

Macであれば、次のコマンドでインストールできます。

```
brew install pmd
```

### バイナリをダウンロードする

バイナリをダウンロードしてインストールすることができます。次のページからダウンロードしてください。

* <a href="https://github.com/pmd/pmd/releases" target="_blank">the github releases page</a>

## PMDでコードをチェックする

### サンプルコードの準備

PMDでコードをチェックしてみます。今回はサンプルコードを作って検証します。サンプルコードはGithubにアップロードしているのでクローンしてご利用ください。

```
git clone git@github.com:shoyan/pmd-samples.git
cd pad-samples
```

### PMDの実行

Pmdを実行します。`-d`がソースコードのパス、`-R`がルールが設定してあるファイルのパス、`-l`に言語を指定します。

```
pmd pmd -d src/main/java -R rules.xml -l java
3月 20, 2018 6:03:49 午後 net.sourceforge.pmd.cache.NoopAnalysisCache <init>
警告: This analysis could be faster, please consider using Incremental Analysis: https://pmd.github.io/pmd-6.1.0/pmd_userdocs_getting_started.html#incremental-analysis
/pmd-samples/src/main/java/UnusedCode.java:2:        Avoid unused private fields such as 'FOO'.
/pmd-samples/src/main/java/UnusedCode.java:4:        Avoid unused local variables such as 'i'.
/pmd-samples/src/main/java/UnusedCode.java:6:        Avoid unused private methods such as 'foo()'.
```

3つの問題が検知されています。UnusedCode.javaの2行目を見てみると、使われていないメンバ変数があります。他のエラーも同様に使われていないローカル変数とプライベートメソッドを検知しています。


### ルールについて

ルールの一覧については次のリンクよりご覧ください。
* <a href="https://pmd.github.io/pmd-6.1.0/pmd_rules_java.html" target="_blank">Java Rules</a>

0の状態からルールを構築するのは大変です。そのため、PMDのリポジトリには様々なルールのサンプルが定義されています。基本的なルールが網羅されているbasic.xmlを利用するとよいでしょう。他にも様々なルールがあるので参考にしてください。
* <a href="https://github.com/pmd/pmd/tree/master/pmd-java/src/main/resources/rulesets/java" target="_blank">rulesets java</a>

### カテゴリ

PMDのルールはカテゴリに分類されています。例えば、ベストプラクティスであれば、ベストプラクティスに沿ったルールが定義されています。カテゴリの詳細については次のリンクでご覧ください。

* <a href="https://github.com/pmd/pmd/tree/master/pmd-java/src/main/resources/category/java" target="_blank">category</a>


## トラブルシューティング

PMD6.1.0はPMD7への過渡期バージョンのようで、Deprecatedの警告が山のように出ます。
設定ファイルでしか警告を消す術がないので設定ファイルを修正します。

次のようにdeprecatedをfalseにするか、プロパティ自体を削除してください。

```
<rule ref="category/java/errorprone.xml/AvoidBranchingStatementAsLastInLoop" deprecated=“false" />
```

## 参考リンク

* <a href="https://pmd.github.io/pmd-6.1.0/index.html" target="_blank">PMD Introduction</a>

