---
layout: post
title: "NginxのCookbookでハマった"
date: 2016-05-31 18:03:46 +0900
comments: true
categories: chef
description: "Nginxのcookbookでversionを指定しているのに、指定したバージョンでインストールされない問題でハマった。結果としてわかったことはパッケージでインストールする場合はバージョンが指定できない。バージョンを指定したい場合はソースでインストールする必要がある。"
---

Nginxのcookbookでversionを指定しているのに、指定したバージョンでインストールされない問題でハマった。

https://supermarket.chef.io/cookbooks/nginx

## 結果としてわかったこと

パッケージでインストールする場合はバージョンが指定できない。  
バージョンを指定したい場合はソースでインストールする必要がある。  

Nginx cookbookはパッケージでのインストールかソースでのインストールのいずれかを選ぶことができる。  
デフォルトはパッケージである。  
ソースでインストールしたい場合は、`node['nginx']['install_method’]='source'` とする必要がある。  
`install_method`を指定したときにはじめてversionの指定が可能となる。

## 立ちはだかる様々な問題
### versionが反映されない

`attributes/default.rb`にversionを定義してみたが、指定されたバージョンがインストールされない問題にハマった。  
デフォルトである、1.4.4がインストールされている。

#### 解決方法

rolesのoverride_attributesとして指定すると指定されたバージョンがインストールされた。  
基本的にvendor cookbookの値の定義はroleのoverride_attributeで指定したほうがいいのかもしれない。  
とはいえ、パラメーターによっては上書きされる場合もあってそのあたりの挙動がよくわかっていない。

### Checksumを指定しないといけない

sourceからインストールする場合、checksumを設定する必要がある。  
checksumは以下のようにして作成できる。


```
shasum -a 256 nginx-x.x.x.tar.gz

```

もしくは


```
curl http://nginx.org/download/nginx-x.x.x.tar.gz | shasum -a 256

```

checksumは `node['nginx']['source']['checksum’]` に指定する。

## ChefのAttributeの優先順位について

マニュアルによると、以下のようになっている(しかし、versionをoverrideで定義しても上書きできなかった)。

- A default attribute located in a cookbook attribute file
- A default attribute located in a recipe
- A default attribute located in an environment
- A default attribute located in role
- A force_default attribute located in a cookbook attribute file
- A force_default attribute located in a recipe
- A normal attribute located in a cookbook attribute file
- A normal attribute located in a recipe
- An override attribute located in a cookbook attribute file
- An override attribute located in a recipe
- An override attribute located in a role
- An override attribute located in an environment
- A force_override attribute located in a cookbook attribute file
- A force_override attribute located in a recipe
- An automatic attribute identified by Ohai at the start of the chef-client run

https://docs.chef.io/attributes.html#attribute-precedence

## Attributeの使い分けについて

以下のようにルール決めをしているという記事もあった。

> Attributeファイル=>文言変更  
> recipeファイル=>テスト的に変えたいものがある時のみ。本番では消す。  
> Environmentファイル=>development(開発）,alpha(実験用)、staging(テスト)、production（本番）でのIPやOSバージョンの違い。同じ構成が複数台ある時もここで区別。  
> Role=>ミドルウェアのバージョン違い

http://dev.classmethod.jp/server-side/chef/attribute-overrides-pattern/

## 参考リンク

- http://stackoverflow.com/questions/27366774/chefexceptionschecksummismatch-when-installing-nginx-1-7-8-from-source
