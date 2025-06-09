---
layout: post
title: "SQLインジェクションの基礎と対策"
date: 2024-02-09 10:00:00
updated: 2024-02-09 10:00:00
category: 技術記事
tags:
  - セキュリティ
  - SQL
  - データベース
  - チュートリアル
description: "PHPアプリのSQLインジェクション対策として、mysql_real_escape_string() 等があるがこれだけでは万全ではないことがあるのでメモ。例えば以下のSQLではmysql_real_escape_string()を使っているが、脆弱性が存在する。"
---

PHPアプリのSQLインジェクション対策として、`mysql_real_escape_string()` 等があるがこれだけでは万全ではないことがあるのでメモ。

例えば以下のSQLでは`mysql_real_escape_string()`を使っているが、脆弱性が存在する。

```php
$id = mysql_real_escape_string("1 OR 1=1");
$sql = "SELECT * FROM table_name WHERE id = $id";
```

`mysql_real_escape_string()` では上記を防ぐことができない。
クエリ内の変数をシングルクォート（`'`）で囲むことで上記を防ぐことができる。

```php
$id = mysql_real_escape_string("1 OR 1=1");
$sql = "SELECT * FROM table_name WHERE id = '$id'";
```
