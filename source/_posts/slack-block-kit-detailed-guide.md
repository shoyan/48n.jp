---
title: Slack Block Kitの制約とデザインパターンガイド
date: 2025-06-05 11:00:00
tags:
  - Slack
  - Block Kit
  - API
  - UI
  - デザイン
categories:
  - プログラミング
---

## はじめに

前回の記事「[GoでSlack通知を実装する方法](/2025/06/04/go-slack-notification-guide/)」では、Slack通知の基本的な実装方法とBlock Kitの初歩的な使い方について解説しました。

今回は、Slack Block Kitをより深く掘り下げて、その特性、制約、そして効果的なデザインパターンについて詳しく説明します。特に、Block Kitの「見た目があまり変えられない」という特性と、その制約の中でいかに美しく機能的なメッセージを作成するかに焦点を当てます。

## Slack Block Kitの特性と制約

### 1. デザインの統一性と制約

Slack Block Kitの最も大きな特徴は、**デザインの自由度が意図的に制限されている**ことです。これにはいくつかの理由があります。

- **一貫性の確保**: すべてのアプリからのメッセージが統一された見た目になる
- **可読性の向上**: Slackの標準UIパターンに従うことで、ユーザーが迷わない
- **アクセシビリティ**: スクリーンリーダーやキーボードナビゲーションに配慮

### 2. 制約の具体例

以下のような点でカスタマイズが制限されています。

- **色の変更**: テキストやブロックの背景色は基本的に変更不可
- **フォントサイズ**: 固定のフォントサイズ体系
- **余白・レイアウト**: ブロック間の余白やレイアウトは Slack側で制御
- **アニメーション**: 動的な効果は実装不可

これらの制約があるからこそ、**コンテンツの構成とブロックの組み合わせ**が重要になります。

## Block Kitの主要ブロックタイプ解説

実際のサンプルコードを基に、各ブロックタイプの特性と使用例を詳しく見てみましょう。

### 1. Header ブロック

```json
{
    "type": "header",
    "text": {
        "type": "plain_text",
        "text": "Slack Block Kitデザインシステム見本",
        "emoji": true
    }
}
```

**特徴:**
- メッセージの最上部に配置される大きなタイトル
- plain_textのみ対応（Markdownは使用不可）
- 絵文字を使用することで視覚的なアクセントを追加可能

**使用場面:**
- 重要な通知のタイトル
- ダッシュボードのセクション見出し
- システムアラートの分類

### 2. Section ブロック

```json
{
    "type": "section",
    "text": {
        "type": "mrkdwn",
        "text": "*1. 基本的なセクション*\nこれは基本的なセクションブロックです。*太字*や_斜体_、~取り消し線~、`コード`などのMarkdown書式が使えます。"
    }
}
```

**特徴:**
- 最も汎用的で使用頻度が高いブロック
- Markdownによる豊富なテキスト装飾
- accessoryフィールドで画像やボタンを右側に配置可能

**応用例 - 画像付きセクション:**
```json
{
    "type": "section",
    "text": {
        "type": "mrkdwn",
        "text": "画像を右側に配置できます"
    },
    "accessory": {
        "type": "image",
        "image_url": "https://api.slack.com/img/blocks/bkb_template_images/beagle.png",
        "alt_text": "かわいい犬の画像"
    }
}
```

### 3. Context ブロック

```json
{
    "type": "context",
    "elements": [
        {
            "type": "mrkdwn",
            "text": "👆 Contextブロックは小さいテキストで補足情報を表示するのに最適です"
        }
    ]
}
```

**特徴:**
- 小さなフォントサイズで表示
- 補足情報や注釈に最適
- 複数の要素を横並びで配置可能

**使用場面:**
- タイムスタンプ
- 作成者情報
- 追加の説明文

### 4. Divider ブロック

```json
{
    "type": "divider"
}
```

**特徴:**
- シンプルな水平線
- セクション間の視覚的な区切り
- パラメータ不要で最もシンプルなブロック

### 5. Actions ブロック

```json
{
    "type": "actions",
    "elements": [
        {
            "type": "button",
            "text": {
                "type": "plain_text",
                "text": "Primary ボタン",
                "emoji": true
            },
            "style": "primary",
            "value": "primary_button",
            "url": "https://example.com/primary"
        }
    ]
}
```

**特徴:**
- ボタンやその他のインタラクティブ要素を配置
- 最大5つの要素まで横並び配置可能
- 3つのボタンスタイル：default（境界線のみ）、primary（青色）、danger（赤色）

## 制約の中での効果的なデザインパターン

### 1. 疑似的な枠線の実装

Block Kitには明確な「枠線」や「カード」要素がありませんが、以下のようなテクニックで視覚的なグループ化を実現できます。

```json
{		
	"type": "section",
	"text": {
		"type": "mrkdwn",
		"text": "*2. 擬似的な枠線付きセクション*"
	}
},
{
	"type": "section",
	"text": {
		"type": "mrkdwn",
		"text": "```項目:値```"
	}
},
{
	"type": "section",
	"text": {
		"type": "mrkdwn",
		"text": "```ステータス:完了```"
	}
}
```

**工夫のポイント:**
- Markdownを使用した疑似的な枠線

### 2. 状態表示のパターン

色が変更できない制約の中で、絵文字とテキストを組み合わせて状態を表現。

```json
{
    "type": "section",
    "text": {
        "type": "mrkdwn",
        "text": "• *<https://example.com/task/goals|目標の基本を学ぶ>*\n  :alarm_clock: 2024年9月24日"
    }
},
{
    "type": "section",
    "text": {
        "type": "mrkdwn",
        "text": "• *<https://example.com/task/personal-goals|個人目標設定の方法を学ぶ>*\n  :warning: 2024年9月24日（期限超過）"
    }
}
```

**使用される表現方法:**
- `:alarm_clock:` - 通常の期限
- `:warning:` - 期限超過や注意
- `:white_check_mark:` - 完了
- `:x:` - エラーやキャンセル

### 3. 複数ボタンのレイアウトパターン

Actionsブロックでは、最大5つまでのボタンを配置できます。

```json
{
    "type": "actions",
    "elements": [
        {
            "type": "button",
            "text": {
                "type": "plain_text",
                "text": "確認",
                "emoji": true
            },
            "style": "primary",
            "value": "confirm"
        },
        {
            "type": "button",
            "text": {
                "type": "plain_text",
                "text": "後で",
                "emoji": true
            },
            "value": "later"
        },
        {
            "type": "button",
            "text": {
                "type": "plain_text",
                "text": "キャンセル",
                "emoji": true
            },
            "style": "danger",
            "value": "cancel"
        }
    ]
}
```

**デザインの考慮点:**
- Primary（青色）は最重要アクション用
- Default（境界線のみ）は通常アクション用
- Danger（赤色）は削除や危険なアクション用


## まとめ

Slack Block Kitは確かに「見た目があまり変えられない」という制約がありますが、これらの制約を理解し、適切にブロックを組み合わせることで、美しく機能的なメッセージを作成できます。

重要なのは以下の点です。

1. **制約を受け入れる**: デザインの自由度は制限されているが、その分一貫性と可読性が保たれる
2. **コンテンツ構成に集中**: 色やレイアウトではなく、情報の構造化と優先順位付けに注力
3. **パターンの活用**: 疑似的な枠線や絵文字による状態表現など、制約内でのテクニックを習得
4. **ユーザビリティ優先**: 見た目より機能性とアクセシビリティを重視

Block Kitの詳細なリファレンスは[Slack Block Kit Builder](https://app.slack.com/block-kit-builder)で実際に構築しながら確認できますので、ぜひご活用ください。

この記事で紹介したテクニックのサンプルもぜひご利用ください！

```json
{
	"blocks": [
		{
			"type": "header",
			"text": {
				"type": "plain_text",
				"text": "Slack Block Kitデザインシステム見本",
				"emoji": true
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "こんにちは、鈴木 太郎さん\nこちらはデザインパターンの総合的な見本です。"
			}
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*1. 基本的なセクション*\nこれは基本的なセクションブロックです。 *太字* や _斜体_ 、 ~取り消し線~ 、 `コード` などのMarkdown書式が使えます。"
			}
		},
		{
			"type": "context",
			"elements": [
				{
					"type": "mrkdwn",
					"text": "👆 Contextブロックは小さいテキストで補足情報を表示するのに最適です"
				}
			]
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*2. 擬似的な枠線付きセクション*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "```項目:値```"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "```ステータス:完了```"
			}
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*3. リンク付きのテキスト*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "• *<https://example.com/task/1|リンク付きタスク名>*\n  2024年9月24日"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "• リンクなしタスク名\n  2024年9月25日"
			}
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*4. 画像付きセクション*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "画像を右側に配置できます"
			},
			"accessory": {
				"type": "image",
				"image_url": "https://api.slack.com/img/blocks/bkb_template_images/beagle.png",
				"alt_text": "かわいい犬の画像"
			}
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*5. ボタンとアクション*"
			}
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "Primary ボタン",
						"emoji": true
					},
					"style": "primary",
					"value": "primary_button",
					"url": "https://example.com/primary"
				}
			]
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "Default ボタン（アウトライン）",
						"emoji": true
					},
					"value": "default_button",
					"url": "https://example.com/default"
				}
			]
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "Danger ボタン",
						"emoji": true
					},
					"style": "danger",
					"value": "danger_button",
					"url": "https://example.com/danger"
				}
			]
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*6. 通知カード*"
			}
		},
		{
			"type": "context",
			"elements": [
				{
					"type": "mrkdwn",
					"text": "┌──────────────────────────────────────────┐"
				}
			]
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "　*【オンボーディング】入社後1ヶ月間のTODO*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "　*社員*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "　佐藤 花子、田中 一郎、山田 太郎"
			}
		},
		{
			"type": "context",
			"elements": [
				{
					"type": "mrkdwn",
					"text": "└──────────────────────────────────────────┘"
				}
			]
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "コースを確認",
						"emoji": true
					},
					"style": "primary",
					"value": "check_course",
					"url": "https://example.com/onboarding/course"
				}
			]
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*7. タスクリスト（期限付き）*"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "• *<https://example.com/task/goals|目標の基本を学ぶ>*\n  :alarm_clock: 2024年9月24日"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "• *<https://example.com/task/management|代表的なマネジメントの型を知る>*\n  :alarm_clock: 2024年9月25日"
			}
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "• *<https://example.com/task/personal-goals|個人目標設定の方法を学ぶ>*\n  :warning: 2024年9月24日（期限超過）"
			}
		},
		{
			"type": "divider"
		},
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*8. 複数ボタン配置*"
			}
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "確認",
						"emoji": true
					},
					"style": "primary",
					"value": "confirm"
				},
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "後で",
						"emoji": true
					},
					"value": "later"
				},
				{
					"type": "button",
					"text": {
						"type": "plain_text",
						"text": "キャンセル",
						"emoji": true
					},
					"style": "danger",
					"value": "cancel"
				}
			]
		},
		{
			"type": "divider"
		},
		{
			"type": "context",
			"elements": [
				{
					"type": "mrkdwn",
					"text": "このメッセージはデザインシステムの参考用に自動生成されました"
				}
			]
		}
	]
}
```