---
layout: post
title: "LaravelをMacにインストールして起動する手順"
date: 2016-04-28 13:36:36 +0900
comments: true
categories: php laravel
description: "PHPで人気のフレームワーク、Laravelのチュートリアルです。このチュートリアルではMacでLaravelを起動する手順を紹介します。冗長な説明を除きLaravelを動かすために重要な部分のみ解説することでスピーディにLaravelを動作できるようにしています。"
---

PHPで人気のフレームワーク、[Laravel](https://laravel.com/)を手元のPCにインストールして起動するチュートリアルです。
冗長な説明を除きLaravelを動かすために重要な部分のみ解説することでスピーディにLaravelを動作できるようにしています。

### Laravelのインストール

まずはcomposerでlaravelコマンドをインストールします。


```php
⇒  composer global require "laravel/installer"
Changed current directory to /Users/PMAC025S/.composer

Using version ^1.3 for laravel/installer
./composer.json has been created
Loading composer repositories with package information
Updating dependencies (including require-dev)
  - Installing symfony/process (v3.0.4)
    Downloading: 100%

  - Installing symfony/polyfill-mbstring (v1.1.1)
    Loading from cache

  - Installing symfony/console (v3.0.4)
    Downloading: 100%

  - Installing guzzlehttp/promises (1.1.0)
    Downloading: 100%

  - Installing psr/http-message (1.0)
    Loading from cache

  - Installing guzzlehttp/psr7 (1.3.0)
    Downloading: 100%

  - Installing guzzlehttp/guzzle (6.2.0)
    Downloading: 100%

  - Installing laravel/installer (v1.3.3)
    Downloading: 100%

symfony/console suggests installing symfony/event-dispatcher ()
symfony/console suggests installing psr/log (For using the console logger)
Writing lock file
Generating autoload files

```

`.bashrc` や `.zshrc` にパスを追加します。


```
export PATH="$PATH:/Users/PMAC025S/.composer/vendor/bin"

```

これでlaravelコマンドが実行できるようになります。


```
⇒  laravel
Laravel Installer version 1.3.3

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help  Displays help for a command
  list  Lists commands
  new   Create a new Laravel application.

```

`laravel new application_name` でインストールします。  


```
⇒  laravel new blog
Crafting application...

```

以下の構成で作成されています。


```
⇒  tree -L 1 blog
blog
├── app
├── artisan
├── bootstrap
├── composer.json
├── composer.lock
├── config
├── database
├── gulpfile.js
├── package.json
├── phpunit.xml
├── public
├── readme.md
├── resources
├── server.php
├── storage
├── tests
└── vendor

```

## 設定

設定情報は`config`ディレクトリに保存します。
今回は設定を変更しません。

## データベースを準備する
### データベースのマイグレーション

Laravelに同梱されているartisanというツールを使って作成します。


```
⇒  php artisan make:migration create_tasks_table --create=tasks
Created Migration: 2016_04_28_011840_create_tasks_table

```

`database/migrations`ディレクトリに作成されます。
2016_04_28_011840_の部分はartisanが作成するので、makeするタイミングによって変わります。

`2016_04_28_011840_create_tasks_table.php`を編集して、nameカラムを追加します。


```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateTasksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('tasks', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('tasks');
    }
}

```

`.env`ファイルのデータベースの設定を自分のローカルのPCのmysqlに変更します。  
デフォルトでは、[Homestead](https://laravel.com/docs/5.2/homestead)というLaravel開発用の仮想環境の設定です(今回は使いません)。


```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=root
DB_PASSWORD=

```

マイグレーションを実行します。


```
⇒  php artisan migrate
Migration table created successfully.
Migrated: 2014_10_12_000000_create_users_table
Migrated: 2014_10_12_100000_create_password_resets_table
Migrated: 2016_04_28_011840_create_tasks_table

```

### モデルを作成する

Laravelは[Eloquent](https://laravel.com/docs/5.2/eloquent)というORMがデフォルトで使われるようになっています。

artisanでモデルを作成します。


```
⇒  php artisan make:model Task
Model created successfully.

```

モデルは `app` ディレクトリ配下に作成されます。

### ルーティング

ルーティングの設定は、`app/Http/routes.php`に定義します。
(ルーティングの設定はsinatraっぽい)


```
<?php

use App\Task;
use Illuminate\Http\Request;

/**
 * Show Task Dashboard
 */
Route::get('/', function () {
    return view('tasks');
});

/**
 * Add New Task
 */
Route::post('/task', function (Request $request) {
    //
});

/**
 * Delete Task
 */
Route::delete('/task/{task}', function (Task $task) {
    //
});

```

### Viewの設定

Larvelはデフォルトで[Blade](https://laravel.com/docs/5.2/blade)というテンプレートエンジンを使います。  
viewのディレクトリは`resources/views`です。


```
$ mkdir resources/views/layouts

```

全体のレイアウトのテンプレートとして使う`app.blade.php`を作成します。


```
 <!-- resources/views/layouts/app.blade.php -->

<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Laravel Quickstart - Basic</title>

        <!-- CSS And JavaScript -->
    </head>

    <body>
        <div class="container">
            <nav class="navbar navbar-default">
                <!-- Navbar Contents -->
            </nav>
        </div>

        @yield('content')
    </body>
</html>

```

`tasks.blade.php`を作成します。


```
 <!-- resources/views/tasks.blade.php -->

@extends('layouts.app')

@section('content')

    <!-- Bootstrap Boilerplate... -->

    <div class="panel-body">
        <!-- Display Validation Errors -->

        <!-- New Task Form -->
        <form action="{{ url('task') }}" method="POST" class="form-horizontal">
            {!! csrf_field() !!}

            <!-- Task Name -->
            <div class="form-group">
                <label for="task" class="col-sm-3 control-label">Task</label>

                <div class="col-sm-6">
                    <input type="text" name="name" id="task-name" class="form-control">
                </div>
            </div>

            <!-- Add Task Button -->
            <div class="form-group">
                <div class="col-sm-offset-3 col-sm-6">
                    <button type="submit" class="btn btn-default">
                        <i class="fa fa-plus"></i> Add Task
                    </button>
                </div>
            </div>
        </form>
    </div>

    <!-- TODO: Current Tasks -->
@endsection

```

### サーバーを起動する

さて、一通りの設定が整いました。  
サーバーを起動してブラウザで確認してみましょう。  
以下のコマンドでサーバーを起動します。


```
⇒  php artisan serve
Laravel development server started on http://localhost:8000/

```

`http://localhost:8000/` にブラウザでアクセスします。

Taskという文字とフォームが表示されれば成功です！

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4844339451&linkId=3602fd744613779ea0da0a7b2834983c"></iframe>

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=syoyama-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4774173134&linkId=8a4652f77ca59ebb2ab2adf95f685aef"></iframe>

### 参考文献

* https://laravel.com/docs/5.2
* https://laravel.com/docs/5.2/quickstart
