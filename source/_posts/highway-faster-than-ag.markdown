---
layout: post
title: "超高速grep「The Silver Searcher(ag)」からhighwayに乗り換えた"
date: 2015-10-28 13:51:49 +0900
comments: true
categories: 
---
プログラマーにとって，grepコマンドはなくてはならない存在です。  
基本的かつ古典的なユーティリティであるgrepですが，使いにくい面もあります。

2013年のはじめころから，grepに取って代わるコマンドとして「The Silver Searcher」（以下「ag」）が注目されはじめました。  
そして最近、[highway](https://github.com/tkengo/highway)というagライクで高速なgrepツールがリリースされました。

### The Silver Searcher（ag）とは？
highwayの紹介の前にagとagが開発された背景について説明したいと思います。  

プログラムを書いていると，ソースコード全域にわたって文字列を検索したい，ということはよくあります。  
そのようなときにgrepコマンドが活躍するわけですが，ソースコードのディレクトリには検索したくないファイルが往々としてあるわけです。  

たとえばバージョン管理システムが使っている「.git」ディレクトリは，検索対象に含めたくありません。  
これをgrepで実現しようとすると，オプションやパイプを組み合わせて，少々面倒なワンライナーを書く必要があります。

こういった問題を解決するため，ackというプログラムが作られました。  
ackは以下のような特徴を持つ，開発者向けの賢いgrepツールです。

* デフォルトでディレクトリツリーを再帰検索
* .gitなどを暗黙的に除外
* 検索するファイルタイプをオプションで指定可能（--perl，など）
* 拡張子だけでなく，shebangを見てファイルタイプを判別可能
* 検索結果をファイル単位でわかりやすくまとめて表示

ackはgrepと比べて使い勝手がよいのですが，Perlで実装されていることもあり，動作速度に難があります。  
その動作速度の問題を改善するためにagが開発されました。    

agは開発者が「A code searching tool similar to ack, with a focus on speed.」と紹介しているとおり，「高速なack」を目指して開発されたプログラムです。  
「ackに比べて3から5倍高速」を謳っており，そして実際非常に高速です。


### The Silver Searcher（ag）の問題点
紹介した通り、使い勝手がよく、性能もよいagなのですが、以下の問題点があります。  

* EUC-JPやShift_JISなどの日本語に使われるマルチバイト文字列が検索できない
* 検索結果の出力順が検索する度に異なる

全てのコードがUTF-8であれば問題ありませんが、日本語圏で使われるファイルでは、なかなかそうもいきません。  
検索結果の出力順が検索する度に異なるのも少し使いにくいと感じます。  

その点、highwayはEUC-JPやShift_JISをサポートしており、検索結果の表示順の問題もありません。  

### highwayのメリット
agの問題点であげた点をhighwayはクリアしています。  
また、速度もagよりも高速です。  

ベンチマークについては以下の記事をご覧ください。  
http://tkengo.github.io/blog/2015/10/19/release-highway/

### highwayのインストール

#### For OS X
homebrewでインストールできます。


```
$ brew tap tkengo/highway
$ brew install highway

```

#### For Fedora Core

```
$ sudo vi /etc/yum.repos.d/highway.repo
[repos.highway]
name=highway
baseurl=http://tkengo.github.io/highway/fedora
enabled=0
gpgcheck=0

$ sudo yum install highway --enablerepo="repos.highway"

```

### 使い方

```bash
# カレントディレクトリをhogeをいう文字列で再帰的に検索
$ hw hoge

# public_html以下のディレクトリからhogeという文字列を検索し、マッチした行から10行目までを表示
$ hw -A 10 hoge public_html

# オプションは以下のコマンドで参照できます
$ hw -h

```

### unite.vimと連携する
unite.vimのgrepにhighwayを使うようにします。


```
" grep検索
nnoremap <silent> ,g  :<C-u>Unite grep:. -buffer-name=search-buffer<CR>

" unite grepにhw(highway)を使う
if executable('hw')
  let g:unite_source_grep_command = 'hw'
  let g:unite_source_grep_default_opts = '--no-group --no-color'
  let g:unite_source_grep_recursive_opt = ''
endif

```

### おわりに
grepツールは完全に[highway](https://github.com/tkengo/highway)に乗り換えましたが、速度面、機能面に関しては問題ありません。  
まだgrepで頑張っている人はもちろん，agをすでに使っている人であっても，highwayの利用を検討する価値は充分にあると思います。
