---
layout: post
title: "毎日技術ブログを書いたらアクセスは増えるの？"
date: 2017-06-23 13:43:07
updated: 2017-06-23 13:43:07
comments: true
tags: 
  - ブログ
  - SEO
image: 2017-03-08-main.png
---

![2017-03-08-main](/images/2017-03-08-main.png)

こんにちは、SHOYANです。

SHOYAN BLOGでは平日毎日更新を目標としてWeb技術に特化した記事を書いていました。
その期間は2016年4月13日〜2016年12月31日です(後半はモチベーションが保てず、週1回程度の更新となっていましたが・・・)。

毎日更新することでブログのアクセス数はどうなったかということをまとめました。

## 結論

結論ファーストです。結論として毎日記事を公開しても、思ったよりもアクセス数は伸びませんでした。その理由とアクセスを増やすために有効であろう手法をまとめました。

## 100記事書いた後と前のアクセス数の比較

下のグラフは2017年1月1日〜2017年2月1日(青線)と1年前(オレンジ線)のセッションの比較です。

![2017-03-08-01](/images/2017-03-08-01.png)

記事数が増えたことによりセッションは171%増加しました。なるほど、記事を増やせばアクセスを増やすことができるのかと思われるはずです。しかし、この結果から記事数を増やせばアクセス数は増えると結論づけるのは時期尚早です。

## アクセス数の伸び悩み現象

下に表示しているのは直近1年のグラフです。グラフを見てもらえばわかる通り、アクセス数の伸び悩みが顕著です。

![2017-03-08-02](/images/2017-03-08-02.png)

記事数は増えているのになぜでしょうか。

### 検索されるキーワードで記事を書かなければアクセスは増えない

私のブログは検索エンジンとSNSからの流入がほとんどです。そのうち、アクセス数のベースとなっているのは検索エンジンからの流入です。

検索エンジンからの流入はみなさんが検索エンジンで検索して検索結果に表示され、そのリンクをクリックしていただかない限りは発生しません。ですので、検索結果の上位に表示されない、もしくは、そもそも検索されない記事を書いてもアクセスは増えません。

私のブログですが、118記事のうち32記事は流入がまったくありません。4分の1の記事にまったくアクセスがないという状態です。

次に私が実践から学んだSEOの知見を紹介します。

### 独自ドメインはキーワードに関係のある名前でないと効果がない

独自ドメインはSEOに有利と言われていますが、効果はありませんでした。最初はgithub.ioのサブドメイン、`shoyan.github.io`で運営していましたが、2016年11月29日に独自ドメイン `48n.jp` を設定しました。グラフを見ればわかる通り、アクセスは変わっていません。キーワードに関係のある独自ドメインでないと効果がないと思われます。

![2017-03-08-02](/images/2017-03-08-02.png)


### ドメインを変更しても適切にリダイレクトの設定を行えばアクセスは減少しない

2016年11月29日に独自ドメイン `48n.jp` を設定しました。以前のドメインにアクセスした場合は301リダイレクトするように設定していたのでアクセスの減少は見られませんでした。適切にリダイレクト設定を行うことでアクセスの減少を防ぐことができます。

### 有名なサイトのサブドメインかどうかは検索順位に関係ない

有名なサイトのサブドメインが有利という説があります。例えば私の使っていたgithub.ioドメインは世界中のIT技術者から利用されています。この事実からgithub.ioドメインはIT技術のSEOに強いといった考え方ができるでしょう。

しかし、私は関係ないと考えています。というのも、github.ioドメインとはいえ、いいサイトもしょぼいサイトもあります。中には不正をしているサイトもあることでしょう。

はたしてGoogleはそのようなサイトを全部ひっくるめて良いサイトと判断するでしょうか。テック系のサイトであろうという判断くらいはするかもしれません。しかし、あとはコンテンツ次第。あくまでもコンテンツでサイトは評価されるはずです。

実際にどこの馬の骨ともわからない(Googleからみたら)独自ドメインにしてみましたが、それによるアクセスの減少はありませんでした。

## 技術ブログでアクセスを集めるには

最後に技術ブログでアクセスを集めるために有効ではないかと考えていることを紹介します。

### キーワードを意識してコンテンツを作る

私はこの基本ができていなかったために誰からも人に読まれない記事をせっせと量産していました。

以下の2つは押さえておきたいポイントです。

* 検索されそうなキーワードを選定すること
* タイトルにキーワードを含めること

キーワードの検索ボリュームはGoogleが提供している[キーワードプランナー](https://adwords.google.co.jp/KeywordPlanner)で確認できます。また[Keyword Tool](http://keywordtool.io/)でどんなキーワードが検索されているかを確認することができます。

この2つのツールを使って検索されているキーワードを確認して、そのキーワードをタイトルに含めてください。

### 良質なコンテンツを作る

良質なコンテンツを作ることがアクセスを集める大前提であることに異論がある人は少ないでしょう。有益な情報を継続して発信することで読者が増えていくのは自然なことです。私は[Slack](https://slack.com/)のRSSインテグレーションを使って面白いと思ったブログを購読しています。

### 購読してもらえる仕組みを用意する

ブログを購読してもらえる仕組みを用意しましょう。はてなブログであれば購読機能がついていますし、RSSのリンクを設置することでRSSリーダーに登録してもらいやすくなります。ブログを作成するサービスやツールにはRSSの機能が備わっているので、それを使うとよいでしょう。

### SNSで拡散してもらいやすくする

良質なコンテンツを書いてバズるとアクセスが爆発的に増えます。技術系のブログだとはてなブックマークのホットエントリーに掲載されることで結構なアクセスを稼ぐことができます。

はてなブックマークに掲載された時のアクセスです。

![2017-03-08-03](/images/2017-03-08-03.png)

1日で私のブログの2ヶ月分くらいのアクセスを叩き出しています。また、外部リンクを得ることができるのでサイトの評価にも良い影響があると思われます。ちなみに2件ブクマしてもらえると、はてなブックマークの新着に掲載されるとのことです。セルフブクマ(自分自身でのブクマ)もカウントされるので、実質誰か1人にブクマしてもらえばいいわけです。

はてなブックマークとTwitterのSNSボタンは用意しておきましょう。Twitterカードも設定しておくとよいと思います。設定方法をまとめているので参考にしてください。

* [Twitterカードをサイトに設定する手順](/blog/2017/02/25/introduce-twitter-card/)

## まとめ

技術ブログを2年ほど書いてきましたが、単純に記事を書けばアクセスが増えるわけではないのでなかなか難しいですね。あまりアクセスにこだわることなく記事の内容にこだわったほうが良いように思っているこの頃です。とはいえ、この記事に書いていることはアクセスアップにそれなりの効果があるはずなので参考にしてみてください。
