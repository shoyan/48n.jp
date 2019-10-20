---
layout: post
title: "php-idnkitをインストールする"
date: 2016-09-09 10:53:43 +0900
comments: true
categories: php
description: "php-idnkitをインストールしたのでメモ。OSはubuntu、PHPのバージョンは5.6.24で行った。php-idnkitは拡張モジュールなのでPHPをコンパイルし直す必要はない。モジュールをコンパイルして、php.iniのextensionに指定すれば使えるようになる。"
---

php-idnkitをインストールしたのでメモ。
OSはubuntu、PHPのバージョンは5.6.24で行った。

php-idnkitはidnkitをPHPから使えるようにするPHPの拡張モジュール。
拡張モジュールなのでPHPをコンパイルし直す必要はない。モジュールをコンパイルして、php.iniのextensionに指定すれば使えるようになる。

## idnkitのインストール

php-idnkitはあくまでidnkitをPHPから使えるようにする拡張モジュールなのでidnkitがないと動作しない。
まずはidnkitをインストールする。

idnkitはJPNICが提供しており、以下からソースコードをダウンロードすることができる。

* https://www.nic.ad.jp/ja/idn/idnkit/download/

```
$ wget https://www.nic.ad.jp/ja/idn/idnkit/download/sources/idnkit-1.0-src.tar.gz
$ tar -zxf idnkit-1.0-src.tar.gz
$ cd idnkit-1.0-src && \
    ./configure && \
    make && \
    make install
```

## php-idnkitをインストール

続いてphp-idnkitをインストールする。
そのままではエラーとなったので以下のパッチをあてた。

php-idnkit.patch

```diff
--- xxx/idnkit.c
+++ yyy/idnkit.c
@@ -36,7 +36,11 @@ static int le_idnkit;
  *
  * Every user visible function must have an entry in idnkit_functions[].
  */
+#if ZEND_MODULE_API_NO >= 20100525
+zend_function_entry idnkit_functions[] = {
+#else
 function_entry idnkit_functions[] = {
+#endif
    PHP_FE(idnkit_decodename,   NULL)
    PHP_FE(idnkit_encodename,   NULL)
    PHP_FE(idnkit_errno,        NULL)
@@ -104,7 +108,7 @@ PHP_MINIT_FUNCTION(idnkit)
    idn_nameinit(1);

    /* get idnkit version */
-   REGISTER_STRING_CONSTANT("IDNKIT_VERSION", (char*)idn_version_getstring(), CONST_CS | CONST_PERSISTENT);
+   REGISTER_STRING_CONSTANT("IDNKIT_VERSION", "1.0", CONST_CS | CONST_PERSISTENT);

    /* idnkit actions */
    REGISTER_LONG_CONSTANT("IDNKIT_DELIMMAP",       IDN_DELIMMAP,       CONST_CS | CONST_PERSISTENT);
```

```
$ wget http://www.sera.desuyo.net/idnkit/php-idnkit-20031204.tar.gz
$ tar -zxf php-idnkit-20031204.tar.gz
$ cd idnkit && \
    patch -lsp1 < /tmp/php-idnkit.patch && \
    phpize && \
    ./configure && \
    make && \
    make install
```

## php.iniにextensionを指定

最後にphp.iniにextensionを指定を指定する。
以下は、`/usr/local/lib/php.ini` にphp.iniがある場合の設定例。

```
$ echo "extension=idnkit.so" >> /usr/local/lib/php.ini
```

以下のコマンドが動作すればOK。

```php
$ php -a
> echo idnkit_encodename(mb_convert_encoding("日本語ＪＰドメイン名.jp", "UTF-8", "auto"));
```
