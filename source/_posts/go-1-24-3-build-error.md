---
title: Go 1.24.3アップデート後のビルドエラーと解決方法
date: 2025-05-30 16:03:12
tags:
  - Go
  - トラブルシューティング
categories:
  - プログラミング
---

## はじめに

最近Go 1.24.3へアップデートしたところ、ローカル環境でビルドエラーが発生するようになりました。この記事では、発生した問題と解決方法について共有します。

## 発生した問題

Go 1.24.3にアップデートした後、以下のようなエラーメッセージが表示されるようになりました。

```
link: duplicated definition of symbol dlopen, from github.com/ebitengine/purego and github.com/ebitengine/purego (exit status 1)
```

## 原因

調査の結果、このエラーはMacのIntel CPU（darwin/amd64）環境で発生する既知の問題であることがわかりました。Go公式リポジトリでも同様の問題が報告されています。

[Github Issue #73617: cmd/link: Go 1.24.3 and 1.23.9 regression - duplicated definition of symbol dlopen](https://github.com/golang/go/issues/73617)

## 解決方法

この問題は`github.com/ebitengine/purego`の最新バージョン（v0.8.3）で修正されています。以下のコマンドでpuregoをアップデートすることで解決できます。

```bash
go get github.com/ebitengine/purego@v0.8.3
```

アップデート後、ビルドが正常に完了することを確認しました。

## まとめ

Go言語のバージョンアップ後にはライブラリとの互換性の問題が発生することがあります。特にGoのマイナーバージョンアップデート（1.24.2→1.24.3など）でも互換性の問題が生じる可能性があるため、ビルドエラーが発生した場合は依存ライブラリの更新も検討するとよいでしょう。

## 参考リンク

- [Go公式リポジトリのIssue #73617](https://github.com/golang/go/issues/73617)
- [purego v0.8.3リリースノート](https://github.com/ebitengine/purego/releases/tag/v0.8.3)
