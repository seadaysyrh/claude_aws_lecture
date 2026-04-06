# AWS (Amazon Web Services) ざっくり概要

> 作成日: 2026-04-07

---

## AWSとは

Amazonが提供するクラウドコンピューティングサービス群。2006年開始、世界シェアNo.1（約32%）。

サーバーやネットワーク機器を自前で用意せず、必要なときに必要な分だけ使える「従量課金」が基本。

---

## サービス一覧

### コンピューティング

| サービス | 用途 | 詳細 |
|---|---|---|
| **EC2** | 仮想サーバー。好きなOSを選んで使う | [→ EC2](services/ec2.md) |
| **Lambda** | サーバーレス関数。コードだけ書けばいい、サーバー管理不要 | [→ Lambda](services/lambda.md) |
| **ECS/EKS** | Dockerコンテナの実行基盤（EKSはKubernetes） | [→ ECS/EKS](services/ecs_eks.md) |

### ストレージ

| サービス | 用途 | 詳細 |
|---|---|---|
| **S3** | オブジェクトストレージ。ファイル置き場、静的Webホスティングにも使える | [→ S3](services/s3.md) |
| **EBS** | EC2にアタッチするブロックストレージ（HDDみたいなもの） | [→ EBS/EFS](services/ebs_efs.md) |
| **EFS** | 複数EC2で共有できるNFSストレージ | [→ EBS/EFS](services/ebs_efs.md) |

### データベース

| サービス | 用途 | 詳細 |
|---|---|---|
| **RDS** | マネージドRDB（MySQL/PostgreSQL/Auroraなど） | [→ RDS](services/rds.md) |
| **DynamoDB** | NoSQL。スケールしやすい、サーバーレス | [→ DynamoDB](services/dynamodb.md) |
| **ElastiCache** | Redis/Memcachedのマネージドキャッシュ | [→ ElastiCache](services/elasticache.md) |

### ネットワーク

| サービス | 用途 | 詳細 |
|---|---|---|
| **VPC** | 仮想プライベートネットワーク。AWSリソースを隔離する | [→ VPC](services/vpc.md) |
| **Route 53** | DNSサービス | [→ Route 53](services/route53.md) |
| **CloudFront** | CDN。エッジから高速配信 | [→ CloudFront](services/cloudfront.md) |
| **ELB** | ロードバランサー（ALB/NLBなど） | [→ ELB](services/elb.md) |

### 認証・セキュリティ

| サービス | 用途 | 詳細 |
|---|---|---|
| **IAM** | ユーザー・ロール・権限管理。AWSの要 | [→ IAM](services/iam.md) |
| **Cognito** | アプリのユーザー認証基盤 | [→ Cognito](services/cognito.md) |
| **WAF** | Webアプリファイアウォール | [→ WAF/KMS](services/waf_kms.md) |
| **KMS** | 暗号鍵の管理 | [→ WAF/KMS](services/waf_kms.md) |

### 開発・運用

| サービス | 用途 | 詳細 |
|---|---|---|
| **CloudWatch** | ログ・メトリクス・アラート監視 | [→ CloudWatch](services/cloudwatch.md) |
| **CloudFormation** | インフラをコードで管理（IaC） | [→ CloudFormation](services/cloudformation.md) |
| **CodePipeline/CodeBuild** | CI/CD パイプライン | [→ CodePipeline](services/codepipeline.md) |
| **SQS/SNS** | メッセージキュー/通知サービス | [→ SQS/SNS](services/sqs_sns.md) |

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
