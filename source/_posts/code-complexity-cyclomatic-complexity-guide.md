---
layout: post
title: "コードの複雑度を測る：循環的複雑度（Cyclomatic Complexity）の計算方法と実践ガイド"
date: 2025-08-07 10:00:00
updated: 2024-08-07 10:00:00
comments: true
tags: プログラミング コード品質 複雑度
description: "コードの複雑度を定量的に測る方法として、循環的複雑度（Cyclomatic Complexity）について詳しく解説します。計算方法、実践的な測定ツール、複雑度を下げる方法まで網羅的に紹介します。"
---

コードの品質を測る指標として「複雑度」がありますが、特に**循環的複雑度（Cyclomatic Complexity）**は、コードの保守性やバグの発生リスクを定量的に評価できる指標です。

この記事では、循環的複雑度の計算方法、複雑度を削減する方法、実践的な測定ツールについて、詳しく解説していきます。

## 循環的複雑度（Cyclomatic Complexity）とは

循環的複雑度は、プログラムの制御フローの分岐の数から算出される複雑度の指標です。この値が高いほど、コードは複雑で理解しにくく、バグが発生しやすくなります。

### 基本計算式

```
M = E - N + 2P
```

- **M**: 循環的複雑度
- **E**: 制御フローグラフの辺の数（edge）
- **N**: 制御フローグラフのノード数（node）
- **P**: 連結成分の数（通常は1で計算する）

### 簡易計算方法（現場でよく使われる）

実際の開発現場では、以下の簡易計算方法がよく使われます。

1. `if`、`for`、`while`、`case`、`catch` などの分岐やループの数を数える
2. その数に **1** を足す

## 計算例で理解する循環的複雑度
簡単な関数を例に説明をしていきます。

### 例1：簡易計算方法
まずは、簡易的な計算方法で関数の複雑度を計算していきましょう。

```javascript
function example(x) {
  if (x > 0) {           // 分岐 1
    console.log("A");
  } else if (x === 0) {  // 分岐 2
    console.log("B");
  } else {
    console.log("C");
  }

  for (let i = 0; i < 5; i++) {  // 分岐 3
    console.log(i);
  }
}
```

- 分岐・ループの数：3
- 複雑度：3 + 1 = **4**

上記の例のように分岐を繰り返しを数えて+1をすれば概算で計算することができます。

### 例2：基本計算式を使った計算方法
次は `M = E - N + 2P` を使った計算方法を見ていきましょう。

```javascript
function helper() {
  console.log("do something");
}
```

**制御フローグラフ：**
まずは、関数の制御フローグラフを作成します。
```
[Start] → [print] → [End]
```

**計算：**
次に制御フローグラフからノードの数、辺の数を数えます。

- N（ノード数）= 3（Start, print, End）
- E（辺の数）= 2（Start→print, print→End）
- P（連結成分数）= 1

```
M = E - N + 2P
M = 2 - 3 + 2*1
M = 1
```

結果：**複雑度 = 1**（最低値、まったく複雑ではない関数）

### 分岐を追加した場合
次に分岐を追加した関数の計算をしてみましょう。

```javascript
function helper(flag) {
  if (flag) {
    console.log("do something");
  }
}
```

**制御フローグラフ：**
```
[Start] → (flag?) ─Yes→ [print] → [End]
               └─No──────────────→ [End]
```

**計算：**
- N（ノード数）= 4（Start, 条件判定, print, End）
- E（辺の数）= 4（Start→条件判定, 条件判定→print, 条件判定→End, print→End）
- P（連結成分数）= 1

```
M = E - N + 2P
M = 4 - 4 + 2*1
M = 2
```

結果：**複雑度 = 2**（分岐を1つ追加しただけで、複雑度は 1 → 2 に増加）

### ネストした条件分岐
条件分岐がネストした関数の循環的複雑度を計算をしてみます。

```javascript
function helper(a, b) {
  if (a > 0) {
    if (b > 0) {
      console.log("A and B are positive");
    }
  }
}
```

**制御フローグラフ：**
```
[Start] 
   |
(a > 0?) ----No----> [End]
   |
  Yes
   |
(b > 0?) ----No----> [End]
   |
  Yes
   |
[print] → [End]
```

**計算：**
- N（ノード数）= 5（Start, 条件判定1, 条件判定2, print, End）
- E（辺の数）= 6（Start→条件判定1, 条件判定1→End, 条件判定1→条件判定2, 条件判定2→End, 条件判定2→print, print→End）
- P（連結成分数）= 1

```
M = E - N + 2P
M = 6 - 5 + 2*1
M = 3
```

結果：**複雑度 = 3**

### 重要なポイント

- 循環的複雑度は「ネストの深さ」ではなく「分岐の総数」で増える
- ただし、ネストが深いと認知的複雑度（Cognitive Complexity）はもっと増える
- 人間はネスト構造を理解するのが苦手なので、Cognitive Complexity では深さもペナルティになる

## 複雑度の目安

| 複雑度 | 意味 | 対応 |
|--------|------|------|
| 1〜10 | シンプル、理解しやすい | 問題なし |
| 11〜20 | 複雑、リファクタリングを検討 | リファクタリング推奨 |
| 21〜50 | 非常に複雑、バグの温床 | 分割必須 |
| 50+ | ほぼ地獄 |  分割必須 |

## 関数呼び出しと複雑度の関係

循環的複雑度の計算は、原則として「関数ごとに独立して」計測するのが基本です。

### 基本ルール

- 呼び出している関数の中身はカウントに含めない
- 関数呼び出しは、それ自体は「分岐」でも「ループ」でもないので複雑度には直接影響しない
- 複雑度に影響するのは、その関数呼び出しが条件式の一部やループの制御として使われている場合

### 例1：単純な呼び出し（複雑度に影響なし）

```javascript
function helper() {
  console.log("do something");
}

function main() {
  helper();
}
```

- `main` の中には分岐もループもなし
- `main` の複雑度 = 1（最低値）
- `helper` は別に計算（=1）

### 例2：条件式の中で呼び出し（影響あり）

```javascript
function isValid(x) {
  return x > 0;
}

function main(x) {
  if (isValid(x)) {   // 分岐1
    console.log("ok");
  }
}
```

- `isValid()` の中身はカウントしないが、`if` の分岐1つとして `main` の複雑度に加算される
- 複雑度：
  - `main` =  1（if）+ 1 = **2**
  - `isValid` = 1

## 複雑度を下げる方法

### 1. 関数の分割
関数を分割することで１つ１つの関数の循環的複雑度は下がります。

**Before（複雑度 = 8）**

```javascript
function complexFunction(data) {
  if (data.type === 'A') {
    // 処理A
    if (data.value > 10) {
      // 処理A-1
    } else {
      // 処理A-2
    }
  } else if (data.type === 'B') {
    // 処理B
    if (data.value > 20) {
      // 処理B-1
    } else {
      // 処理B-2
    }
  }
}
```

**After（複雑度 = 2）**

```javascript
function processTypeA(data) {
  if (data.value > 10) {
    // 処理A-1
  } else {
    // 処理A-2
  }
}

function processTypeB(data) {
  if (data.value > 20) {
    // 処理B-1
  } else {
    // 処理B-2
  }
}

function complexFunction(data) {
  if (data.type === 'A') {
    return processTypeA(data);
  } else if (data.type === 'B') {
    return processTypeB(data);
  }
}
```

### 2. ポリモーフィズムの活用
ポリモーフィズムを使うことで、分岐がなくなります。それにより、複雑度が下がります。
**Before（複雑度 = 6）**

```javascript
function processPayment(payment) {
  if (payment.type === 'credit') {
    return processCreditCard(payment);
  } else if (payment.type === 'debit') {
    return processDebitCard(payment);
  } else if (payment.type === 'bank') {
    return processBankTransfer(payment);
  }
}
```

**After（複雑度 = 1）：**

```javascript
const paymentProcessors = {
  credit: processCreditCard,
  debit: processDebitCard,
  bank: processBankTransfer
};

function processPayment(payment) {
  const processor = paymentProcessors[payment.type];
  return processor ? processor(payment) : throw new Error('Unknown payment type');
}
```
## 測定ツール
ここでは、循環的複雑度の計算方法について説明してきました。
実際の関数は複雑であり、手計算は現実的ではありません。各言語でツールが開発されており、そのツールを利用するのがよいです。

- **JavaScript/TypeScript**: ESLint
- **Python**: radon
- **Java**: SonarQube / PMD
- **Go**: gocyclo

## まとめ

循環的複雑度は、コードの品質を数値で測れる指標です。**チーム開発では「このコードは複雑すぎる」という主観的な意見ではなく、「複雑度が15だからリファクタリングが必要」と客観的に判断できます**。複雑度の計算方法を理解すれば、コードレビューでも「なぜこの関数は複雑なのか」を論理的に説明できるようになり、より建設的な議論ができるようになります。

