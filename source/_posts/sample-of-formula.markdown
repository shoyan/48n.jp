---
layout: post
title: "LATEXチートシート - 数式記号の読み方・表し方"
date: 2016-07-12 14:45:29
updated: 2022-12-18 13:42:29
comments: true
tags: 数学
description: "LATEXを使った数式記号の読み方・表し方をまとめました。"
math: true
---

LATEXを使った数式記号の読み方・表し方をまとめました。
[数式記号の読み方・表し方](http://izumi-math.jp/sanae/report/suusiki/suusiki.htm)の数式を実際にLATEXで表示しています。

- 数学初心者の私が30歳で数学を学び直したときの経験を書きました。[30歳から始める数学](/blog/2015/12/01/mathematics-of-advent-calendar/)

## 1. 数と式

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ a \times b $$ | aかけるb | a \times b
$$ a \div b $$ | a割るb | a \div b
$$ a \pm b $$ | aプラスマイナスb | a \pm b
$$ a \times b $$ | aかけるb | a \times b
$$ a \div b $$ | a割るb | a \div b
$$ a \pm b $$ | aプラスマイナスb | a \pm b
$$ a \mp b $$ | aマイナスプラスb | a \mp b
$$ a \cdot b $$ | aかけるb | a \cdot b
$$ a < b $$ | a小なりb<br>aはbより小さい | a < b
$$ a > b $$ | a大なりb<br>aはbより大きい | a > b
$$ a \leqq b $$ | a小なりイコールb<br>aはb以下 | a \leqq b
$$ a \geqq b $$ | a大なりイコールb<br>aはb以上 | a \geqq b
$$ a \neq b $$ | aはbと等しくない<br>aノットイコールb | a \neq b
$$ a \fallingdotseq b $$ | aニアリーイコールb<br>aはbにほぼ等しい | a \fallingdotseq b
$$ a^n $$ | aのn乗 | a^n
$$ ( a^m ) ^n $$ | aのm乗のn乗 | ( a^m ) ^n
$$ \sqrt{a} $$ | ルートa<br>平方根a | \sqrt{a}
$$ \sqrt[n]{a} $$ | n乗根a | \sqrt[n]{a}
$$ \frac{b}{a} $$ | a分のb<br>b割るa | \frac{b}{a}
$$ \mid a \mid $$ | 絶対値a<br>aの絶対値 | \mid a \mid
$$ [x] $$ | xを越えない最大の整数<br>ガウスx | [x]
$$ a,b,c,\cdots $$ | a,b,c,… | a,b,c,\cdots


## 2. 関数・写像

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ y=f(x) $$ | yイコールf,x<br>yイコールf,かっこ,x,（かっこ） | y=f(x)
$$ f ^{-1} (x) $$ | f,インバースx<br>f,xの逆関数 | f ^{-1} (x)
$$ \sin x $$ | サインx | \sin x
$$ \cos x $$ | コサインx | \cos x
$$ \tan x $$ | タンジェントx | \tan x
$$ \sin ^2 x $$ | サイン2乗x | \sin ^2 x
$$ \log _a x $$ | ログa,b<br>aを底数とするbの対数 | \log _a x
$$ \log x $$ | ログ,x | \log x
$$ f \circ g $$ | fマルg<br>fとgの合成写像 | f \circ g
$$ f ^{-1} $$ | fインバース<br>fの逆写像 | f ^{-1}
$$ X \stackrel{f}{\to} Y $$ | XからYへの写像f<br>X矢印,Y,f | X \stackrel{f}{\to} Y
$$ a \stackrel{f}{\to} b $$ | aをbに移す写像f<br>a矢印,b,f | a \stackrel{f}{\to} b
$$ f: x \to y $$ | xからyへの写像f<br>f,x矢印,y | f: x \to y
$$ f(x,y) $$ | f,x,y<br>f,かっこ,x,y,（かっこ） | f(x,y)

## 3. ベクトル・行列

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ \vec{a} $$ | ベクトルa<br>aベクトル | \vec{a}
$$ \overrightarrow{AB} $$ | ベクトルAB<br>ABベクトル | \overrightarrow{AB}
$$ \mid \vec{a} \mid $$ | ベクトルaの大きさ<br>ベクトルaの絶対値 | \mid \vec{a} \mid
$$ \vec{0} $$ | 零ベクトル<br>ゼロベクトル | \vec{0}
$$ \vec{a} \neq \vec{b} $$ | ベクトルaはベクトルbではない | \vec{a} \neq \vec{b}
$$ \vec{a} \parallel \vec{b} $$ | ベクトルa,bは平行<br>ベクトルa平行ベクトルb | \vec{a} \parallel \vec{b}
$$ \vec{a} \perp \vec{b} $$ | ベクトルa,bは垂直<br>ベクトルa垂直ベクトルb | \vec{a} \perp \vec{b}
$$ \vec{a}=(a_1,a_2) $$ | ベクトルaイコールa1,a2<br>ベクトルaイコール,かっこa1,a2 | \vec{a}=(a_1,a_2)
$$ \vec{a} \cdot \vec{b} $$ | ベクトルa,bの内積 | \vec{a} \cdot \vec{b}
$$ ( a \quad b ) $$ | 行ベクトルa,b<br>かっこ,a,b, | ( a \quad b )
$$ \begin{pmatrix} a \\ b \end{pmatrix} $$ | 列ベクトルa,b<br>かっこ,a,b, | \begin{pmatrix} a \\ b \end{pmatrix}
$$ m \times n $$ | m,n行列<br>mかけるn行列 | m \times n
$$ \begin{pmatrix} a & b \\ c & d \end{pmatrix} $$ | 行列a,b,c,d<br>かっこ,a,b,c,d | \begin{pmatrix} a & b \\ c & d \end{pmatrix}
$$ A^2 $$ | Aの2乗 | A^2
$$ A^{-1} $$ | Aの逆行列<br>Aインバース | A^{-1}
$$ A \vec{x} $$ | Aベクトルx | A \vec{x}
$$ O $$ | 零行列 | O

## 4. 微分・積分

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ \{ a_n \} $$ | 数列an | \{ a_n \}
$$ \sum _{k=1} ^{n} {k(k+1)} $$ | シグマ,ak,k=1からnまで<br>シグマ,k=1からnまで,ak | \sum _{k=1} ^{n} {k(k+1)}
$$ n \to \infty $$ | n矢印無限大<br>n無限大 | n \to \infty
$$ \lim {n \to \infty} a_n=\alpha $$ | nが限りなく大きくなるときのanの極限値はα<br> リミット,n→∞,an,イコールα | \lim {n \to \infty} a_n=\alpha
$$ x \to a $$ | x矢印a<br>xが限りなくaに近づく | x \to a
$$ \lim {x \to a} f(x)=b $$ | xが限りなくaに近づくとき,f(x)の極限値はbである<br>リミット,xがaに近づくときのf(x),イコール,b<br>リミット,x矢印a,f(x),イコールb | \lim {x \to a} f(x)=b
$$ \lim {x \to a+0} f(x) $$ | xがaに近づくときのf(x)の右極限値<br>リミット,xが大きい方からaに近づくときのf(x)<br>リミット,x矢印a+0,f(x) | \lim {x \to a+0} f(x)
$$ \lim {x \to a-0} f(x) $$ | xがaに近づくときのf(x)の左極限値<br>リミット,xが小さい方からaに近づくときのf(x)<br>リミット,x矢印a-0,f(x) | \lim {x \to a-0} f(x)
$$ \Delta x \to 0 $$ | デルタx矢印0<br>デルタxが限りなく0に近づく | \Delta x \to 0
$$ f'(x) $$ | f,ダッシュ,x | f'(x)
$$ y' $$ | y,ダッシュ | y'
$$ \frac{dy}{dx} $$ | dy,dx | \frac{dy}{dx}
$$ \frac{d}{dx} f(x) $$ | d,dx,f(x) | \frac{d}{dx} f(x)
$$ \frac{d}{dx} f(x) $$ | d,dx,f(x) | \frac{d}{dx} f(x)
$$ ( a,b ) $$ | 開区間a,b | ( a,b )
$$ [ a,b ] $$ | 閉区間a,b | [ a,b ]
$$ f"(x) $$ | f,トゥーダッシュ,x | f"(x)
$$ y" $$ | y,トゥーダッシュ | y"
$$ \frac{d^2y}{dx^2} $$ | d,トゥー,y,d,x,トゥー yの第2次導関数 | \frac{d^2y}{dx^2}
$$ \frac{d^y}{dx^2} f(x) $$ | f(x) d,トゥー,d,x,トゥー,f(x)<br>f(x)の第2次導関数 | \frac{d^y}{dx^2} f(x)
$$ y^{(n)} $$ | yの第n次導関数 | y^{(n)}
$$ f^{(n)(x)} $$ | f(x)の第n次導関数 | f^{(n)(x)}
$$ \frac{d^ny}{dx^n} $$ | d,n,d,x,n,f(x)<br>yの第n次導関数 | \frac{d^ny}{dx^n}
$$ \frac{d^n}{dx^n} f(x) $$ | d,n,d,x,n,f(x)<br>f(x)の第n次導関数 | \frac{d^n}{dx^n} f(x)
$$ \int _a ^b f(x) dx $$ | インテグラル,aからbまで,f(x),dx | \int _a ^b f(x) dx
$$ [ F(x) ] ^b _a $$ | F(x),a,b | [ F(x) ] ^b _a

## 5. 集合・理論

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ A \subset B $$ | AはBの真部分集合である | A \subset B
$$ A \supset B $$ | AはBを真部分集合に持つ | A \supset B
$$ A \subseteqq B $$ | A含まれるB<br>AはBの部分集合である<br>AはBに含まれる | A \subseteqq B
$$ A \supseteqq B $$ | A含むB<br>AはBを含む<br>BはAを部分集合に持つ | A \supseteqq B
$$ a \in A $$ | aはAの要素である<br>aはAに属する<br>a属するA | a \in A
$$ a \notin A $$ | aはAの要素でない<br>aはAに属さない<br>a属さないA | a \notin A
$$ A \ni a $$ | aを要素とする<br>Aの要素 | A \ni a
$$ \{ 1,2,3,4 \} $$ | 集合1,2,3,4<br>1,2,3,4を要素とする集合 | \{ 1,2,3,4 \}
$$ \{ x \mid x<6 \} $$ | x（の集合）ただしx<6<br>x<6を満たす集合 | \{ x \mid x<6 \}
$$ A \cap B $$ | AキャップB<br>A 交わり<br>AとBの交わり（共通部分）<br>AインターセクションB | A \cap B
$$ A \cup B $$ | A カップ B<br>A結びB<br>AとBの結び<br>AユニオンB | A \cup B
$$ A=B $$ | AイコールB<br>AはBに等しい | A=B
$$ \bar{A} $$ | Aバー<br>Aの補集合 | \bar{A}
$$ \phi $$ | 空集合<br>ファイ | \phi
$$ P \Rightarrow Q $$ | PならばQ | P \Rightarrow Q
$$ P \Leftrightarrow Q $$ | PとQは同値 | P \Leftrightarrow Q
$$ \bar{P} $$ | Pでない<br>Pの否定<br>Pバー | \bar{P}

## 6. 確率・統計

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ _n P _r $$ | n,P,r<br>Pのn,r<br>パーミュテーション,n,r | _n P _r
$$ _n C _r $$ | n,C,r<br>Cのn,r<br>コンビネーション,n,r | _n C _r
$$ n! $$ | nの階乗<br>nファクトリアル | n!
$$ n(A) $$ | n,A<br>n,かっこ,A,（かっこ） | n(A)
$$ P(A) $$ | P,A<br>事象Aの確率 | P(A)
$$ P _A (B) $$ | P,A,B<br>PのA,B<br>P,A,かっこ,B,（かっこ） | P _A (B)
$$ \bar{x} $$ | xバー<br>xの平均 | \bar{x}
$$ E(X) $$ | E,X<br>Xの平均 | E(X)
$$ V(X) $$ | V,X<br>Xの分散 | V(X)
$$ \sigma (X) $$ | シグマ,X<br>Xの標準偏差 | \sigma (X)
$$ P(X=A) $$ | P,かっこ,X=a,（かっこ）<br>X=aとなる確率 | P(X=A)
$$ B(n,p) $$ | B,n,p | B(n,p)
$$ N(m,\sigma ^2) $$ | N,m,σ2 | N(m,\sigma ^2)

## 7. 幾何

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ x \circ $$ | x度 | x \circ
$$ \angle A $$ | 角A | \angle A
$$ \triangle {ABC} $$ | 三角形ABC | \triangle {ABC}
$$ l \parallel m $$ | l平行m<br>lとmは平行 | l \parallel m
$$ l \nparallel m $$ | lとmは平行でない | l \nparallel m
$$ \triangle {ABC} \equiv \triangle {DEF} $$ | △ABCと△DEF合同<br> △ABC合同△DEF | \triangle {ABC} \equiv \triangle {DEF}
$$ \overline{AB} $$ | ABの長さ<br> ABのバー | \overline{AB}

## 8. ギリシャ文字

記号 | 読み方 | LATEX表記
--- | --- | ---
$$ \alpha $$ | アルファ | \alpha
$$ \beta $$ | ベータ | \beta
$$ \gamma $$ | ガンマ | \gamma
$$ \theta $$ | シータ | \theta
$$ \pi $$ | パイ | \pi
$$ \Delta $$ | デルタ | \Delta

## 関連記事
- 数学初心者の私が30歳で数学を学び直したときの経験を書きました。[30歳から始める数学](/blog/2015/12/01/mathematics-of-advent-calendar/)

- 数学初心者の私が数学の勉強会で登壇することに！
  - [プログラマのための数学勉強会@福岡に登壇してきました](/blog/2015/09/08/mathematics-for-programmer/)
  - [プログラマのための数学勉強会@福岡#2に登壇してきました](/blog/2015/11/11/mathematics-for-programmer-second/)

## 参考リンク
* [数式記号の読み方・表し方](http://izumi-math.jp/sanae/report/suusiki/suusiki.htm)


<script type="text/javascript" src="//cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
