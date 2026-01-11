---
title: Go言語でAsynqにレート制限を実装する
date: 2025-06-03 12:00:00
tags:
  - Go
categories:
  - プログラミング
description: Go言語のタスクキューライブラリAsynqにレート制限を実装する方法を、複数のアプローチとともに詳しく解説します。golang.org/x/time/rateとRedisベースの実装を比較し、実用的なサンプルコードを提供します。

---

タスクキューは多くのWebアプリケーションで必要不可欠な機能ですが、大量のタスクを処理する際にはレート制限が重要になります。特に外部API呼び出しやメール送信などのタスクでは、適切なレート制限なしに処理すると、サービス制限に引っかかったり、相手先のサーバーに負荷をかけてしまう可能性があります。

今回は、Go言語の人気タスクキューライブラリである[Asynq](https://github.com/hibiken/asynq)にレート制限を実装する方法を、複数のアプローチとともに解説します。

## プロジェクト構成

このサンプルプロジェクトは以下の構成になっています。
- [サンプルコード](https://github.com/shoyan/asynq-late-limiter)はGitHubで公開しています。

```
asynq-late-limiter/
├── go.mod
├── tasks/
│   └── tasks.go          # タスクの定義と処理ロジック
├── limiter/
│   ├── limiter.go        # Limiterインターフェース
│   ├── rate_limiter.go   # golang.org/x/time/rateを使った実装
│   ├── redis_rate_limiter.go  # Redisベースの実装（シンプル版）
│   ├── redis_rate_limiterv2.go # Redisベースの実装（改良版）
├── producer/
│   └── main.go           # タスクを生成するプロデューサー
└── worker/
    └── main.go           # タスクを処理するワーカー
```

## 1. インターフェース設計

まず、レート制限の基盤となるインターフェースを定義します。

```go
// limiter/limiter.go
package limiter

import (
    "fmt"
    "time"
)

// レート制限を確認し、エラーを返す
type Limiter interface {
    Check() error
}

// RateLimitErrorは、レート制限が超過されたときに発生するエラー
type RateLimitError struct {
    RetryIn time.Duration
}

func (e *RateLimitError) Error() string {
    return fmt.Sprintf("rate limited (retry in %v)", e.RetryIn)
}
```

このインターフェース設計により、異なるレート制限の実装を簡単に差し替えることができます。

## 2. レート制限の実装方式

### 2.1 golang.org/x/time/rateを使った実装

標準的なトークンバケットアルゴリズムを使った実装です。

```go
// limiter/rate_limiter.go
type RateLimiter struct {
    limiter *rate.Limiter
    limit   int           // 1秒間に処理できるリクエスト数
    burst   int           // 瞬間的に許可される最大リクエスト数
    retryIn time.Duration // レート制限時の再試行待機時間
}

func NewRateLimiter(limit int, burst int, retryIn time.Duration) *RateLimiter {
    return &RateLimiter{
        limiter: rate.NewLimiter(rate.Limit(limit), burst),
        limit:   limit,
        burst:   burst,
        retryIn: retryIn,
    }
}

func (rl *RateLimiter) Check() error {
    if !rl.limiter.Allow() {
        return &RateLimitError{
            RetryIn: rl.retryIn,
        }
    }
    return nil
}
```

**メリット**
- 実装が簡単
- メモリ効率が良い
- 高速

**デメリット**
- 単一プロセスでのみ動作
- 分散環境では使用できない

### 2.2 Redisベースの実装（改良版）

複数のワーカープロセス間でレート制限を共有する場合に適しています。

```go
// limiter/redis_rate_limiterv2.go
type RedisRateLimiterV2 struct {
    client     *redis.Client
    key        string
    limit      int
    windowSize time.Duration
}

func (r *RedisRateLimiterV2) Allow(taskType string) (bool, int64, error) {
    ctx := context.Background()
    key := fmt.Sprintf("rate_limit:%s", taskType)
    now := time.Now().Unix()

    script := `
        local key = KEYS[1]
        local limit = tonumber(ARGV[1])
        local window = tonumber(ARGV[2])
        local now = tonumber(ARGV[3])

        -- 古いエントリを削除
        redis.call("ZREMRANGEBYSCORE", key, "-inf", now - window)

        -- 現在のリクエスト数を取得
        local count = redis.call("ZCOUNT", key, "-inf", "+inf")

        if count >= limit then
            -- 最も古いリクエストのタイムスタンプを取得
            local oldest = redis.call("ZRANGE", key, 0, 0, "WITHSCORES")
            local retryIn = 0.0
            if #oldest > 0 then
                retryIn = math.max(0, oldest[2] + window - now)
            end
            return {0, retryIn}
        end

        -- 新しいリクエストを追加
        redis.call("ZADD", key, now, tostring(now) .. "-" .. redis.call("INCR", "request_counter"))
        redis.call("EXPIRE", key, window)

        return {1, 0}
    `

    result, err := r.client.Eval(ctx, script, []string{key}, r.limit, int(r.windowSize.Seconds()), now).Result()
    // ... エラーハンドリングと結果の解析
}
```

**メリット**
- 分散環境での動作
- 複数のワーカー間でレート制限を共有
- 正確な待機時間の算出

**デメリット**
- Redisへの依存
- わずかなパフォーマンスオーバーヘッド

## 3. Asynqとの統合

### タスクの定義

```go
// tasks/tasks.go
func EmailNotificationTask(ctx context.Context, t *asynq.Task, limit limiter.Limiter) error {
    // レート制限を確認
    if err := limit.Check(); err != nil {
        return err
    }

    // メール送信処理
    var payload map[string]string
    if err := json.Unmarshal(t.Payload(), &payload); err != nil {
        return err
    }
    log.Println("Sending Email to:", payload["email"], "with subject:", payload["subject"])
    return nil
}
```

### ワーカーの設定

```go
// worker/main.go
func main() {
    // 1秒間1リクエストに制限
    emailRateLimiter := limiter.NewRedisRateLimiterV2("email", 1, time.Second)

    srv := asynq.NewServer(
        asynq.RedisClientOpt{Addr: ":6379"},
        asynq.Config{
            Concurrency: 5,
            Queues: map[string]int{
                "default": 7,
                "email":   3,
            },
            // レート制限エラーを失敗としてカウントしない
            IsFailure: func(err error) bool { 
                return !IsRateLimitError(err) 
            },
            // 再試行の間隔を指定
            RetryDelayFunc: retryDelay,
            DelayedTaskCheckInterval: 100 * time.Millisecond,
        },
    )

    mux := asynq.NewServeMux()
    mux.HandleFunc(tasks.TypeEmailTask, func(ctx context.Context, t *asynq.Task) error {
        // TaskにRateLimiterを設定する
        return tasks.EmailNotificationTask(ctx, t, emailRateLimiter)
    })

    if err := srv.Run(mux); err != nil {
        log.Fatalf("サーバーを起動できませんでした: %v", err)
    }
}
```

### エラーハンドリングとリトライ戦略

```go
// レート制限エラーの判定
func IsRateLimitError(err error) bool {
    _, ok := err.(*limiter.RateLimitError)
    return ok
}

// カスタムリトライ遅延
func retryDelay(n int, err error, task *asynq.Task) time.Duration {
    var ratelimitErr *limiter.RateLimitError
    if errors.As(err, &ratelimitErr) {
        return ratelimitErr.RetryIn
    }
    return asynq.DefaultRetryDelayFunc(n, err, task)
}
```

## 4. 実行方法

### 1. 依存関係のインストール

```bash
go mod tidy
```

### 2. Redisの起動

```bash
# Dockerを使用する場合
docker run -d -p 6380:6379 redis:latest

# または直接実行
redis-server --port 6380
```

### 3. ワーカーの起動

```bash
go run worker/main.go
```

### 4. タスクの投入

別のターミナルで以下のコマンドを実行。

```bash
go run producer/main.go
```

## 5. 実装のポイント

### レート制限の適切な設定

- **外部API呼び出し**: APIの制限に合わせて設定（例：100リクエスト/分）
- **メール送信**: メール送信サービスの制限に合わせて設定（例：10通/秒）
- **データベース操作**: データベースの負荷を考慮して設定

### エラーハンドリング

```go
srv := asynq.NewServer(
    redisOpt,
    asynq.Config{
        // レート制限エラーは失敗としてカウントしない
        IsFailure: func(err error) bool { 
            return !IsRateLimitError(err) 
        },
        // レート制限エラーの場合は適切な待機時間でリトライ
        RetryDelayFunc: retryDelay,
    },
)
```

## まとめ

このサンプルコードでは、Asynqにレート制限を実装する複数の方法を紹介しました。どの実装を選ぶかは、システムの要件によって決まります：

- **単一プロセス環境**: `golang.org/x/time/rate`ベースの実装
- **分散環境**: Redisベースの実装

インターフェースベースの設計により、後から実装を切り替えることも容易です。適切なレート制限により、安定したタスク処理システムを構築できます。

## 参考リンク

- [Asynq公式ドキュメント](https://github.com/hibiken/asynq)
- [golang.org/x/time/rate](https://pkg.go.dev/golang.org/x/time/rate)
- [サンプルコード](https://github.com/shoyan/asynq-late-limiter)
