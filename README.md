# gh-review

GitHub Actions上でAIレビューを自動化するためのサンプルリポジトリです。Pull Request作成や更新、コメントでの呼び出しに応じて`actions/ai-inference`を利用したレビューを実行します。

## ワークフロー概要

| ファイル | 内容 |
| --- | --- |
| `.github/workflows/pr-ai-review.yml` | PRオープン／更新時にAIレビューを実行し、差分ごとのチャンクをAIに渡してインラインコメントを投稿します。 |
| `.github/workflows/mention-ai-review.yml` | コメントで`@ai-review`や`/ai`を含む投稿があった場合にオンデマンドでレビューします。 |

それぞれ共通のプロンプトを`.github/prompts/`配下に保持しており、レビュー方針を一元管理できます。

## 使い方

1. GitHub上でPRを作成すると自動的にAIレビューが実行され、問題箇所があれば対象行にインラインコメントが付きます。
2. 追加のレビューを依頼したい場合はPRコメントに`@ai-review`を含めて投稿します。
3. Draft PRや`skip-ai-review`ラベルが付与されたPRではワークフローがスキップされます。

## 注意点

- `actions/ai-inference`で利用するモデルやトークン消費量はGitHub Modelsの設定に依存します。
- Diffが大きい場合は一部が切り捨てられるため、必要に応じてファイル数やトークン制限を調整してください。

## AIレビューの動き

1. `pr-ai-review`ワークフローがPRの差分をファイル・ハンク単位にチャンク化し、各チャンクをBase64でエンコードしてマトリクスとして展開します。
2. 各チャンクについて`actions/ai-inference`が`.github/prompts/system.md`と`.github/prompts/review.prompt.yml`をもとにレビューを実行し、指摘をJSON配列で返します。
3. AIの応答は`actions/github-script`でパースされ、`path`と`line`を指定したGitHubレビューコメントとして投稿されます。
