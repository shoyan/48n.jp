---
layout: post
title: "先週月曜日の日付を取得するアルゴリズム"
date: 2016-06-09 14:04:17 +0900
comments: true
categories: アルゴリズム
description: "曜日の日付を計算するアルゴリズムをRubyで実装してみました。先週の月曜日を求めるアルゴリズム、次の月曜日を求めるアルゴリズム、前回の月曜日を求めるアルゴリズム等を実装しています。"
---

## 先週の月曜日を求めるアルゴリズム

* 先週(7日前)が月曜日である場合はその日付を返す
* 月曜日でない場合
  - 月曜日より前であれば日付を1日たす
  - 月曜日より後であれば日付を1日ひく

### Rubyで実装


```ruby
def last_monday(date = Date.today - 7)
  return date if date.monday?
  if date.wday < 1
    date += 1
  else
    date -= 1
  end
  last_monday(date)
end

```

### 先週の金曜日を求める場合


```ruby
def last_friday(date = Date.today - 7)
  return date if date.friday?
  if date.wday < 5
    date += 1
  else
    date -= 1
  end
  last_friday(date)
end

```

## 次の月曜日の日付を求めるアルゴリズム

* 明日が月曜日かどうか
  - 月曜日であればその日を返す
  - 月曜日でなければ1日たす

### Rubyで実装


```ruby
def next_monday(date = Date.today + 1)
  return date if date.monday?
  next_monday(date + 1)
end

```

## 前回の月曜日の日付を求めるアルゴリズム

* 昨日が月曜日かどうか
  - 月曜日であればその日を返す
  - 月曜日でなければ1日ひく

### Rubyで実装


```ruby
def prev_monday(date = Date.today - 1)
  return date if date.monday?
  prev_monday(date - 1)
end

```

### 実行結果


```
puts Date.today.strftime("%Y-%m-%d (%a)")
=> 2016-06-09 (Thu)
puts prev_monday.strftime("%Y-%m-%d (%a)")
=> 2016-06-06 (Mon)
puts next_monday.strftime("%Y-%m-%d (%a)")
=> 2016-06-13 (Mon)
puts last_monday.strftime("%Y-%m-%d (%a)")
=> 2016-05-30 (Mon)
puts last_friday.strftime("%Y-%m-%d (%a)")
=> 2016-06-03 (Fri)

```
