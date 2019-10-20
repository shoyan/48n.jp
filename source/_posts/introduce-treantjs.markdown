---
layout: post
title: "Treant.jsでツリーダイアグラムを描画する"
date: 2016-06-21 13:26:40 +0900
comments: true
categories: JavaScript
description: "Treant.jsとはツリーダイアグラムを描画するためのJavaScriptライブラリです。Treant.jsの使い方をチュートリアル形式で紹介します。"
---

Treant.jsとはツリーダイアグラムを描画するためのJavaScriptライブラリです。  
[Treant.js](http://fperucic.github.io/treant-js/)でツリーダイアグラムを描画してみました。

http://codepen.io/shoyan/details/MeaqzN/

## 簡単なサンプルを作ってみる

まずは簡単なサンプルを作ってみましょう。

http://codepen.io/shoyan/pen/qNOMoN

[codepen.io](http://codepen.io/)を使うと簡単にサンプルが作成できるので便利です。

Treant.jsを利用するには、いくつか必要なモジュールがあります。

#### JS

- http://fperucic.github.io/treant-js/Treant.js
- http://fperucic.github.io/treant-js/vendor/raphael.js

#### CSS

- http://fperucic.github.io/treant-js/Treant.css

### BaseとなるHTMLのテンプレート


```html
<!doctype html>
<html lang="ja">
<head>
    <meta charset="utf-8"/>
    <title></title>

    <!-- stylesheets -->
    <link rel="stylesheet" href="http://fperucic.github.io/treant-js/Treant.js" type="text/css"/>
     <style>
         body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,fieldset,input,textarea,p,blockquote,th,td { margin:0; padding:0; }
         body { background: #fff; }
         /* optional Container STYLES */
         .chart { height: 159px; width: 332px; margin: 5px; margin: 5px auto; border: 3px solid #DDD; border-radius: 3px; }
         .node { color: #9CB5ED; border: 2px solid #C8C8C8; border-radius: 3px; }
         .node p { font-size: 20px; line-height: 20px; height: 20px; font-weight: bold; padding: 3px; margin: 0; }
    </style>
</head>
<body>
    <div id="tree-simple" style="width:335px; height: 160px" </div>

    <!-- javascript -->
    <script src="http://fperucic.github.io/treant-js/vendor/raphael.js"></script>
    <script src="http://fperucic.github.io/treant-js/Treant.js"></script>
</body>
</html>

```

### JS

JavaScriptファイルです。設定をJSONで定義して、コンストラクタに渡しています。


```js
simple_chart_config = {
    # chartの設定をします
    chart: {
        container: "#tree-simple"
    },
    # ノードを定義します
    nodeStructure: {
        text: { name: "Parent node" },
        children: [
            {
                text: { name: "First child" }
            },
            {
                text: { name: "Second child" }
            }
        ]
    }
};
# コンストラクタ
var my_chart = new Treant(simple_chart_config);

```

基本的には、nodeStructureに必要なノードを定義して、CSSで見た目を調整するという感じです。  
アニメーションにも対応していて、その場合はjQueryが必要だったりするようです。  
サンプルも色々あるので参考にしてみるとよいと思います。  
http://fperucic.github.io/treant-js/
