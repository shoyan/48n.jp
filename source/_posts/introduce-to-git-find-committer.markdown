---
layout: post
title: "Gitのコミッターを集計するGitFindCommitterをつくった"
date: 2016-07-25 18:09:52 +0900
comments: true
categories: gem git
description: "Gitのコミッターを集計するGitFindCommitterをつくったので紹介します。GitFindCommitterとは変更されたファイルを対象としてコミッターを探すツールです。名前の通り、Gitのコミット履歴からコミッターを探します。"
---

Gitのコミッターを集計する[GitFindCommitter](https://github.com/shoyan/git_find_committer)をつくったので紹介します。

## GitFindCommitterとは

GitFindCommitterとは変更されたファイルを対象としてコミッターを探すツールです。
名前の通り、Gitのコミット履歴からコミッターを探します。
例えば、fixブランチでhoge.rbとfuga.rbを修正したとします。
GitFindCommitterはhoge.rbとfugue.rbを対象にコミッターを探します。

## なぜつくったのか

チーム内でコードレビューを行っているのですが、なかなかレビューしてもらえないということが時々あります。
コードレビューの際に詳しいコミッターをレコメンドすればスムーズにレビューしてもらえるのではと考えました。
レビュアーのレコメンド機能はまだ作成中なのですが、そのモジュールの1つとしてGemに機能を切り出しました。

## 使い方

以下のコマンドでインストールします。

```
$ gem install git_find_committer
```

searchメソッドにリポジトリとブランチを指定すると、関連するファイルのコミッターを探してきます。

```ruby
require 'git_find_committer'

GitFindCommitter.search(repo: 'balloonbros/sutekki', branch: 'add-ui')
=> [{:name=>"Shohei Yamasaki", :commit_count=>51}, {:name=>"keitakawamoto", :commit_count=>21}]
```

GitHub Enterpriseにも対応しており、GitHub Enterpriseで利用する場合は、以下の設定が必要です。

```
GitFindCommitter.configure do |c|
  c.url = "https://<hostname>"
  c.access_token = "<your 40 char token>"
end
```

## 便利なTips

集計対象のコミッターをフィルターすることができます。

```
GitFindCommitter.configure do |c|
  c.available_committer_names = %w(shoyan)
end
```

名前のみ配列で取得します。

```
GitFindCommitter.search(repo: 'balloonbros/sutekki', branch: 'add-ui’).names(1)
=> ["Shohei Yamasaki"]
```
