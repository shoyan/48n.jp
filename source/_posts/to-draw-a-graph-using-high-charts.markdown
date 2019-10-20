---
layout: post
title: "グラフを描画するHighChartsで平均気温のグラフを描画する"
date: 2016-05-31 13:58:42 +0900
comments: true
categories: javascript
description: "グラフを描画するHighChartsを紹介します。 HighChartsはJavaScriptのグラフ描画ライブラリです。福岡市の平均気温をグラフで表示するデモを作成しました。"
---

グラフを描画する[HighCharts](http://www.highcharts.com/)を紹介します。  
HighChartsはJavaScriptのグラフ描画ライブラリです。  
HighChartsを使えば簡単にグラフの描画ができます。

## デモページ

福岡市の平均気温をグラフで表示してみました。

http://codepen.io/shoyan/details/jrOjWK/

## サンプルを作ってみる

簡単なサンプルを作ってみましょう。  
私がサンプルとして利用していた[CODEPEN](http://codepen.io/)を利用すると簡単に作成できるのでオススメです。

HighChartsを使うには、jQueryとhighCharts.jsが必要です。


```
<script src="http://code.highcharts.com/highcharts.js"></script>
<script src="https://code.jquery.com/jquery-2.2.4.min.js"></script>

```

index.htmlのbody部は以下を定義します。
div要素にグラフが描画されます。

index.html


```
<div id="container" style="width:100%; height:400px;"></div>

```

JavaScript tag `<script> </script>` に書くか、外部ファイルに定義してください。


```
$(function () {
    $('#container').highcharts({
        chart: {
            type: 'bar'
        },
        title: {
            text: 'Fruit Consumption'
        },
        xAxis: {
            categories: ['Apples', 'Bananas', 'Oranges']
        },
        yAxis: {
            title: {
                text: 'Fruit eaten'
            }
        },
        series: [{
            name: 'Jane',
            data: [1, 0, 4]
        }, {
            name: 'John',
            data: [5, 7, 3]
        }]
    });
});

```

あとは、ブラウザでアクセスすればグラフが描画されます。

## 構文

基本的な構文は以下です。


```
$("グラフを描画するhtml要素").highcharts(option)

```

### HighChartsの基本的なパラメーター(option)

HighChartsの基本的なパラメーター(option)を紹介します。

- **TITLE**: チャートのタイトル
- **SERIES**: 描画するデータの値
- **TOOLTIP**: チャートにマウスオーバーしたときに表示されるツールチップの設定
- **LEGEND**: SERIESの説明
- **AXES**: 縦軸と横軸の説明を設定します。

### グラフの種類について

typeにグラフの種類を設定できます。

* 棒グラフ: column
* 折れ線グラフ: line
* 円グラフ: pie
* 帯グラフ: bar
* ヒストグラム: column
* 散布図: scatter
* 箱ひげ図: boxplot
* 三角グラフ: pyramid

様々なグラフのデモページが用意されており、参考になります。

http://www.highcharts.com/demo
