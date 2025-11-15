# gh-review

GitHub Actions上でAIレビューを自動化するためのサンプルリポジトリです。Pull Request作成や更新、コメントでの呼び出しに応じて`actions/ai-inference`を利用したレビューを実行します。

## ワークフロー概要

| ファイル | 内容 |
| --- | --- |
| `.github/workflows/pr-ai-review.yml` | PRオープン／更新時にAIレビューを実行し、結果をPRコメントへ投稿します。 |
| `.github/workflows/mention-ai-review.yml` | コメントで`@ai-review`や`/ai`を含む投稿があった場合にオンデマンドでレビューします。 |

それぞれ共通のプロンプトを`.github/ai-prompts/`配下に保持しており、レビュー方針を一元管理できます。

## 使い方

1. GitHub上でPRを作成すると自動的にAIレビューが実行され、結果がPRコメントに表示されます。
2. 追加のレビューを依頼したい場合はPRコメントに`@ai-review`を含めて投稿します。
3. Draft PRや`skip-ai-review`ラベルが付与されたPRではワークフローがスキップされます。

## 注意点

- `actions/ai-inference`で利用するモデルやトークン消費量はGitHub Modelsの設定に依存します。
- Diffが大きい場合は一部が切り捨てられるため、必要に応じてファイル数やトークン制限を調整してください。
