---
layout: post
title: "SSHKitを実際に使ってみて理解する"
date: 2016-06-14 13:34:48 +0900
comments: true
categories: ruby gem
description: "Capistrano/sshkitを紹介します。SSHKitはサーバーに対してコマンドを実行するためのツールキットです。CapistranoやCapistranoプラグインではSSHKitが使われています。実際に使ってみて使い方を理解しましょう。"
---

Capistrano/sshkitを紹介します。  
https://github.com/capistrano/sshkit

SSHKitはリモートサーバーに対してコマンドを実行するためのツールキットです。  
CapistranoやCapistranoプラグインではSSHKitが使われています。

## インストール


```
gem install sshkit

```

## コマンドのサンプル

実際に使ってみて理解していきます。
使うには、sshkitをロードする必要があります。


```ruby
require 'sshkit'
include SSHKit::DSL

```

### ホスト名を取得する

まずはログインしてホスト名を取得してみましょう。


```
on ['deploy@example.com’] do |host|
 puts capture(:hostname)
end
=> example.com

```

`on` メソッドに対象のサーバーとブロックを渡します。  
対象のサーバーは複数設定することも可能です。  
ブロックにはサーバー上で実行するコマンドを設定します。  
`capture`メソッドは渡された引数をコマンドとして実行し、結果をログに出力します。

### 特定のユーザーでコマンドを実行する

特定のユーザーでコマンドを実行する場合は、`as`メソッドで指定します。


```
on ['example.com'] do |host|
  as 'deploy' do
  　puts capture(:whoami)
  end
end

```

### 特定のディレクトリを指定する

特定のディレクトリを指定する場合は、`within`メソッドを指定します。


```
on ['deploy@example.com'] do |host|
  within '/var/log' do
    puts capture(:head, '-n5', 'messages')
  end
end

```

`/var/log` で `head -n5 messages` を実行します。

### 環境変数を指定する

`with`メソッドで環境変数を指定することができます。


```
on hosts do |host|
  with rack_env: :test do
    puts capture("env")
  end
end

```

rack_envに`:test` を設定しています。

### ファイルをチェックして存在すればメッセージを表示、なければファイルを作成する


```
on ['deploy@example.com'] do |host|
  f = '/tmp/file'
  if test("[ -f #{f} ]")
    info "#{f} already exist on #{host}!"
  else
    execute :touch, f
  end
end
INFO [790b6aaa] Running /usr/bin/env touch /tmp/file as deploy@example.com
INFO [790b6aaa] Finished in 0.052 seconds with exit status 0 (successful).

```

`test`メソッドでファイルをチェックし、`execute`メソッドで`touch`コマンドを実行しています。

### ファイルをアップロードする

ファイルをアップロードすることもできます。


```
on ['deploy@example.com'] do |host|
  upload! 'README.md', '/tmp/README.md'
end

```

第1引数がローカルのファイルのパス、第2引数がサーバーに配置するファイルのパスです。

また、`recursive`オプションをtrueに設定することでディレクトリをアップロードすることもできます。


```
on hosts do |host|
  upload! '.', '/tmp/mypwd', recursive: true
end

```

### ローカルで実行する

ローカルで実行することもできます。


```
run_locally do
  within '/tmp' do
    execute :whoami
  end
end

# もしくは

on(:local) do
  within '/tmp' do
    execute :whoami
  end
end

```

### Rakeタスクで利用する

RakeタスクでSSHKitのDSLを使うこともできます。  
この仕組みを利用してCapistranoのプラグインは作成されています。


```
require 'sshkit'

SSHKit.config.command_map[:rake] = "./bin/rake"

desc "Deploy the site, pulls from Git, migrate the db and precompile assets, then restart Passenger."
task :deploy do
  include SSHKit::DSL

  on "example.com" do |host|
    within "/opt/sites/example.com" do
      execute :git, :pull
      execute :bundle, :install, '--deployment'
      execute :rake, 'db:migrate'
      execute :rake, 'assets:precompile'
      execute :touch, 'tmp/restart.txt'
    end
  end
end

```

サンプルがこちらにたくさんあるので、参考になると思います。

https://github.com/capistrano/sshkit/blob/master/EXAMPLES.md
