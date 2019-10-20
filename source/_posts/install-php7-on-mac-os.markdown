---
layout: post
title: "Mac OSXにPHP7をインストール"
date: 2016-01-12 22:26:16 +0900
comments: true
categories: "PHP"
---

2015年12月にPHP7.0.0がリリースされました。
先日、PHP7.0.2がリリースされ、活発に開発が行われているようです。

手元でPHP7を試すために、PHP7.0.2をインストールしてみました。
OSはMac OSX(Yosemite)です。

依存パッケージのinstallにはhomebrewを使うので、updateしておきます。


```
brew update

```

PHPのインストールには、phpenvを使います。
phpenvを使うことで、複数のバージョンをインストールすることができます。

### phpenvのインストール

```
$ git clone git@github.com:CHH/phpenv.git
$ cd phpenv
$ phpenv-install.sh

```

以下を `$HOME/.bash_profile` or `$HOME/.bashrc`に追記します。
(zshの場合は、`$HOME/.zsh_profile` or `$HOME/.zshrc`です。)

```
eval "$(phpenv init -)"

```

### php-buildのインストール
phpのbuildはphp-buildで行うため、php-buildのインストールを行います。


```
$ git clone git://github.com/php-build/php-build.git $HOME/.phpenv/plugins/php-build

```

### 依存パッケージのインストール
PHP7のインストールに必要なパッケージをインストールします。


```
$ brew install bison
$ brew install re2c
$ brew install libjpeg
$ brew install libpng
$ brew install libmcrypt
$ brew install autoconf

```

### PHP7.0.2のインストール


```
$ phpenv install 7.0.2

```

PHP7がインストールできたら、globalコマンドでPHPのバージョンを設定します。


```
$ phpenv global 7.0.2

```

PHPのbuild時にエラーがでた場合はphp-buildのソースコードを修正して確認してみてください。
https://github.com/php-build/php-build/blob/bfd562bd6c11a97f953d92b0aac699ad82a045e2/share/php-build/extension/extension.sh#L128

.phpenv/plugins/php-build/share/php-build/extension/extension.sh

```
{
    $PREFIX/bin/phpize > /dev/null
    "$(pwd)/configure" --with-php-config=$PREFIX/bin/php-config \
        $configure_args > /dev/null

    make > /dev/null
    make install > /dev/null
} >&4 2>&1

```

以下のように修正します。


```
{
    $PREFIX/bin/phpize
    "$(pwd)/configure" --with-php-config=$PREFIX/bin/php-config \
        $configure_args

    make
    make install
} >&4 2>&1

```

### PHP7について
PHP7はPHP5との互換性が重視されているようなので、移行コストは低そうです。  
基本的にはPHP5.6までで非推奨の機能が廃止されています。  
詳しくは以下を参照ください。

* [PHP5.6.x からPHP7.0.xへの移行](https://secure.php.net/manual/ja/migration70.php)

パフォーマンスに関してはけっこう改善されているようで、PHP5の2倍とのことでした。

* [PHP7で変わること ——言語仕様とエンジンの改善ポイント](http://www.slideshare.net/hnw/phpcon-kansai20150530)
* [PHP7.0.0 Released](https://secure.php.net/archive/2015.php#id2015-12-03-1)

<iframe src="http://rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=ss_til&asins=4774144371" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>

<iframe src="http://rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=ss_til&asins=4802610440" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>
