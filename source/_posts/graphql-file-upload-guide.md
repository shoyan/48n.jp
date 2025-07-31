---
layout: post
title: "GraphQLガイド - ファイルアップロード"
date: 2025-07-31 15:00:00
updated: 2025-07-31 15:00:00
comments: true
tags: 
  - GraphQL
description: "GraphQLでファイルアップロードを実装する方法について詳しく解説します。Base64エンコード方式とmultipart/form-data方式の2つのアプローチを比較し、それぞれの実装の特徴と基本的なセキュリティについて紹介します。"
---

GraphQLでファイルアップロードを実装する場合、いくつかの方法があります。GraphQL自体はバイナリデータを直接扱う仕様にはなっていませんが、工夫次第で実現可能です。

この記事では、GraphQLでのファイルアップロード実装について詳しく解説し、適切な方法を選択できるようにします。

## バイナリデータの取り扱いについて

GraphQLの標準プロトコルはJSONのみをサポートしており、JSONはバイナリデータを直接扱えません。しかし、**工夫次第でバイナリデータの送受信は可能**です。主な方法は以下の2つです。

### **1. Base64エンコードで送信**

- バイナリデータをBase64文字列に変換し、GraphQLのString型で送受信します
- 小さなファイルや簡易的な用途には使えますが、ファイルサイズが大きいと効率が悪くなります

```javascript
// クライアント側
const file = document.getElementById('fileInput').files[0];
const reader = new FileReader();
reader.onload = function() {
  const base64 = reader.result.split(',')[1]; // data:image/jpeg;base64, の部分を除去
  
  const mutation = `
    mutation UploadFile($file: String!) {
      uploadFile(file: $file) {
        id
        url
        filename
      }
    }
  `;
  
  // GraphQLリクエストを送信
  fetch('/graphql', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      query: mutation,
      variables: { file: base64 }
    })
  });
};
reader.readAsDataURL(file);
```

### **2. GraphQL multipart request specification（ファイルアップロード）**

- Apollo Serverやgraphql-uploadなどのライブラリを使うことで、**multipart/form-data**形式でファイルアップロードが可能です
- これはHTTPの拡張で、GraphQLのmutationと一緒にバイナリファイルを送信できます
- サーバー側でgraphql-uploadなどのミドルウェアが必要です

```javascript
// クライアント側
const file = document.getElementById('fileInput').files[0];
const formData = new FormData();

formData.append('operations', JSON.stringify({
  query: `
    mutation UploadFile($file: Upload!) {
      uploadFile(file: $file) {
        id
        url
        filename
      }
    }
  `,
  variables: { file: null }
}));

formData.append('map', JSON.stringify({
  "0": ["variables.file"]
}));

formData.append('0', file);

fetch('/graphql', {
  method: 'POST',
  body: formData
});
```

## multipart/form-dataとは

multipart/form-dataは、HTTPリクエストで**テキストデータとバイナリデータ（ファイル）を同時に送信**するためのMIMEタイプです。HTMLの`<form>`要素でファイルアップロードを行う際に使用されます。

### **基本的な構造**

**1. Content-Typeヘッダー**

```
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
```

- boundaryは、データの境界を区切る文字列です
- ブラウザやクライアントが自動生成します

**2. リクエストボディの構造**

```
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="text_field"

Hello World
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="image.jpg"
Content-Type: image/jpeg

[バイナリデータ]
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

**3. 実際のHTTPリクエスト例**

```
POST /graphql HTTP/1.1
Host: api.example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="operations"

{"query":"mutation UploadFile($file: Upload!) { uploadFile(file: $file) { id url filename } }","variables":{"file":null}}
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="map"

{"0":["variables.file"]}
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="0"; filename="document.pdf"
Content-Type: application/pdf

[PDFファイルのバイナリデータ]
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

**4. GraphQL multipart request specificationの構造**

1. **operations**: GraphQLクエリのJSON
2. **map**: ファイルとGraphQL変数のマッピング
3. **0, 1, 2...**: 実際のファイルデータ

## Base64エンコードとバイナリの比較

| 項目 | Base64エンコード | バイナリ（multipart/form-data） |
|------|------------------|--------------------------------|
| データサイズ | 元サイズ + 33% | 元サイズのまま |
| メモリ使用量 | 約33%増加 | 元サイズのまま |
| 転送時間 | 約33%増加 | 元サイズのまま |
| CPU使用量 | エンコード/デコード処理が必要 | 処理不要 |
| 実装の複雑さ | 簡単 | やや複雑 |
| 適している用途 | 小さなデータ（QRコード、アイコン） | 大きなファイル（画像、動画、PDF） |

## セキュリティ
ファイルアップロード処理における、基本的なセキュリティ対策を紹介します。

### 1. ファイルサイズの制限
サーバー側でファイルサイズを検証します。

### 2. ファイルタイプの検証
ホワイトリスト方式で .jpg, .png, .pdf のように明示的に許可された拡張子のみ受付します。
また、クライアントから送られたContent-Typeではなく、サーバー側でMIMEタイプを再検査します。

### 3. ファイル名のリネーム
アップロードされたファイルをランダム名に変えて保存（例：upload_48cd1e2a.jpg）します。

## まとめ

GraphQLでのファイルアップロードは、Base64エンコード方式とmultipart/form-data方式の2つのアプローチがあります。

- **Base64エンコード**: 小さなファイルや簡易的な用途に適している
- **multipart/form-data**: 大きなファイルや本格的なファイルアップロード機能に適している

用途に応じて適切な方法を選択し、ファイルサイズ制限やファイルタイプ検証などのセキュリティ対策も忘れずに実装することが重要です。 