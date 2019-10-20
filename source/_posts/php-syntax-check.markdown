---
layout: post
title: "レガシーコード向けに修正した部分だけPHP構文チェックをする仕組みを作った"
date: 2016-06-23 17:14:34 +0900
comments: true
categories: php
description: "修正したファイルのみPHPの構文チェックを実行する仕組みを作ってみました。構文に問題があれば、PRにコメントでその箇所に通知されます。導入の方法をサンプルコードつきで紹介します。"
---

修正した部分だけPHPの構文チェックをする仕組みを作ってみました。  
構文に問題があればGithubのPull Requestにコメントされます。  

![2016-06-23_php-syntax-check](/images/2016-06-23_php-syntax-check.png)

この方法のよいところは既存のソースを変えることなくPHPの構文チェックの仕組みを導入できることです。  
規約に沿っていないコードが大量にあり、かつテストコードがないような環境(レガシー環境)にも導入することができます。

使用したツールは以下です。

- [packsaddle](https://github.com/packsaddle)
- [PHP_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
- [CircleCI](https://circleci.com/)
- [Github](https://github.com/)

また、packsaddleのツールはRuby製ですので、Rubyが動作する環境が必要です。

サンプルとしてGithubにphp-syntax-checkというリポジトリを作成しているので参考にしてください。

- https://github.com/shoyan/php-syntax-check

以下のスクリプトをCircleCIで実行しています。

check_syntax.sh


```bash
#!/bin/bash

echo "Start"
LIST=`git diff --name-only origin/master | grep -e '.php$'`

if [ -z "$LIST" ]; then
    echo "PHP file not changed."
    exit 0
fi

if [ -n "$CI_PULL_REQUEST" ]; then
    git diff --name-only origin/master \
        | grep -e '.php$' \
        | xargs vendor/bin/phpcs -n --standard=PSR2 --report=checkstyle \
        | bundle exec checkstyle_filter-git diff origin/master \
        | bundle exec saddler report \
        --require saddler/reporter/github \
        --reporter Saddler::Reporter::Github::PullRequestReviewComment
fi

```

## 仕組み

以下の3つのセクションに分類されます。

- 対象のファイルを抽出
- 構文チェック
- 結果をレポート

### 1. 対象のファイルを抽出

以下のコマンドでmasterと差分のあるファイル名を取得します。


```
git diff --name-only origin/master

```

さらに grepで拡張子が .phpのファイルのみ対象にします。


```
git diff --name-only origin/master | grep -e ‘.php$'

```

### 2. 構文チェック

構文チェックツールは好きなものを使えます(CheckStyleフォーマットで出力できるものに限りますが)。  
今回は[PHP_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)を使いました。


```
xargs vendor/bin/phpcs -n --standard=PSR2 --report=checkstyle

```

`xargs`は前のコマンドを引数でとるために必要です。

以下のコマンドでmasterとブランチの差分をチェックして、差分があるところのエラーを検知対象のエラーとしています。


```
bundle exec checkstyle_filter-git diff origin/master

```

エラーの差分の抽出に[checkstyle_filter-git](https://github.com/packsaddle/ruby-checkstyle_filter-git)というツールを使っています。
これは、入力として渡したCheckStyle formatの文字列から、変更した内容の部分のエラーを抽出するツールです。
例えばファイルの10行目〜15行目を変更した場合、その行で発生したエラーのみを抽出します。

### 3. 結果をレポート

saddlerを使って結果をGithubに通知します。


```
bundle exec saddler report \
        --require saddler/reporter/github \
        --reporter Saddler::Reporter::Github::PullRequestReviewComment

```

注意点としては、PRを作っていないと通知でエラーとなります。

ですので、Pull Requestがあるかどうかをチェックするif文をいれています。
`$CI_PULL_REQUEST` はCIrcleCIの変数で、Pull Requestが作られていればこの変数にURLが格納されています。


```
if [ -n "$CI_PULL_REQUESTS" ]; then

fi

```

また、通知には`GITHUB_ACCESS_TOKEN`を発行して環境変数に登録しておく必要があります。

- https://help.github.com/articles/creating-an-access-token-for-command-line-use/

CircleCIの環境変数の設定については、以下を参照ください。

- https://circleci.com/docs/environment-variables/#custom

また、通知ではなく単に結果を出力する場合は、[saddler/reporter/text](https://github.com/packsaddle/ruby-saddler-reporter-text)を指定します。


```
bundle exec saddler report \
  --require saddler/reporter/text \
  --reporter Saddler::Reporter::Text

```

以下は、出力があった場合はエラーにする例です。


```
RESULT=`git diff --name-only origin/master \
    | grep -e '.php$' \
    | xargs vendor/bin/phpcs -n --standard=custom_ruleset.xml --report=checkstyle \
    | bundle exec checkstyle_filter-git diff origin/master \
    | bundle exec saddler report \
      --require saddler/reporter/text \
      --reporter Saddler::Reporter::Text`

if [ -n "$RESULT" ]; then
    echo ""
    echo "An error has been detected,this following:"
    echo "$RESULT"
    exit 1
fi

```

## その他

<del>ファイルのエンコードがEUC-JPのソースコードに適用したところエラーとなりました。  
リポジトリをフォークして対応しました。</del>
1.1.0より使えるようになりました！

- https://github.com/shoyan/ruby-checkstyle_filter-git/commit/a11c3c7f4daf1ddf26c0ab4f4ae9d8b1b78bda4b

以下のように設定します。

Gemfile

```
gem "checkstyle_filter-git", git: "https://github.com/shoyan/ruby-checkstyle_filter-git.git", branch: "implement-exec"

```

iconvでgit diffの出力結果の文字コードを`EUC-JP`->`UTF-8`に変換して渡せるようにしました。


```
git diff --name-only origin/master \
  | grep -e '.php$' \
  | xargs phpcs -n --standard=custom_ruleset.xml --report=checkstyle \
  | bundle exec checkstyle_filter-git exec "git diff origin/master | iconv -f EUCJP -t UTF8"

```

<del>PRもしているので直ることに期待です。</del>
Mergeいただきました。

- [https://github.com/packsaddle/ruby-checkstyle_filter-git/pull/11](https://github.com/packsaddle/ruby-checkstyle_filter-git/pull/11)

サンプルとしてGithubにphp-syntax-checkというリポジトリを作成しているので参考にしてください。

- [https://github.com/shoyan/php-syntax-check](https://github.com/shoyan/php-syntax-check)

## 関連記事

- [PHPコーディング規約とサポートするツール](/blog/2016/03/17/php-coding-rule/)

## 参考リンク

- [変更したファイルにrubocopやjscsを実行して pull requestに自動でコメントする](http://packsaddle.org/articles/saddler-overview/)
- [規約に沿ってないPHPコードを駆逐する](http://qiita.com/noboru_i/items/23827b655ac854ba04b2)
