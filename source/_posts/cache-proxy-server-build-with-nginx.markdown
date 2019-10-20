---
layout: post
title: "Nginxで構築するキャッシュプロキシサーバ"
date: 2018-03-13 15:27:18 +0900
comments: true
categories: 
---

Nginxを使えば簡単にプロキシサーバを構築することができます。手元で動かせるサンプルコードをGithubで公開しています。git cloneしてご利用ください。PCにDockerがインストールされていれば簡単に動作環境を構築することができます。

<a href="https://github.com/shoyan/nginx-proxy-cache" target="_blank">https://github.com/shoyan/nginx-proxy-cache</a>

## キャッシュを有効にする

Nginxのキャッシュ機能はデフォルトでは有効ではないので、設定する必要があります。

/etc/nginx/nginx.conf

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    # キャッシュの場所を指定する
    # levels: キャッシュの階層レベル
    # keys_zone: 使用する共有メモリゾーンの名前とサイズ
    proxy_cache_path /var/cache/nginx/cache levels=1:2 keys_zone=my-key:10m;
    proxy_temp_path /var/cache/nginx/tmp;

    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

次にlocationディレクティブでキャッシュを有効にします。

/etc/nginx/conf.d/default.conf

```
server {
    listen       80;
    server_name  localhost;

    proxy_set_header    Host    $host;
    proxy_set_header    X-Real-IP    $remote_addr;
    proxy_set_header    X-Forwarded-Host       $host;
    proxy_set_header    X-Forwarded-Server    $host;
    proxy_set_header    X-Forwarded-For    $proxy_add_x_forwarded_for;

    location / {
        # 8081のポートで起動しているサーバにプロキシする
        proxy_pass    http://app:8081/;
        # 全てのアクセスをキャッシュする
        proxy_ignore_headers Cache-Control;
        # my-keyというキー名でキャッシュを登録
        proxy_cache my-key;
        # キャッシュは1分間有効にする
        proxy_cache_valid any 1m;
        # レスポンスヘッダにキャッシュがヒットしたかどうかを付与する
        add_header X-Nginx-Cache $upstream_cache_status;
    }
}
```

以上の設定を行えばキャッシュが有効になります。

キャッシュの詳細な設定についてはNginxのドキュメントを参照ください。
<a href="http://nginx.org/en/docs/http/ngx_http_proxy_module.html" target="_blank">ngx_http_proxy_module</a>

アプリサーバはNode.jsで構築しています。1秒でHello Worldを返す単純なアプリサーバです。こちらもDockerで動作するのでNode.jsをPCにインストールする必要はありません。

プロキシサーバとアプリサーバの起動は次のコマンドで行います。

```
$ docker-compose up
Starting proxycachesample_app_1 ...
Starting proxycachesample_app_1 ... done
Starting proxycachesample_proxy_1 ...
Starting proxycachesample_proxy_1 ... done
Attaching to proxycachesample_app_1, proxycachesample_proxy_1
app_1    | Server running at http://localhost:8081/
```

1回目のアクセスはリクエストがアプリサーバまで行くのでレスポンスタイムは1秒程度かかります。

```
$ curl http://localhost:8080 -w "%{time_total}"
Hello World
1.009103
```

2回目からのアクセスはプロキシサーバがキャッシュしているため、レスポンスタイムが速くなります。

```
$ curl http://localhost:8080  -w "%{time_total}"
Hello World
0.003797
```

確認が終わったらサーバを停止しておきましょう。サーバの停止はCtrl+Cか次のコマンドで行えます。

```
$ docker-compose stop
```
