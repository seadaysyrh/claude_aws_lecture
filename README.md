# AWS (Amazon Web Services) ざっくり概要

> 作成日: 2026-04-07

---

## AWSとは

Amazonが提供するクラウドコンピューティングサービス群。2006年開始、世界シェアNo.1（約32%）。

サーバーやネットワーク機器を自前で用意せず、必要なときに必要な分だけ使える「従量課金」が基本。

---

## サービス一覧

### コンピューティング

| サービス | 用途 |
|---|---|
| **[EC2](services/ec2.md)** | 仮想サーバー。好きなOSを選んで使う |
| **[Lambda](services/lambda.md)** | サーバーレス関数。コードだけ書けばいい、サーバー管理不要 |
| **[ECS/EKS](services/ecs_eks.md)** | Dockerコンテナの実行基盤（EKSはKubernetes） |

### ストレージ

| サービス | 用途 |
|---|---|
| **[S3](services/s3.md)** | オブジェクトストレージ。ファイル置き場、静的Webホスティングにも使える |
| **[EBS](services/ebs_efs.md)** | EC2にアタッチするブロックストレージ（HDDみたいなもの） |
| **[EFS](services/ebs_efs.md)** | 複数EC2で共有できるNFSストレージ |

### データベース

| サービス | 用途 |
|---|---|
| **[RDS](services/rds.md)** | マネージドRDB（MySQL/PostgreSQL/Auroraなど） |
| **[DynamoDB](services/dynamodb.md)** | NoSQL。スケールしやすい、サーバーレス |
| **[ElastiCache](services/elasticache.md)** | Redis/Memcachedのマネージドキャッシュ |

### ネットワーク

| サービス | 用途 |
|---|---|
| **[VPC](services/vpc.md)** | 仮想プライベートネットワーク。AWSリソースを隔離する |
| **[Route 53](services/route53.md)** | DNSサービス |
| **[CloudFront](services/cloudfront.md)** | CDN。エッジから高速配信 |
| **[ELB](services/elb.md)** | ロードバランサー（ALB/NLBなど） |

### 認証・セキュリティ

| サービス | 用途 |
|---|---|
| **[IAM](services/iam.md)** | ユーザー・ロール・権限管理。AWSの要 |
| **[Cognito](services/cognito.md)** | アプリのユーザー認証基盤 |
| **[WAF](services/waf_kms.md)** | Webアプリファイアウォール |
| **[KMS](services/waf_kms.md)** | 暗号鍵の管理 |

### 開発・運用

| サービス | 用途 |
|---|---|
| **[CloudWatch](services/cloudwatch.md)** | ログ・メトリクス・アラート監視 |
| **[CloudFormation](services/cloudformation.md)** | インフラをコードで管理（IaC） |
| **[CodePipeline/CodeBuild](services/codepipeline.md)** | CI/CD パイプライン |
| **[SQS/SNS](services/sqs_sns.md)** | メッセージキュー/通知サービス |

---

## 重要な概念

### リージョンとAZ

- **リージョン**: 地理的な場所（例: ap-northeast-1 = 東京）
- **AZ (Availability Zone)**: リージョン内の独立したデータセンター群
  - 複数AZに分散させると障害に強くなる（Multi-AZ構成）

### 課金モデル

| 種別 | 内容 |
|---|---|
| **オンデマンド** | 時間単位で利用。最も柔軟 |
| **リザーブドインスタンス** | 1〜3年契約で最大72%割引 |
| **スポットインスタンス** | 余剰キャパシティを安く使う（最大90%引き、突然終了の可能性あり） |

### 責任共有モデル

```
AWS側の責任:
  物理インフラ、ハードウェア、ネットワーク、ホストOS

ユーザー側の責任:
  ゲストOS、ミドルウェア、アプリ、データ、IAM設定
```

---

## よくある構成パターン

### 一般的なWebアプリ構成

```
[Internet]
    ↓
[CloudFront]     ← CDN/キャッシュ
    ↓
[ALB]            ← ロードバランサー
    ↓
[EC2 / ECS]      ← アプリサーバー（複数AZ）
    ↓
[RDS Aurora]     ← DB（Multi-AZ）
```

### シンプルなサーバーレス構成

```
[API Gateway] → [Lambda] → [DynamoDB]
                               ↕
                              [S3]
```

---

## 学習ロードマップ

1. **IAM** → 権限・セキュリティの考え方（最初に絶対やる）
2. **VPC** → ネットワーク構成の基礎
3. **EC2 + S3** → 最もよく使う基本サービス
4. **RDS + ELB** → 実用的なWeb構成
5. **Lambda + API Gateway** → サーバーレス
6. **CloudWatch** → 監視・運用

### 資格

- **AWS SAA（Solutions Architect Associate）** が登竜門
- CLF（Cloud Practitioner）→ SAA の順で取るのが一般的

---

## よく出るキーワードまとめ

| 用語 | 意味 |
|---|---|
| ARN | AWSリソースの一意な識別子 |
| AMI | EC2のOSイメージ（テンプレート） |
| Security Group | EC2などのファイアウォール（ステートフル） |
| NACL | VPCサブネットレベルのファイアウォール（ステートレス） |
| NAT Gateway | プライベートサブネットからのインターネット接続 |
| IGW (Internet Gateway) | VPCをインターネットに接続する出口 |
| Elastic IP | 固定のパブリックIPアドレス |
| Auto Scaling | 負荷に応じてEC2台数を自動増減 |
