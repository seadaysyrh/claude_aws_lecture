# CloudWatch

## 概要

AWSリソースとアプリケーションの監視・観測サービス。ログの収集・メトリクスの可視化・アラートの通知をまとめて担う。

---

## 主要機能

### CloudWatch Metrics（メトリクス）

AWSサービスのパフォーマンス指標を時系列データで収集。

- EC2のCPU使用率、ネットワーク転送量
- ALBのリクエスト数、レイテンシ
- RDSの接続数、クエリ実行時間
- Lambda の実行回数、エラー数、実行時間

カスタムメトリクスとして自分のアプリの値も送れる。

### CloudWatch Logs（ログ）

アプリや各サービスのログを収集・保存・検索する。

| 概念 | 説明 |
|---|---|
| **ロググループ** | ログの論理的な入れ物（例: `/aws/lambda/my-function`） |
| **ログストリーム** | ロググループ内の実際のログの流れ（インスタンスごと等） |

- **Logs Insights**: SQLライクなクエリでログを検索・分析できる
- 保存期間（保持期間）は1日〜無制限で設定可能

### CloudWatch Alarms（アラーム）

メトリクスが閾値を超えたときにアクションを起こす。

```
CPU使用率 > 80% が5分続いたら
  → SNSで通知メール送信
  → Auto Scalingで台数を増やす
```

| アラーム状態 | 説明 |
|---|---|
| **OK** | 正常範囲内 |
| **ALARM** | 閾値超え |
| **INSUFFICIENT_DATA** | データ不足 |

### CloudWatch Dashboard

メトリクスやアラームをまとめて可視化するダッシュボード。複数サービスの状態を一画面で確認できる。

### CloudWatch Events / EventBridge

AWSサービスのイベントを検知して別サービスをトリガー。

```
例:
EC2インスタンスの起動 → Lambda起動（タグ付け処理）
毎日0時              → Lambda起動（定期バッチ）
CodePipelineの失敗   → SNS通知
```

---

## Container Insights

ECS/EKSのコンテナ単位のメトリクスを収集する機能。

---

## CloudWatch Agent

EC2インスタンスにインストールして、OSレベルのメトリクス（メモリ使用率、ディスク使用量など）やアプリログをCloudWatchに送るエージェント。

> デフォルトではEC2のメモリとディスク使用率はCloudWatchに送られないため、エージェントが必要。

---

## よく使う監視構成

```
EC2 / ECS / Lambda
    ↓ メトリクス・ログ
CloudWatch
    ↓ 閾値超え
CloudWatch Alarm
    ↓
SNS → メール/Slackへ通知
    ↓
Auto Scaling / Lambda（自動対処）
```
