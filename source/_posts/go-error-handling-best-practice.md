---
title: Goのエラーハンドリングのベストプラクティス
date: 2025-05-30 12:33:47
description: Goのエラーハンドリングのベストプラクティスを解説。errors.Is()、errors.As()、エラーのラッピングとアンラッピング、カスタムエラーの作成方法について詳しく説明します。
keywords: Go,エラーハンドリング,errors.Is,errors.As,プログラミング,ベストプラクティス
tags:
  - Go
  - エラーハンドリング
  - プログラミング
categories:
  - プログラミング
---
## はじめに

Goのエラーハンドリングは他の言語と少し異なるアプローチを取ります。例外機構を持たないGoでは、エラーは値として扱われ、関数の戻り値として明示的に返されます。このシンプルなアプローチは強力ですが、効果的に活用するにはいくつかのベストプラクティスを理解する必要があります。

この記事では、Goのエラーハンドリングの基本から、カスタムエラーの作成、エラーのラッピングとアンラッピング、そして`errors.Is()`と`errors.As()`を使用した効果的なエラー判定の方法について解説します。

## Goのエラーハンドリングのベストプラクティス

Goのエラーハンドリングのベストプラクティスを理解するために、いくつかの具体例を見ていきましょう。

### 1. 基本的なエラー定義と判定

Goでは、標準パッケージの`errors`を使って簡単にエラーを定義できます。以下のサンプルコードでは、よく使われるパターンを示しています。

```go
package main

import (
	"errors"
	"fmt"
)

// カスタムエラー型の定義
var (
	ErrNotFound = errors.New("not found")
	ErrInvalid  = errors.New("invalid input")
)

// データベース操作を模擬する関数
func findUser(id string) error {
	// エラーをラップして返す
	return fmt.Errorf("failed to find user: %w", ErrNotFound)
}

type NotFoundError struct {
	ID string
}

func (e *NotFoundError) Error() string {
	return fmt.Sprintf("user %s not found", e.ID)
}

func main() {
	// 悪い例: == 演算子での比較
	err := findUser("123")
	if err == ErrNotFound { // これは動作しない！
		fmt.Println("== で比較。ユーザーが見つかりませんでした")
	}

	// 良い例1: errors.Is() を使用
	err = findUser("123")
	if errors.Is(err, ErrNotFound) {
		fmt.Println("errors.Is() で比較。ユーザーが見つかりませんでした")
	}

	err = &NotFoundError{ID: "123"}
	var notFoundErr *NotFoundError
	if errors.As(err, &notFoundErr) {
		fmt.Printf("ユーザー %s が見つかりませんでした\n", notFoundErr.ID)
	}

	// エラーのラップと展開の例
	err = findUser("123")
	fmt.Printf("元のエラー: %v\n", err)

	// ラップされたエラーを展開
	unwrapped := errors.Unwrap(err)
	fmt.Printf("展開されたエラー: %v\n", unwrapped)
}
```

実行結果
```bash
errors.Is() で比較。ユーザーが見つかりませんでした
ユーザー 123 が見つかりませんでした
元のエラー: failed to find user: not found
展開されたエラー: not found
```

### 2. エラー判定の種類と使い分け

上記のコードには、エラー判定のための重要な方法がいくつか含まれています。それぞれを詳しく解説します。

#### エラー比較の落とし穴: `==` 演算子

```go
// 悪い例: == 演算子での比較
err := findUser("123")
if err == ErrNotFound { // これは動作しない！
    fmt.Println("== で比較。ユーザーが見つかりませんでした")
}
```

このコードでは、`findUser`関数が返すエラーと`ErrNotFound`を`==`演算子で直接比較しています。しかし、このアプローチには大きな問題があります。`findUser`関数は単純に`ErrNotFound`を返すのではなく、`fmt.Errorf("failed to find user: %w", ErrNotFound)`を使ってエラーをラップしているため、`==`比較は失敗します。ラップされたエラーは元のエラーと等価ではないからです。

#### 推奨方法1: `errors.Is()`

```go
// 良い例1: errors.Is() を使用
err = findUser("123")
if errors.Is(err, ErrNotFound) {
    fmt.Println("errors.Is() で比較。ユーザーが見つかりませんでした")
}
```

Go 1.13以降で導入された`errors.Is()`関数は、エラーチェーン内のどこかに特定のエラー値が含まれているかを確認します。これにより、ラップされたエラーでも正しく比較できるようになります。`errors.Is()`は、エラーが同一かどうかを確認する際の推奨方法です。

#### 推奨方法2: `errors.As()`

```go
err = &NotFoundError{ID: "123"}
var notFoundErr *NotFoundError
if errors.As(err, &notFoundErr) {
    fmt.Printf("ユーザー %s が見つかりませんでした\n", notFoundErr.ID)
}
```

`errors.As()`関数は、エラーチェーン内のいずれかのエラーが特定の型に一致するかを確認し、一致する場合はその値をターゲット変数に設定します。これは、エラーの型に基づいて処理を分岐させたい場合や、エラー内の追加情報（この例では`ID`）にアクセスしたい場合に特に有用です。

### 3. エラーのラッピングとアンラッピング

```go
// エラーのラップと展開の例
err = findUser("123")
fmt.Printf("元のエラー: %v\n", err)

// ラップされたエラーを展開
unwrapped := errors.Unwrap(err)
fmt.Printf("展開されたエラー: %v\n", unwrapped)
```

Go 1.13では、`%w`動詞を使用して元のエラーをラップする機能が`fmt.Errorf`に追加されました。これにより、より詳細なコンテキスト情報を提供しながら、元のエラー値を保持できます。`errors.Unwrap()`関数を使用すると、ラップされたエラーから元のエラーを取り出すことができます。

### 4. カスタムエラー型の作成

```go
type NotFoundError struct {
    ID string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("user %s not found", e.ID)
}
```

Goでは、`Error()`メソッドを実装した任意の型をエラーとして使用できます。カスタムエラー型を作成することで、エラーに追加情報（この例では`ID`）を含めることができ、より詳細なエラーハンドリングが可能になります。

## まとめ

Goのエラー判定では、==演算子による比較はラップされたエラーに対して正しく動作しません。
そのため、Go1.13以降で導入された`errors.Is`や`errors.As`を活用することで、より安全かつ柔軟なエラーハンドリングが可能になります。
エラー処理の品質向上のため、ぜひこれらの手法を取り入れてみてください。
