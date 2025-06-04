---
title: GoでSlack通知を実装する方法
date: 2025-06-04 10:30:00
tags:
  - Go
  - Slack
  - API
  - 通知
categories:
  - プログラミング
---

## はじめに

Slackは現代のチーム開発において欠かせないコミュニケーションツールです。システムからの通知やアラート、定期的なレポートなど、様々な情報をSlackに送信することで、チーム全体での情報共有を効率化できます。

この記事では、Goを使ってSlackに通知を送信する方法を、基本的なテキストメッセージから高度なBlock Kitを使ったリッチなメッセージまで、実際のコード例とともに解説します。

## 使用するライブラリ

今回は、[`github.com/slack-go/slack`](https://github.com/slack-go/slack)というGoの公式Slackクライアントライブラリを使用します。このライブラリは活発に開発されており、Slack APIの最新機能もサポートしています。

## セットアップ

### 1. Slackアプリの作成とトークンの取得

まず、[Slack API](https://api.slack.com/apps)でアプリを作成し、Bot User OAuth Tokenを取得する必要があります。

1. Slack APIのページにアクセス
2. "Create New App" → "From scratch"でアプリを作成
3. "OAuth & Permissions"から必要な権限を設定
   - `users:read` - ユーザー一覧の取得
   - `chat:write` - メッセージの送信
   - `im:write` - ダイレクトメッセージの送信
4. Bot User OAuth Tokenをコピー

### 2. 環境変数の設定

取得したトークンを環境変数として設定します。

```bash
export SLACK_BOT_TOKEN="xoxb-your-token-here"
```

## 基本的な実装

### Slackクライアントの初期化

```go
package main

import (
    "log"
    "os"
    "github.com/slack-go/slack"
)

func main() {
    // Slack APIトークンを環境変数から取得
    token := os.Getenv("SLACK_BOT_TOKEN")
    if token == "" {
        log.Fatal("SLACK_BOT_TOKEN is not set")
    }

    // Slackクライアントの初期化
    api := slack.New(token)
}
```

### ユーザー一覧の取得

通知を送信する前に、まずワークスペース内のユーザー一覧を取得してみましょう。

```go
func getUsers(api *slack.Client) {
    // ユーザー一覧を取得
    users, err := api.GetUsers()
    if err != nil {
        log.Fatalf("ユーザー一覧の取得に失敗しました: %v", err)
    }

    // ユーザー情報を表示
    log.Printf("ワークスペース内のユーザー数: %d\n", len(users))
    for i, user := range users {
        // 必要な情報だけを表示（すべての情報を表示するとログが長くなりすぎるため）
        log.Printf("%d: ID=%s, Name=%s, RealName=%s, IsBot=%v\n",
            i+1, user.ID, user.Name, user.RealName, user.IsBot)
    }
}
```

### シンプルなダイレクトメッセージの送信

ユーザーIDを指定して、シンプルなテキストメッセージを送信する関数です。

```go
func sendDirectMessage(api *slack.Client, userID, message string) error {
    // ユーザーとのDMチャンネルを開く
    channel, _, _, err := api.OpenConversation(&slack.OpenConversationParameters{
        Users: []string{userID},
    })
    if err != nil {
        return fmt.Errorf("DMチャンネルを開けませんでした: %v", err)
    }

    // DMを送信
    _, _, err = api.PostMessage(
        channel.ID,
        slack.MsgOptionText(message, false),
    )
    if err != nil {
        return fmt.Errorf("DMの送信に失敗しました: %v", err)
    }

    log.Printf("ユーザー %s にDMを送信しました", userID)
    return nil
}
```

## Block Kitを使った高度なメッセージ

Slack Block Kitを使用すると、画像、ボタン、フォーマットされたテキストなどを含む、より視覚的にリッチなメッセージを作成できます。

### Block Kitメッセージの構築

以下は、様々なBlock Kit要素を含むサンプルメッセージの構築例です。

```go
func buildSampleBlockKit() slack.MsgOption {
    // Block Kitを使ったメッセージの構築
    headerText := slack.NewTextBlockObject("mrkdwn", "*ブロックキットのサンプル*", false, false)
    headerSection := slack.NewSectionBlock(headerText, nil, nil)

    // テキストブロック
    textBlock := slack.NewTextBlockObject("mrkdwn", "これは `Block Kit` を使ったメッセージです。\n*太字* や _斜体_ などのMarkdownも使えます！", false, false)
    textSection := slack.NewSectionBlock(textBlock, nil, nil)

    // 画像ブロック
    accessory := slack.NewImageBlockElement("https://api.slack.com/img/blocks/bkb_template_images/beagle.png", "犬の画像")
    imageText := slack.NewTextBlockObject("mrkdwn", "こちらはかわいい犬の画像です :dog:", false, false)
    imageSection := slack.NewSectionBlock(imageText, nil, slack.NewAccessory(accessory))

    // ボタン要素
    btnText := slack.NewTextBlockObject("plain_text", "クリックしてください", false, false)
    btn := slack.NewButtonBlockElement("click_button", "button_clicked", btnText)
    btnAccessory := slack.NewAccessory(btn)

    // ボタンセクション
    btnSectionText := slack.NewTextBlockObject("mrkdwn", "アクションを実行するには:", false, false)
    btnSection := slack.NewSectionBlock(btnSectionText, nil, btnAccessory)

    // 区切り線
    divider := slack.NewDividerBlock()

    // フッターブロック
    footerText := slack.NewTextBlockObject("mrkdwn", "Block Kitの詳細は <https://api.slack.com/block-kit|こちら> をご覧ください", false, false)
    footerSection := slack.NewSectionBlock(footerText, nil, nil)

    // すべてのブロックを一つのメッセージにまとめる
    return slack.MsgOptionBlocks(
        headerSection,
        divider,
        textSection,
        imageSection,
        divider,
        btnSection,
        divider,
        footerSection,
    )
}
```

### Block Kitメッセージの送信

構築したBlock Kitメッセージを送信する関数です。

```go
func sendDirectMessageWithBlocks(api *slack.Client, userID string, blocks slack.MsgOption) error {
    // ユーザーとのDMチャンネルを開く
    channel, _, _, err := api.OpenConversation(&slack.OpenConversationParameters{
        Users: []string{userID},
    })
    if err != nil {
        return fmt.Errorf("DMチャンネルを開けませんでした: %v", err)
    }

    // ブロックキットメッセージを送信
    _, _, err = api.PostMessage(
        channel.ID,
        blocks,
    )
    if err != nil {
        return fmt.Errorf("DMの送信に失敗しました: %v", err)
    }

    log.Printf("ユーザー %s にブロックキットDMを送信しました", userID)
    return nil
}
```

## 完全なサンプルコード

以下が、これまでの機能をすべて含んだ完全なサンプルです。

```go
func main() {
    // Slack APIトークンを環境変数から取得
    token := os.Getenv("SLACK_BOT_TOKEN")
    if token == "" {
        log.Fatal("SLACK_BOT_TOKEN is not set")
    }

    // Slackクライアントの初期化
    api := slack.New(token)

    // ユーザー一覧を取得して表示
    getUsers(api)

    // 特定のユーザーにDMを送信
    userID := "U02K6JU8D" // 実際のユーザーIDに置き換えてください
    err := sendDirectMessage(api, userID, "これはテストメッセージです！")
    if err != nil {
        log.Fatalf("DMの送信に失敗しました: %v", err)
    }

    // ブロックキットを使用したDMを送信
    blocks := buildSampleBlockKit()
    err = sendDirectMessageWithBlocks(api, userID, blocks)
    if err != nil {
        log.Fatalf("ブロックキットDMの送信に失敗しました: %v", err)
    }
}
```

## 実行方法

1. 依存関係をインストール：
```bash
go mod tidy
```

2. 環境変数を設定：
```bash
export SLACK_BOT_TOKEN="xoxb-your-token"
```

3. プログラムを実行：
```bash
go run main.go
```

## 注意事項

1. **レート制限**: Slack APIにはレート制限があります。大量のメッセージを送信する場合は、適切な間隔を設けましょう。

2. **エラーハンドリング**: 本番環境では、ネットワークエラーやAPI制限エラーに対する適切なリトライ機構を実装することを推奨します。

3. **ユーザーID**: 実際の運用では、ユーザー名からユーザーIDを動的に取得する仕組みを構築することが一般的です。

4. **セキュリティ**: Slack APIトークンは機密情報です。ソースコードに直接記述せず、必ず環境変数や設定ファイルから読み込むようにしてください。

## まとめ

この記事では、Goを使ったSlack通知の実装方法を、基本的なテキストメッセージから高度なBlock Kitを使ったリッチなメッセージまで幅広く解説しました。

`github.com/slack-go/slack`ライブラリを使用することで、Slack APIの豊富な機能を簡単に利用できます。システム監視、定期レポート、チーム内通知など、様々な用途でSlack通知を活用して、より効率的なチーム開発を実現してください。

Block Kitの詳細については、[Slack Block Kit Builder](https://app.slack.com/block-kit-builder)で実際にブロックを構築しながら学習することをおすすめします。
