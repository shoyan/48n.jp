---
layout: post
title: "テストカバレッジ100%を目指さない理由"
date: 2019-08-02 17:47:20 +0900
comments: true
categories: ソフトウェア開発
---
## 結論

テストカバレッジ100%は目指さない

## 理由

以前、このようなツイートをしました。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">マージ条件に作成したコードのテストカバレッジが100%であることというプロジェクトを見たことがあります</p>&mdash; しょーやん (@sinn_shoyan) <a href="https://twitter.com/sinn_shoyan/status/1155279098102472705?ref_src=twsrc%5Etfw">July 28, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

テストカバレッジ100%に違和感を感じない人はもう少し勉強した方がいいですね。

テストカバレッジ100%を目指すとこのようになってしまいます。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">テストカバレッジ100%を目指すとどうなるかを説明しましょう。
テストの目的がカバレッジを通すことになってしまい、意味のないテストが乱立します</p>&mdash; しょーやん (@sinn_shoyan) <a href="https://twitter.com/sinn_shoyan/status/1155279684218675201?ref_src=twsrc%5Etfw">July 28, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

さらにテストの問題も発生します。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">さらに、コードとテストコードが密結合になり、コードを少し変えるとテストが落ちるという状態になります。
リファクタリングする場合、テストコードも書き直しになります</p>&mdash; しょーやん (@sinn_shoyan) <a href="https://twitter.com/sinn_shoyan/status/1155280247924768772?ref_src=twsrc%5Etfw">July 28, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

テストカバレッジの問題については次の記事に詳しく書いてあります。

* <a herf="https://qiita.com/bremen/items/d02eb38e790b93f44728" target="_blank">テストカバレッジ100%を追求しても品質は高くならない理由と推奨されるカバレッジの目標値について</a>

全てのコードにコメントを書くのは馬鹿げていると感じる人は多いでしょう。全てのコードにテストを書くということも同じように考えるとどうでしょう。何事も過ぎたるは及ばざるが如しです。
