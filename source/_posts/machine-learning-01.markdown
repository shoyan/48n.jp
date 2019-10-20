---
layout: post
title: "機械学習ことはじめ"
date: 2016-11-11 14:13:32 +0900
comments: true
categories: 機械学習
description: "機械学習を学び始めたエンジニアの記録です。"
---

機械学習をやるぞ！と息巻いてから半年が経とうとしている。
進捗はてんでダメ。ほとんど勉強せずに時間だけが過ぎてしまった。
さすがにこれではいけないと学習を始めた。

とりあえず学ぶにも何かしらの教材がないと効率が悪いと思い、基礎的な理論が勉強できそうな 「機械学習理論入門」を買った(半年前に)。
機械学習の理論を優しく書いてあるはずなのだが、高校数学の理解もままならない自分の数学力では理解に苦しみ、本棚の肥やしとなっていた。
この段階では機械学習にも色々種類があるんだなということがわかった。

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4774176982&linkId=f54a583abd60635f4ad56b5e2705933b"></iframe>

次に機械学習理論の入門としてわかりやすそうなやる夫シリーズを読みだした。

- [やる夫で学ぶ機械学習 - 序章 -](http://tkengo.github.io/blog/2016/01/03/yaruo-machine-learning1/)
- [やる夫で学ぶ機械学習 - 単回帰問題 -](http://tkengo.github.io/blog/2016/01/04/yaruo-machine-learning2/)

まずは最小二乗法についての話が出てきた。
これはそれとなく理解できたので、最小二乗法を使って誤差を求めるプログラムをRubyで実装してみたりもした。

- https://github.com/shoyan/machine_learning/blob/master/01.rb

しかし、最急降下法でつまづいてしまった。
最急降下法に出てくる合成関数の微分がよく分からない

そもそも合成関数ってなんだというところで、合成関数について勉強した。
合成関数は関数の引数が関数になっている関数だった。

関数: y=g(x)
合成関数: y=g(f(x)) ←こういう感じで引数が関数となっている

やる夫シリーズを理解するには微分の知識が必要だということがわかったので、微分について勉強するために数学ガールを読んでいる。微分について会話形式で学んでいけるので、なかなかわかりやすい。

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4797382317&linkId=d26bd243cf2a37dd3f63507bea574228"></iframe>

というところで、現状は微分を学んでいる。

だいぶ遠回りしている感がある。
道のりは険しい。
