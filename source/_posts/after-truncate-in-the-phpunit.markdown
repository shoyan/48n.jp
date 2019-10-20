---
layout: post
title: "PHPUnitの後処理でテーブルをTRUNCATEする"
date: 2016-11-08 17:48:30 +0900
comments: true
categories: php
description: "PHPUnitでテスト用のレコードを作成するのだが、テストで作成したレコードが残ってしまい再度テストを行うと失敗するという現象に遭遇した。テストを実行した後にPHPUnitでテーブルをTRUNCATEする方法を紹介する"
---

PHPUnitでテスト用のレコードを作成するのだが、テストで作成したレコードが残ってしまい再度テストを行うと失敗するという現象に遭遇した。

以下のように`AppTestCase` を作成してカスタマイズしている。
`setup()` メソッドで `datasets` ディレクトリにymlファイルがあればその内容でデータを作成している。

```php
class AppTestCase extends PHPUnit_Extensions_Database_TestCase
{
    static private $_pdo = null;
    private $_conn       = null;
    private $_dataSet;
    private $_obj;

    protected function setUp()
    {
        $fixturePath = 'datasets/' . get_class($this) . '/' . $this->getName() . '.yml';
        if (file_exists($fixturePath)) {
            $this->_dataSet = new PHPUnit_Extensions_Database_DataSet_YamlDataSet($fixturePath);
        } else {
            $this->_dataSet = new PHPUnit_Extensions_Database_DataSet_DefaultDataSet();
        }
        parent::setUp();
    }

    public function getConnection()
    {
        if ($this->_conn === null) {
            if (self::$_pdo == null) {
                self::$_pdo = new PDO(DB_DSN, DB_USER, DB_PASSWD);
            }
            $this->_conn = $this->createDefaultDBConnection(self::$_pdo, DB_DBNAME);
        }
        return $this->_conn;
    }

    public function getDataSet()
    {
        return $this->_dataSet;
    }
}
```

PHPUnitのドキュメントにはTRUNCATEが実行されると書いてあるが、TRUNCATEはテストデータが作成される前に行われるので最後に作成されたレコードは残ってしまう。

* https://phpunit.de/manual/current/ja/database.html

MySQLサーバのクエリログは以下。
ドキュメント通りデータが作成される前にTRUNCATEが実行されている。

```
2016-11-08T07:47:26.906619Z        26 Connect   root@192.168.75.91 on app_test using TCP/IP
2016-11-08T07:47:26.907174Z        26 Query     SET NAMES 'ujis'
2016-11-08T07:47:27.136497Z        27 Connect   root@192.168.75.91 on app_test using TCP/IP
2016-11-08T07:47:27.248506Z        28 Connect   root@192.168.75.91 on  using TCP/IP
2016-11-08T07:47:27.249034Z        28 Init DB   app_test
2016-11-08T07:47:27.256189Z        28 Init DB   app_test
2016-11-08T07:47:27.256837Z        28 Query     select * from users
2016-11-08T07:47:27.258065Z        28 Init DB   app_test
2016-11-08T07:47:27.258473Z        28 Init DB   app_test
2016-11-08T07:47:27.331138Z        27 Query     SET FOREIGN_KEY_CHECKS = 0
2016-11-08T07:47:27.331548Z        27 Query     TRUNCATE `users`
2016-11-08T07:47:27.349437Z        27 Query     SET FOREIGN_KEY_CHECKS = 1
2016-11-08T07:47:27.349922Z        27 Query     SHOW COLUMNS FROM `users`
2016-11-08T07:47:27.350848Z        27 Query     SHOW INDEX FROM `users`
2016-11-08T07:47:27.351590Z        27 Query     INSERT INTO `users`
                (`account_id`, `domain`, `created_at`)
                VALUES
                ('1', ‘example.com', '2016-11-02 18:40:10')
2016-11-08T07:47:27.354645Z        28 Init DB   app_test
2016-11-08T07:47:27.355089Z        28 Init DB   app_test
2016-11-08T07:47:27.355386Z        28 Query     select * from users
2016-11-08T07:47:27.360299Z        26 Quit
2016-11-08T07:47:27.391481Z        27 Quit
2016-11-08T07:47:27.431965Z        28 Quit
```

テストを実行した後にTRUNCATEするには `getTearDownOperation` メソッドを追加する。

```
class AppTestCase extends PHPUnit_Extensions_Database_TestCase
{
    static private $_pdo = null;
    private $_conn       = null;
    private $_dataSet;
    private $_obj;

    protected function setUp()
    {
        $fixturePath = 'datasets/' . get_class($this) . '/' . $this->getName() . '.yml';
        if (file_exists($fixturePath)) {
            $this->_dataSet = new PHPUnit_Extensions_Database_DataSet_YamlDataSet($fixturePath);
        } else {
            $this->_dataSet = new PHPUnit_Extensions_Database_DataSet_DefaultDataSet();
        }
        parent::setUp();
    }

    public function getConnection()
    {
        if ($this->_conn === null) {
            if (self::$_pdo == null) {
                self::$_pdo = new PDO(DB_DSN, DB_USER, DB_PASSWD);
            }
            $this->_conn = $this->createDefaultDBConnection(self::$_pdo, DB_DBNAME);
        }
        return $this->_conn;
    }

    public function getDataSet()
    {
        return $this->_dataSet;
    }

    // テスト後にテーブルをTRUNCATEする
    public function getTearDownOperation()
    {
        return PHPUnit_Extensions_Database_Operation_Factory::TRUNCATE();
    }
}
```

あとは各UnitTestの`tearDown`メソッドで `parent::tearDown()` を実行する。

```
<?php
class SampleTest extends AppTestCase
{
    public function setUp()
    {
        parent::setUp();
    }

    public function tearDown()
    {
        parent::tearDown();
    }
    .....
}
```

以下が変更後のMySQLのクエリログだ。
最後にTRUNCATEが実行されている。

```
2016-11-08T08:43:49.367260Z       120 Connect   root@192.168.75.91 on app_test using TCP/IP
2016-11-08T08:43:49.367966Z       120 Query     SET NAMES 'ujis'
2016-11-08T08:43:49.626685Z       121 Connect   root@192.168.75.91 on app_test using TCP/IP
2016-11-08T08:43:49.743978Z       122 Connect   root@192.168.75.91 on  using TCP/IP
2016-11-08T08:43:49.744418Z       122 Init DB   app_test
2016-11-08T08:43:49.751453Z       122 Init DB   app_test
2016-11-08T08:43:49.752239Z       122 Query     select * from users
2016-11-08T08:43:49.758493Z       122 Init DB   aap_test
2016-11-08T08:43:49.759944Z       122 Init DB   app_test
2016-11-08T08:43:49.760656Z       122 Query     select * from users
2016-11-08T08:43:49.828928Z       121 Query     SET FOREIGN_KEY_CHECKS = 0
2016-11-08T08:43:49.829372Z       121 Query     TRUNCATE `users`
2016-11-08T08:43:49.852672Z       121 Query     SET FOREIGN_KEY_CHECKS = 1
2016-11-08T08:43:49.853435Z       121 Query     SHOW COLUMNS FROM `users`
2016-11-08T08:43:49.854605Z       121 Query     SHOW INDEX FROM `users`
2016-11-08T08:43:49.855462Z       121 Query     INSERT INTO `users`
                (`account_id`, `domain`, `created_at`)
                VALUES
                ('1', ‘example.com', '2016-11-02 18:40:10')
2016-11-08T08:43:49.863268Z       122 Init DB   app_test
2016-11-08T08:43:49.864301Z       122 Init DB   app_test
2016-11-08T08:43:49.864678Z       122 Query     select * from users
2016-11-08T08:43:49.869473Z       121 Query     SET FOREIGN_KEY_CHECKS = 0
2016-11-08T08:43:49.869882Z       121 Query     TRUNCATE `users`
2016-11-08T08:43:49.885689Z       121 Query     SET FOREIGN_KEY_CHECKS = 1
2016-11-08T08:43:49.888006Z       120 Quit
2016-11-08T08:43:49.914728Z       121 Quit
2016-11-08T08:43:49.941933Z       122 Quit
```

