---
layout: post
title: "Jekyllで数式を表示する方法"
date: 2016-06-27 12:25:08
updated: 2016-06-27 12:25:08
comments: true
tags: 
description: "Jekyllで数式を使いたい場合は、markdownにkramdownを使うのがおすすめです。次にMathjax.jsを読み込みます。以上で準備が整ったので、LaTexの書式で数式を表現できます。"
---
Jekyllで数式を使いたい場合は、markdownにkramdownを使うのがおすすめです。  
というのも、`redcarpet` はワンライナーの書式しか使えません。  
`rdiscount` は自分が試したところ、動作しませんでした。

次にMathjax.jsを読み込みます。

```
<script type="text/javascript" src="//cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

これで準備は整いました。

Kramdown のドキュメントに書かれているサンプルを表示します。

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

```
$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$
```

基本的な書式は以下のようになります。

```
$$
\begin{align*}

LaTexの数式

\end{align*}
$$
```

また`$$`を使ってワンライナーで書くことも可能です。

$$ 5 + 5 $$

```
$$ 5 + 5 $$
```

インラインにしたいときは`\$$`を使います。

\$$ 5 + 5 $$

```
\$$ 5 + 5 $$
```

このように文字中に数式を埋め込むことができます。

光は真空中を1秒間に約 $$ 3.0 × 10^8  $$メートル進む。 光速を $$ cc $$ で表す

```
光は真空中を1秒間に約 $$ 3.0 × 10^8  $$メートル進む。 光速を $$ cc $$ で表す
```

表記の確認には[MathJax checker](http://gyafun.jp/ln/MathJax.html) を使うと便利です。
LaTeX 書式の数式をリアルタイムで確認することができます。

LaTexの書式に関しては以下を参考にしてください。

[http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm](http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm)

参考リンク

- [Syntax kramdown](http://kramdown.gettalong.org/syntax.html#math-blocks)
- [Extras - Jekyll • Simple, blog-aware, static sites](http://jekyllrb.com/docs/extras/)
- [Jekyll 上での数式の表示](http://sekika.github.io/2015/10/10/equation-on-jekyll/)
- [Webの数式表現](http://www.ic.daito.ac.jp/~mizutani/html/mathexpress.html)

<script type="text/javascript" src="//cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
