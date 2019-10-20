---
layout: post
title: "FTPについて調べてみた"
date: 2016-06-17 15:21:34 +0900
comments: true
categories: linux
description: "FTPはすでにご存知のかたも多いと思います。自分はFTPについて、「ファイル転送に使われるプロトコルであり、セキュリティが脆弱である」というくらいしか理解していなかったので詳細を把握するために調べました。"
---

FTPはすでにご存知のかたも多いと思います。  
自分はFTPについて、「ファイル転送に使われるプロトコルであり、セキュリティが脆弱である」というくらいしか理解していなかったので詳細を把握するために調べました。

## FTPとは

FTPとはインターネットの初期の頃から存在するプロトコルで、今でもインターネットでよく使われています。  
用途としては、以下に使われます。

- ウェブページの各種ファイル(HTMLや画像)をクライアントのPCからサーバーへアップロードする
- ソフトウェアの配布サイトやFTPファイルサーバーからクライアントPCへダウンロードする

FTPを利用するにはユーザー名とパスワードが必要です。  
ソフトを配布するための目的で使う匿名でアクセスできるAnonymous(匿名)FTPサーバーもあります。  
しかし、形式上ユーザー名とパスワードは必要なので、ユーザー名にanonymousやftpを使います。パスワードは何でもよいですが、慣習としてメールアドレスを入力するようです。

## プロトコルの概要

FTPのプロトコルは[RFC959](https://tools.ietf.org/html/rfc959)に定義してあります。  
RFC959が書かれたのが1985年なのでおよそ30年前に作られたプロトコルです。

FTPは大きくわけると、コネクションの確立とデータ転送にわけることができます。  
そして、使うポートも2つが用意してあります。

* 20: データ転送ポート
* 21: コントロールポート

20番がデータを転送するときに使うポートで、21番は認証などの接続に使うためのポートです。

### コネクションの確立

FTPは `アクティブモード` か `パッシブモード` のいずれかで動作し、サーバーとの接続をする際にどちらかを選択します。

### アクティブモードの場合

アクティブモードの場合、クライアントはデータ転送用のポートを用意しPORTコマンドを使って待ち受けポートをサーバーに伝えます。  
サーバーはPORTコマンドを受け取ると、そのポートに対して接続を行います。  
しかし、Firewallがある環境の場合、サーバーからの接続が拒否されうまくいかない場合があります。  
その際はパッシブモードを使います。

### パッシブモード

パッシブモードでは、まず最初にクライアントがPASVというコマンドを使いサーバーに送信します。  
そのコマンドを受け取ったサーバーは自身のIPアドレスとポート番号をクライアントに送信します。  
クライアントは受け取ったIPアドレスとポート番号へコネクションを確立しにいきます。  

アクティブモードはサーバーより接続を行う、パッシブモードはクライアントより接続を行うという違いがあります。  
ちなみに、FTPはよく使われるプロトコルのため、PORTコマンドがFirewallを通過する際にPORTコマンドに書かれたPORTは通過できるようにしてくれるFirewallもあるとのことです。

## データ転送

接続ができたら、次はデータ転送を行います。  
FTPにはデータタイプという考え方があって、現在は2つのモードが使われています。

### アスキーモード

アスキーモードは、必要であればデータを変換します。  
例えば異なるOS間でファイルを送ると改行コードが違ったりする場合があると思いますが、アスキーモードはファイルを受け取るホストに適した改行コードにファイルを変換します。

### Imageモード(バイナリモード)

Imageモードは一般的にバイナリモードと呼ばれます。  
バイト単位でデータを転送します。  
アスキーモードのようにデータの変換は行われません。

データの転送モードには、以下の3つのモードがあります。

- ストリームモード
- ブロックモード
- 圧縮モード

### ストリームモード

データをそのまま転送するモードです。

### ブロックモード

データをブロックに分割して転送するモードです。

### 圧縮モード

ランレングスエンコーディングを使ってデータを圧縮して転送するモードです。

## セキュリティ上の問題

FTPはセキュアなプロトコルとして設計されていません。  
ユーザー名やパスワードを暗号化せずに送信する問題のほかにも数多くのセキュリティ脆弱性があげられています。

- 総当たり攻撃
- en:FTP bounce attack
- パケットキャプチャ (sniffing)
- Port stealing
- en:Spoofing attack
- ユーザ名保護

また、通信内容を暗号化できないので通信経路上でパケットキャプチャすることで盗聴することができてしまいます。  
セキュアにする一般的な方法として、SSL/TLSセッション上で通信を行うようにします。  
これをFTPSと言います。  
また、SSHを介してファイル転送を行うSFTP、SCPを使います。  

ちなみにFTPSとSFTPの違いは以下のようになります。

* FTPS : FTPの通信をSSL/TLSで暗号化 → FTPの拡張
* SFTP : SSHの通信を使って、FTPを行う → SSHで動作するアプリケーション

理解を深めるためにtelnetを使って実際にFTPサーバーと通信してみます。  

## 実際にFTPを試してみる

実際にFTPを試してみる場合、FTPサーバーが必要です。  
[ロリポップ！レンタルサーバー](https://lolipop.jp)を使えば無料でFTPサーバーが利用できます。

ちなみにtelnetでファイルの送受信はできません。  
というのも、telnetでは1つのポートを使った通信しかサポートしていないからです。  
FTPはデータ転送用のポートと制御用のポートの2つを利用するため、telnetでは認証しか行えません。  

まずは、telnetで認証をやってみます(localhostにftpサーバーが起動しているという前提です)。  


```
$ telnet localhost 21
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 FTP server ready.

```

まず、ユーザ名を入力します。


```
USER ftp
331 Password required for example.com

```

次に、パスワードを入力します。


```
PASS 123
230 User example.com logged in.

```

telnetでは他に何もできませんのでQUITします。


```
QUIT
221 Goodbye.
Connection closed by foreign host.

```

### FTPでファイルの送受信を行う

FTPでファイルの送受信を行うには、ftpコマンドを使います。  
`-d` オプションはデバッグモードです。


```
ftp -d localhost 21

```

ユーザー名とパスワードを聞かれるので入力します。

`ls`でファイルの一覧をみることができます。


```
ftp> ls
---> EPSV
229 Entering Extended Passive Mode (|||65086|)
229 Entering Extended Passive Mode (|||65086|)
---> LIST
150 Opening ASCII mode data connection for file list
drwx---r-x   6 shoyan shoyan      4096 Jun 17 11:31 .
drwx---r-x   6 shoyan shoyan     4096 Jun 17 11:31 ..
-rw-r--r--   1 shoyan shoyan     3096 Jun 17 11:30 index.html
226 Transfer complete

```

`get`コマンドでファイルをダウンロードできます。


```
ftp> get index.html
local: index.html remote: index.html
---> SIZE index.html
213 3096
---> EPSV
229 Entering Extended Passive Mode (|||65011|)
229 Entering Extended Passive Mode (|||65011|)
---> RETR index.html
150 Opening BINARY mode data connection for index.html (3096 bytes)
100% |************************************************************************************************************************************************************************************************|  3096        4.61 MiB/s    00:00 ETA
226 Transfer complete
3096 bytes received in 00:00 (163.72 KiB/s)
---> MDTM index.html
213 20160617023028
parsed date `20160617023028' as 1466130628, Fri, 17 Jun 2016 11:30:28 +0900

```

参考リンク

- [File Transfer Protocol](https://en.wikipedia.org/wiki/File_Transfer_Protocol)
- [File Transfer Protocol(日本語)](https://ja.wikipedia.org/wiki/File_Transfer_Protocol)
- [3 Minutes Networking No.56](http://www5e.biglobe.ne.jp/%257eaji/3min/56.html)
- [telnetでファイル転送？(FTP)](http://ash.jp/net/ftp_command.htm)
- [FTPコマンドでファイル転送](http://ash.jp/net/telnet_ftp.htm)
- [FTPS、SFTPの違いって?](http://qiita.com/kasei-san/items/bf766e6c2ececa4c3905)
