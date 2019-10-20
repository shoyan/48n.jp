---
layout: post
title: "Expressのインストールと構成を把握する"
date: 2016-05-17 13:33:55 +0900
comments: true
categories: node.js express
description: "Node.jsのフレームワークExpressを試してみました。ExpressはNode.jsでwebアプリケーションを作成するためのフレームワークです。Expressのインストール、express-generatorを使ったスケルトンアプリケーションの作り方を紹介します。"
---

Node.jsのフレームワーク、[Express](http://expressjs.com)を試してみました。  
ExpressはNode.jsでwebアプリケーションを作成するためのフレームワークです。

## インストール

まずは、Node.jsをインストールします。

以下のページからパッケージをダウンロードしてインストールします。  
https://nodejs.org/en/

次にnpmを使って[express-generator](http://expressjs.com/en/starter/generator.html)をインストールします。  
express-generatorはExpressのスケルトンアプリケーションを作成するコマンドです。  
npmはNode.jsをインストールした際にインストールされていると思います。


```
$ sudo npm install express-generator -g

```

## スケルトンアプリケーションを作成する

expressコマンドでスケルトンアプリケーションを作成します。


```
$ express myapp
$ cd myapp
$ npm install

```

起動してみます。


```
⇒  DEBUG=myapp:* npm start

> myapp@0.0.0 start /Users/PMAC025S/Development/sample/nodejs/myapp
> node ./bin/www

  myapp:server Listening on port 3000 +0ms
GET / 200 659.865 ms - 170
GET /stylesheets/style.css 200 9.088 ms - 111
GET /favicon.ico 404 70.846 ms - 1285

```

`http://localhost:3000` にアクセスしてみます。
Welcome to Express と表示されていれば正常に起動できています。

サーバーの停止は`Ctrl + C`です。

## ルーティング

ルーティングの基本的な構造です。


```
app.METHOD(PATH, HANDLER)

```

**app**: expressのインスタンス  
**METHOD**: HTTPメソッド(GET, POST, PUT, PATCH, DELETE等)  
**PATH**: サーバーのパス  
**HANDLER**: 実行する関数  

Hello World!を返すルーティングのサンプルです。  
`/`にGETリクエストを送ると、Hello World!が返却されます。


```javascript
app.get('/', function (req, res) {
  res.send('Hello World!');
});

```

`/`にPOSTリクエストを送ると、`Got a POST request`が返却されます。


```
app.post('/', function (req, res) {
  res.send('Got a POST request');
});

```

## Expressの構造

express-generator で作成されたファイルを確認していきます。  
ファイル構成は以下です。


```
⇒  tree -L 2
.
├── app.js
├── bin
│   └── www
├── node_modules
│   ├── body-parser
│   ├── cookie-parser
│   ├── debug
│   ├── express
│   ├── jade
│   ├── morgan
│   └── serve-favicon
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.jade
    ├── index.jade
    └── layout.jade

```

### app.js

app.jsではアプリの設定やルーティングを定義します。  
簡単なアプリであれば、ここに全て書いてしまってもよいでしょう。  
express-generatorで作成されたファイル構成では、`routes/`、`views/` ディレクトリが作成されており、ルーティングやテンプレートは分離する構成となっています。

### bin
実行ファイルが格納されます。

### node_modules
Expressなどのモジュールが入っているディレクトリです。npmでインストールしたファイルが格納されます。

### package.json
アプリの設定やメタ情報を定義するファイルです。

### public
公開ディレクトリです、cssファイル、imageファイル、JavaScriptファイルを格納します。

### routes
アプリケーションのルーティングと処理を定義したファイルを格納します。

### viewsディレクトリ
テンプレートを定義したファイルを格納します。

Expressの構成は把握できました。  
次はExpressを使ってチャットアプリケーションを作ってみたいと思います(次回へ続く)。

### 参考リンク

- [Installing Express](http://expressjs.com/en/starter/installing.html)
