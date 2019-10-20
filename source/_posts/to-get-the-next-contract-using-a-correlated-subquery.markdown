---
layout: post
title: "相関サブクエリを使って次回契約を取得する"
date: 2015-09-25 13:35:45 +0900
comments: true
categories: sql
---

相関サブクエリを使って次回契約を取得します。

### Contract table
| id | account_id | start_date | end_date | 
| --- | --- | --- | --- |
| 1 | 1 | 20140101 | 20141231 | 
| 2 | 1 | 20150101 | 20151231 | 
| 3 | 1 | 20160101 | 20161231 | 
| 4 | 1 | 20170101 | 20171231 | 
| 5 | 2 | 20150101 | 20151231 | 
| 6 | 2 | 20160101 | 20161231 | 


上記のようなaccount_idと開始日、終了日の登録してあるテーブルがあるとします。  
現在の契約を取得するのは簡単ですね。


```sql
# 現在契約を取得する
SELECT * FROM contracts WHERE start_date >= 現在日付 AND end_date <= 現在日付

```

現在契約を取得するのは簡単ですが、その次の契約を取得するとなるとそう単純にはいきません。

そこで、相関サブクエリを使います。  
相関サブクエリを使うことで次回契約を取得できます。


```sql
# 次回契約を取得する
SELECT * 
  FROM contracts As cont 
 WHERE start_date = (SELECT MIN(start_date) 
                       FROM contracts as c1
                     WHERE c1.start_date > ( SELECT end_date 
                                               FROM contracts as c2
                                             WHERE c2.start_date <= '20150528'
                                               AND c2.end_date >= '20150528'
                                               AND c1.account_id = c2.account_id)
                       AND cont.account_id = c1.account_id
                     GROUP BY c1.account_id);

```

### 結果
| id | account_id | start_date | end_date | 
| --- | --- | --- | --- |
| 3 | 1 | 20160101 | 20161231 | 
| 6 | 2 | 20160101 | 20161231 | 

### クエリの説明
クエリの説明をします。

クエリは内側からみていきます。  
まずは、一番内側にある、 `SELECT end_date ... AND c1.account_id = c2.account_id`のクエリです。  
このクエリでは現在の契約(ここでは2015/5/28とします)を取得します。

2つめのクエリで、次回以降の契約を取得します。  
`SELECT MIN(start_date)` を使うことで、次回契約のなかで直近の契約を取得できます。  
アカウントごとに直近の次回契約を取得したいので、GROUP BY account_id をしています。

c1.account_id = c2.account_id と cont.account_id = c1.account_id は行と行を比較するために必要です。  

3つめのクエリ(`SELECT * ... WHERE start_date =`)で直近の次回契約を条件として、データを取得します。

手元で試したい方は以下のクエリでデータをつくれます。


```sql
CREATE TABLE `contracts` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `account_id` int(11) DEFAULT NULL,
  `start_date` int(11) DEFAULT NULL,
  `end_date` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `contracts` (`id`, `account_id`, `start_date`, `end_date`)
VALUES
    (1, 1, 20140101, 20141231),
    (2, 1, 20150101, 20151231),
    (3, 1, 20160101, 20161231),
    (4, 1, 20170101, 20171231),
    (5, 2, 20150101, 20151231),
    (6, 2, 20160101, 20161231);

```

