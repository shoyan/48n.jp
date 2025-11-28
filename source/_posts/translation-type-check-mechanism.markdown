---
title: TypeScriptで多言語システムの型チェックを実装する
date: 2025-11-28 18:07:53
tags:
  - TypeScript
  - プログラミング
categories:
  - プログラミング
description: 多言語対応におけるTypeScriptの型チェックの仕組みについて説明します。翻訳キーのタイポや必須キーの欠落をコンパイル時に防ぐ方法を解説します。
---


## 概要

多言語対応は一般的に翻訳関数に翻訳キーを渡すことで実装されます。その際に問題になるのが、キー名の間違い、キー名の変更漏れなどによる実装不備です。このようなエラーを人の目で全て確認するのは難しいため、システムによる検知が有効です。

この記事では、TypeScriptの型システムを活用した、型チェックの方法を説明します。これにより、正しい翻訳キーが割り当てられていることがコンパイル時にチェックされ、アプリケーションの品質向上に大きな効果が期待できます。

## 多言語システムの実装

### 1. ベースとなる型の定義

翻訳の型チェックは、日本語のメッセージファイル（`ja.ts`）をベースとして構築します。翻訳ファイルはJSONファイルで扱われることもありますが、型チェックという観点ではtsファイルで管理するのがおすすめです。

```typescript
import { jaMessage } from './ja'

export type JaMessage = typeof jaMessage
```

#### TypeScriptの機能：`typeof`演算子

`typeof`演算子は、値から型を抽出するTypeScriptの機能です。`typeof jaMessage`により、`jaMessage`オブジェクトの構造から型を自動的に生成します。これにより、`jaMessage`の構造が変更されると、型定義も自動的に更新されます。


#### `ja.ts`のサンプルコード

以下は、日本語メッセージファイル（`ja.ts`）のサンプルです。このファイルが型定義のベースとなります。

```typescript
export const jaMessage = {
  term: {
    next: '次へ',
    back: '戻る',
    cancel: 'キャンセル',
    submit: '送信',
  },
  pages: {
    home: {
      title: 'ホーム',
    },
    about: {
      title: '紹介',
    },
    contact: {
      title: 'お問い合わせ',
    },
    terms: {
      title: '利用規約',
    },
  },
} as const
```

このファイルでは、`as const`を使用することで、値がリテラル型として推論されます。これにより、型の精度が向上し、より厳密な型チェックが可能になります。

#### as constとは

`as const`は、TypeScriptの「constアサーション」という機能です。オブジェクトや配列に`as const`を付けることで、以下の効果が得られます。

1. **リテラル型として推論される**: 値が具体的なリテラル型（例：`'マイページ'`）として推論されます
2. **読み取り専用になる**: オブジェクトのプロパティが`readonly`になります
3. **型の精度が向上する**: より具体的な型情報が保持されます

#### as constがない場合

`as const`がない場合、TypeScriptは値の型を一般的な型として推論します。

```typescript
// as const がない場合
export const jaMessage = {
  term: {
    next: '次へ',
    back: '戻る',
  },
}

// 推論される型
// {
//   term: {
//     next: string;  // 一般的な string 型
//     back: string;  // 一般的な string 型
//   }
// }
```

この場合、`typeof jaMessage`で取得できる型は、値が`string`型として推論されます。

#### `as const`がある場合

`as const`がある場合、TypeScriptは値の型をリテラル型として推論します。

```typescript
// as const がある場合
export const jaMessage = {
  term: {
    next: '次へ',
    back: '戻る',
  },
} as const

// 推論される型
// {
//   readonly term: {
//     readonly next: '次へ';         // リテラル型
//     readonly back: '戻る';         // リテラル型
//   }
// }
```

この場合、`typeof jaMessage`で取得できる型は、値が具体的なリテラル型（`'次へ'`、`'戻る'`）として推論されます。

#### なぜ`as const`が必要なのか

翻訳メッセージの型チェックにおいて、`as const`は以下の理由で重要です。

1. **型の一貫性**: `typeof jaMessage`で取得した型が、実際の値の構造を正確に反映します
2. **型の精度**: `GenericType`が適用される際、元の構造が正確に保持されます
3. **読み取り専用の保証**: 翻訳メッセージが誤って変更されることを防ぎます

#### 実際の違い

`as const`の有無による型の違いを確認してみましょう。

```typescript
// as const がない場合
const message1 = { term: { next: '次へ' } }
type Type1 = typeof message1
// Type1 = { term: { next: string } }

// as const がある場合
const message2 = { term: { next: '次へ' } } as const
type Type2 = typeof message2
// Type2 = { readonly term: { readonly next: '次へ' } }
```

`as const`がある場合、`next`の型は`'次へ'`という具体的なリテラル型になります。これにより、型システムがより正確に動作し、翻訳キーの型チェックがより厳密になります。

### 2. 再帰的な型変換

翻訳メッセージはネストされたオブジェクト構造を持っています。この構造を型として表現するために、`GenericType`というユーティリティ型を使用しています。

```typescript
type GenericType<T extends object> = {
  [K in keyof T]: T[K] extends object ? GenericType<T[K]> : string
}

export type I18nMessage = GenericType<JaMessage>
```

#### TypeScriptの機能：条件型（Conditional Types）と再帰的な型

- `[K in keyof T]`: マップ型（Mapped Types）により、オブジェクトの各キーを反復処理します

- `T[K] extends object ? GenericType<T[K]> : string`: 条件型により、値がオブジェクトの場合は再帰的に`GenericType`を適用し、そうでない場合は`string`型とします

これにより、以下のような構造が正しく型付けされます。

```typescript
{
  term: {
    next: string  // 文字列型
    back: string
    cancel: string
    submit: string
  },
  pages: {
    home: {
      title: string  // 3段階のネストも正しく型付けされる
    },
    about: {
      title: string
    },
    contact: {
      title: string
    },
    terms: {
      title: string
    }
  }
}
```

`GenericType`は再帰的に動作するため、何段階のネストでも正しく型付けされます。

### 3. 各言語ファイルでの型チェック

各言語ファイルでは、`I18nMessage`型を明示的に指定することで、型チェックが行われます。

```typescript
import { I18nMessage } from './types'

export const enMessage: I18nMessage = {
  term: {
    next: 'Next',
    back: 'Back',
    cancel: 'Cancel',
    submit: 'Submit',
  },
  pages: {
    home: {
      title: 'Home',
    },
    about: {
      title: 'About',
    },
    contact: {
      title: 'Contact',
    },
    terms: {
      title: 'Terms',
    },
  },
}
```

**型チェックの効果**

- 必須キーが欠落している場合、コンパイルエラーが発生します

- 存在しないキーを追加しようとすると、エラーが発生します

- 値の型が`string`でない場合、エラーが発生します

- キーのタイポがある場合、エラーが発生します

### 4. 翻訳関数の型安全な実装

翻訳関数はライブラリを利用してもよいですが、シンプルな翻訳システムであれば自前で実装するのもよいと思います。ここでは、必要最低限の翻訳関数のサンプルを紹介します。

翻訳関数についても型安全を担保する必要があります。型システムを実装するために、`Paths`型と`PathValue`型を使用します。

#### キーパスの型生成

```typescript
type PathsInternal<T> = T extends string
  ? never
  : T extends object
  ? {
      [K in keyof T]: K extends string
        ? T[K] extends object
          ? T[K] extends string
            ? K
            : `${K}.${PathsInternal<T[K]>}`
          : K
        : never
    }[keyof T]
  : never

export type Paths = PathsInternal<JaMessage>
```

この型により、`'term.next'`、`'pages.home.title'`のような文字列リテラル型が自動生成されます。

#### キーパスから値を取得する型

```typescript
export type PathValue<T, P extends string> = P extends `${infer Key}.${infer Rest}`
  ? Key extends keyof T
    ? T[Key] extends object
      ? PathValue<T[Key], Rest>
      : never
    : never
  : P extends keyof T
  ? T[P] extends string
    ? T[P]
    : never
  : never
```

この型により、キーパスから対応する文字列型を取得できます。

#### 翻訳関数の実装

```typescript
import { I18nMessage, Paths, PathValue } from './types'

export type I18nFunction = <P extends Paths>(
  key: P
) => PathValue<I18nMessage, P>

export function createI18n(message: I18nMessage): I18nFunction {
  return function <P extends Paths>(key: P & string): PathValue<I18nMessage, P> {
    const keys = (key as string).split('.')
    let value: any = message

    for (const k of keys) {
      if (value && typeof value === 'object' && k in value) {
        value = value[k as keyof typeof value]
      } else {
        throw new Error(`Translation key "${key}" not found`)
      }
    }

    if (typeof value !== 'string') {
      throw new Error(`Translation key "${key}" does not point to a string value`)
    }

    return value as PathValue<I18nMessage, P>
  }
}
```

## 使用例

翻訳関数の使用例です。

### 翻訳関数の使用

```typescript
import { createI18n } from './i18n'
import { jaMessage } from './ja'
import { enMessage } from './en'

// 日本語の翻訳関数を作成
const tJa = createI18n(jaMessage)

// 英語の翻訳関数を作成
const tEn = createI18n(enMessage)

// 使用例
console.log(tJa('term.next')) // '次へ'
console.log(tJa('pages.home.title')) // 'ホーム'
console.log(tJa('pages.about.title')) // '紹介'
console.log(tJa('pages.contact.title')) // 'お問い合わせ'
console.log(tJa('pages.terms.title')) // '利用規約'

console.log(tEn('term.next')) // 'Next'
console.log(tEn('pages.home.title')) // 'Home'
console.log(tEn('pages.about.title')) // 'About'
console.log(tEn('pages.contact.title')) // 'Contact'
console.log(tEn('pages.terms.title')) // 'Terms'
```

### 正しい使用例

```typescript
export const enMessage: I18nMessage = {
  term: {
    next: 'Next', // ✅ 正しい
    back: 'Back',
    cancel: 'Cancel',
    submit: 'Submit',
  },
  pages: {
    home: {
      title: 'Home',  // ✅ 3段階のネストも正しい
    },
    about: {
      title: 'About',
    },
    contact: {
      title: 'Contact',
    },
    terms: {
      title: 'Terms',
    },
  },
}
```

### エラーが発生する例

```typescript
export const enMessage: I18nMessage = {
  term: {
    next: 'Next',
    // ❌ エラー: 'back' プロパティが必須だが欠落している
  },
}

// ❌ エラー: 'unknownKey' は存在しないキー
export const enMessage: I18nMessage = {
  term: {
    next: 'Next',
    unknownKey: 'Value',  // エラー
  },
}

// ❌ エラー: 翻訳関数で存在しないキーを指定
const t = createI18n(enMessage)
t('term.invalidKey')      // コンパイルエラー
t('pages.home.invalid')   // コンパイルエラー
t('invalid.path')         // コンパイルエラー
t('pages.invalid.title')  // コンパイルエラー
```

## メリット

1. **コンパイル時の安全性**: 翻訳キーのエラーを実行前に検出できます

2. **自動補完**: IDEが翻訳キーを自動補完してくれます

3. **リファクタリングの安全性**: キー名を変更する際、使用箇所を自動的に検出できます

4. **ドキュメントとしての役割**: 型定義自体が、利用可能な翻訳キーのドキュメントとなります

5. **翻訳関数の型安全**: 翻訳関数を使用する際も、存在しないキーを指定するとコンパイルエラーが発生します


## サンプルコード

サンプルコードは https://github.com/shoyan/i18n-sample で公開しています。
