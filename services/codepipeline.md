# CodePipeline / CodeBuild / CodeDeploy

## 概要

AWSのCI/CDサービス群（Developer Toolsとも呼ばれる）。コードのコミットからテスト・ビルド・デプロイまでを自動化する。

---

## サービスの役割

```
コードをpush
    ↓
[CodePipeline] ← パイプライン全体を管理・オーケストレーション
    ├─ ソース取得（GitHub/CodeCommit/S3）
    ├─ [CodeBuild] ← ビルド・テスト実行
    └─ [CodeDeploy] ← デプロイ実行
```

---

## CodePipeline

パイプライン全体を定義・管理するオーケストレーターサービス。

### ステージ構成例

```
[Source]  → GitHubのmainブランチへのpushをトリガー
[Build]   → CodeBuildでテスト＆Dockerイメージビルド
[Deploy]  → CodeDeployでEC2/ECSにデプロイ
```

- 各ステージの承認ステップ（手動承認）も挟める
- ステージが失敗したら後続は実行されない

---

## CodeBuild

クラウド上のビルドサーバー（マネージドCI環境）。サーバーを管理する必要がない。

### 設定ファイル (buildspec.yml)

```yaml
version: 0.2
phases:
  install:
    runtime-versions:
      nodejs: 18
  pre_build:
    commands:
      - npm install
      - npm test
  build:
    commands:
      - npm run build
      - docker build -t my-app .
      - docker push $ECR_URI/my-app:latest
artifacts:
  files:
    - '**/*'
```

---

## CodeDeploy

EC2・Lambda・ECSへのデプロイを自動化するサービス。

### デプロイ戦略

| 戦略 | 説明 | ダウンタイム |
|---|---|---|
| **In-Place** | 既存インスタンスに上書きデプロイ | 少しあり |
| **Blue/Green** | 新環境を別に作って一気に切り替え | なし |
| **カナリア** | 少量のトラフィックから段階的に切り替え | なし |
| **Linear** | 一定割合ずつ段階的に切り替え | なし |

ECSデプロイでのBlue/Greenが特によく使われる。

---

## CodeCommit

AWSのGitリポジトリサービス。GitHubの代替。ただし2024年に新規ユーザーへの提供が停止されたため、GitHubやGitLabを使う方が一般的。

---

## GitHub Actionsとの比較

| | AWS CodePipeline/CodeBuild | GitHub Actions |
|---|---|---|
| 管理場所 | AWS内で完結 | GitHubと統合 |
| AWS連携 | ネイティブに連携しやすい | OIDC設定が必要だが可能 |
| コスト | ビルド時間課金 | 無料枠あり |
| 採用理由 | AWSに閉じた環境を好む場合 | GitHubを既に使っている場合 |

最近はGitHub Actionsを使ってAWSにデプロイするケースも多い。

---

## よく使う構成

```
GitHub main pushをトリガー
    ↓ CodePipeline
    ↓ CodeBuild → テスト → ECRにDockerイメージpush
    ↓ CodeDeploy → ECSをBlue/Greenデプロイ
    ↓ 完了通知 → SNS → Slack
```
