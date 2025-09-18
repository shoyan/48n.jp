---
title: Goのインターフェース実装チェックイディオムを理解する
date: 2025-09-17 12:30:00
tags:
  - Go
categories:
  - プログラミング
---

## はじめに

Goを書いていると、以下のようなコードを見かけることがあります。

```go
var _ Interface = (*Type)(nil)
```

この一見奇妙なコードは、Goのインターフェース実装をコンパイル時にチェックするためのイディオムです。今回は、このイディオムの目的、必要性、そして適切な使用場面について詳しく解説します。

## このイディオムの目的

### 基本的な仕組み

もう少し具体的なコードで説明してみます。以下のコードは、Counterインターフェースを実装していることをチェックするイディオムです。

```go
var _ Counter = (*CountRepository)(nil)
```

このコードは以下の要素で構成されています：

- `var _` : 空白識別子（blank identifier）を使用した変数宣言
- `Counter` : チェック対象のインターフェース
- `(*CountRepository)(nil)` : チェック対象の型（nilポインタ）

### 何をしているのか

このイディオムは、`*CountRepository` 型が `Counter` インターフェースを実装しているかを**コンパイル時に**チェックします。

## 実際の動作例

### 正常な場合

以下のコードは `Counter` インターフェースを実装したサンプルコードです。

```go
package main

import (
	"context"
	"fmt"
)

type Counter interface {
    Increment(ctx context.Context, key string) (int, error)
    Get(ctx context.Context, key string) (int, error)
    Reset(ctx context.Context, key string) error
}

type CountRepository struct {
}

// すべてのメソッドを実装
func (r *CountRepository) Increment(ctx context.Context, key string) (int, error) {
    // 実装
    return 0, nil
}

func (r *CountRepository) Get(ctx context.Context, key string) (int, error) {
    // 実装
    return 0, nil
}

func (r *CountRepository) Reset(ctx context.Context, key string) error {
    // 実装
    return nil
}

// インターフェース実装チェック
var _ Counter = (*CountRepository)(nil)

func main() {
    // インターフェース実装チェックのサンプル
    repo := &CountRepository{}
    ctx := context.Background()
    
    // メソッド呼び出し
    count, _ := repo.Increment(ctx, "test")
    fmt.Printf("Count: %d\n", count)
}
```

CountRepositoryはCounterインターフェースで定義してある、Inctement、Get、Resetが実装してあるため、ビルドコマンドでビルドすることができます。

```
go build sample.go
```

### エラーの場合

エラーを発生させるために、`Counter` インターフェースにDecrementを追加してみましょう。

```go
type Counter interface {
    Increment(ctx context.Context, key string) (int, error)
    Get(ctx context.Context, key string) (int, error)
    Reset(ctx context.Context, key string) error
    Decrement(ctx context.Context, key string) (int, error)
}
```

ビルドすると、ビルドエラーが発生します。インターフェースの未実装が検知されていますね。

**コンパイルエラーメッセージ：**
```
./sample.go:35:17: cannot use (*CountRepository)(nil) (value of type *CountRepository) as Counter value in variable declaration: *CountRepository does not implement Counter (missing method Decrement)
```

では、イディオムをコメントアウトしてみるとどうでしょうか。

```go
// インターフェース実装チェック
// var _ Counter = (*CountRepository)(nil)
```

ビルドすると、エラーが発生しなくなりました。これは、コンパイル時にインターフェースの未実装が検知されていないことを意味しています。

## なぜこのイディオムが必要なのか

このイディオムですが、通常は不要な場合がほとんどです。
例えば、以下のように `NewCounter` 関数を追加して、`CountRepository` は `Counter` インターフェースであるということを明示的に指定した場合、GOのコンパイラでインターフェースの実装を検知することができます。

```go
type CountRepository struct {
}

// Counterインターフェースという型が明示されている
func NewCounter() Counter {
    return &CountRepository{}
}
```

## エフェクティブGoの指針

エフェクティブGoでは、このイディオムについて以下のように述べています。

> The appearance of the blank identifier in this construct indicates that the declaration exists only for the type checking, not to create a variable. Don't do this for every type that satisfies an interface, though. By convention, such declarations are only used when there are no static conversions already present in the code, which is a rare event.

### 重要なポイント

1. **空白識別子の役割** : 変数を作成せず、型チェックのみを目的とする
2. **乱用すべきではない** : すべてのインターフェース実装に使うべきではない
3. **静的変換がない場合のみ** : 既に型チェックの機会がある場合は不要

使いどころは、**静的変換がない場面** です。

## 静的変換とは

**静的変換（static conversion）** とは、コンパイル時に型の互換性をチェックする機会のことです。

### 静的変換がある場合（イディオムは不要）

```go
func Process(repo Counter) {  // ← 静的変換が発生
    // 処理
}

func main() {
    r := &CountRepository{}
    Process(r)  // ← ここで型チェックが行われる
    // そのため、var _ は不要
}
```

### 静的変換がない場合（イディオムが有用）

```go
// インターフェースと実装が分離されていて、
// まだ使用箇所がない場合
type CountRepository struct{}

// この時点では静的変換が発生していない
// そのため、明示的に型チェックを行う
var _ Counter = (*CountRepository)(nil)
```

## 適切な使用場面

### 1. リポジトリパターン

```go
// domain/repository/user.go
type UserRepository interface {
    Get(ctx context.Context, id string) (*User, error)
    Create(ctx context.Context, user *User) error
}

// infrastructure/repository/user.go
type UserRepository struct {
    db *gorm.DB
}

// リポジトリパターンでは、インターフェースと実装が分離されているため有用
var _ repository.UserRepository = (*UserRepository)(nil)
```

### 2. プラグインアーキテクチャ

```go
// plugin/interface.go
type Plugin interface {
    Initialize() error
    Execute() error
}

// plugin/example.go
type ExamplePlugin struct{}

// プラグインが正しくインターフェースを実装しているかをチェック
var _ Plugin = (*ExamplePlugin)(nil)
```

## 不適切な使用例
### 既に静的変換がある場合

既に静的変換がある場合はイディオムは必要ありません。

```go
// 既に使用箇所がある場合
func NewUserService(repo UserRepository) *UserService {
    return &UserService{repo: repo}
}

// この場合、NewUserServiceの呼び出し時に既に型チェックが行われるため
// var _ は不要
```

## まとめ

`var _ Interface = (*Type)(nil)` イディオムは **静的変換がない場合のみ** 使用すればOKです。
