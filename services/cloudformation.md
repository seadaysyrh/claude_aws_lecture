# CloudFormation

## 概要

インフラをコードで定義・管理するサービス（IaC: Infrastructure as Code）。YAMLまたはJSONでAWSリソースを記述すると、CloudFormationがその通りにリソースを自動作成・更新・削除してくれる。

---

## なぜIaCが必要か

| 手動管理 | IaC（CloudFormation） |
|---|---|
| コンソールで手動操作 | コードで定義 |
| 作業手順が属人化 | 手順がコードとして残る |
| 同じ環境を再現しにくい | `git clone` → デプロイで完全再現 |
| 差分管理できない | Gitで変更履歴を管理 |

---

## 基本的な構成（テンプレート）

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: サンプルテンプレート

Parameters:         # 外から渡す変数
  EnvironmentName:
    Type: String
    Default: dev

Resources:          # 作成するリソースを定義（必須）
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "${EnvironmentName}-my-bucket"

  MyEC2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId: ami-xxxxxxxx

Outputs:            # 作成したリソースの情報を出力
  BucketName:
    Value: !Ref MyBucket
```

---

## スタック

テンプレートから作成されるリソース群の単位。

```
テンプレート（.yaml）→ デプロイ → スタック（実際のAWSリソース）
```

- スタックを削除するとリソースも一括削除
- スタックの更新は差分のみ適用（変更セットで事前確認も可能）

---

## 主要な組み込み関数

| 関数 | 説明 |
|---|---|
| `!Ref` | リソース名やパラメータの値を参照 |
| `!GetAtt` | リソースの属性値を取得（例: ALBのDNS名） |
| `!Sub` | 文字列に変数を埋め込む |
| `!Join` | 文字列を結合 |
| `!If` | 条件分岐 |

---

## チェンジセット（変更セット）

スタック更新前に「何が変わるか」をプレビューする機能。破壊的な変更（リソースの置換・削除）を事前に確認できる。

---

## CloudFormation vs Terraform

| | CloudFormation | Terraform |
|---|---|---|
| 提供元 | AWS公式 | HashiCorp（OSS） |
| 対応クラウド | AWSのみ | マルチクラウド |
| 言語 | YAML/JSON | HCL（独自言語） |
| 状態管理 | AWSが管理 | tfstateファイル |
| 採用理由 | AWSに特化でいい | マルチクラウドや使い慣れている |

---

## CDK (Cloud Development Kit)

CloudFormationをプログラミング言語（TypeScript/Python/Java等）で書けるツール。コードからCloudFormationテンプレートを生成する。

```typescript
// TypeScriptでS3バケットを定義する例
const bucket = new s3.Bucket(this, 'MyBucket', {
  versioned: true,
  encryption: s3.BucketEncryption.S3_MANAGED,
});
```

CloudFormationの生テンプレートよりコードとして書きやすい。

---

## よく使うユースケース

- 環境（dev/stg/prod）の一括構築
- DR（災害対策）用の別リージョン環境の複製
- CI/CDパイプラインからの自動インフラデプロイ
