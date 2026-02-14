# Research & Design Decisions

## Summary
- **Feature**: `github-actions-publish`
- **Discovery Scope**: New Feature（新規機能）
- **Key Findings**:
  - GitHub PagesはデフォルトでJekyllを使用し、マークダウンを自動的にHTMLに変換する
  - GitHub Actions公式ワークフロー（`actions/upload-pages-artifact` + `actions/deploy-pages`）を使用することでカスタムビルドパイプラインを構築可能
  - 追加のビルドツールなしで、JekyllのビルトインGitHub Pagesサポートを使用するのが最もシンプル

## Research Log

### GitHub Pagesのデプロイ方法
- **Context**: マークダウンファイルをHTMLに変換してGitHub Pagesにデプロイする方法の調査
- **Sources Consulted**:
  - [GitHub Docs - Configuring a publishing source](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
  - [GitHub Actions deploy-pages](https://github.com/actions/deploy-pages)
  - [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)
- **Findings**:
  - 方法1: Jekyll自動ビルド（GitHub Pages設定でSourceをブランチに設定）
  - 方法2: GitHub Actionsカスタムワークフロー（`actions/upload-pages-artifact` + `actions/deploy-pages`）
  - 方法3: サードパーティアクション（`peaceiris/actions-gh-pages`）
- **Implications**: シンプルなマークダウンサイトにはJekyll自動ビルドが最適。カスタムビルドが必要な場合はGitHub Actionsワークフローを使用

### マークダウン変換メカニズム
- **Context**: src/配下のマークダウンファイルをHTMLに変換する方法
- **Sources Consulted**:
  - [Jekyll Markdown Reference](https://www.markdownguide.org/tools/jekyll/)
  - [GitHub Pages Markdown Reference](https://www.markdownguide.org/tools/github-pages/)
- **Findings**:
  - GitHub PagesはデフォルトでJekyllを使用
  - Jekyllはkramdownをデフォルトのマークダウンプロセッサとして使用
  - GitHub Flavored Markdown（GFM）との互換性あり
- **Implications**: 追加の変換ツール不要。Jekyll設定ファイル（`_config.yml`）でソースディレクトリを指定可能

## Architecture Pattern Evaluation

| Option | Description | Strengths | Risks / Limitations | Notes |
|--------|-------------|-----------|---------------------|-------|
| Jekyll自動ビルド | GitHub Pagesの標準機能を使用 | 設定最小限、メンテナンス不要 | カスタマイズに制限あり | プロジェクト要件に最適 |
| カスタムGitHub Actions | `actions/deploy-pages`を使用 | フル制御、カスタムビルド可能 | ワークフロー設定が必要 | 将来の拡張性あり |
| サードパーティアクション | `peaceiris/actions-gh-pages` | 実績あり、多機能 | 外部依存、メンテナンスリスク | 過剰機能 |

## Design Decisions

### Decision: Jekyll自動ビルドの採用
- **Context**: マークダウンからHTMLへの変換とデプロイ方法の選択
- **Alternatives Considered**:
  1. Jekyll自動ビルド — GitHub Pages標準機能
  2. カスタムGitHub Actions — `actions/upload-pages-artifact` + `actions/deploy-pages`
  3. サードパーティアクション — `peaceiris/actions-gh-pages`
- **Selected Approach**: Jekyll自動ビルドをベースに、GitHub Actionsワークフローでトリガーを明示的に制御
- **Rationale**: シンプルな要件（マークダウン→HTML変換）にはJekyllの自動ビルドが最適。ただし、トリガー条件の明示化のためGitHub Actionsワークフローを使用
- **Trade-offs**:
  - メリット: 最小限の設定、メンテナンス不要、公式サポート
  - デメリット: ビルドプロセスのカスタマイズに制限
- **Follow-up**: src/ディレクトリをJekyllのソースとして設定する`_config.yml`が必要

### Decision: ワークフロートリガーの設計
- **Context**: mainブランチへのプッシュ時のみビルドを実行する要件
- **Alternatives Considered**:
  1. `push`イベント + ブランチフィルター
  2. `push` + `pull_request`イベントの組み合わせ
- **Selected Approach**: `push`イベントにmainブランチフィルターを適用
- **Rationale**: 要件1.1, 1.2, 1.3を満たす最もシンプルな構成
- **Trade-offs**: PRマージはpushイベントとして検出される
- **Follow-up**: なし

## Risks & Mitigations
- **Risk 1**: src/ディレクトリがJekyllのデフォルト構造と異なる — `_config.yml`で`source`ディレクトリを明示的に指定
- **Risk 2**: Jekyllテーマなしでのレイアウト問題 — デフォルトテーマを使用、または最小限のカスタムレイアウトを作成
- **Risk 3**: マークダウンファイルが存在しない場合の挙動 — Jekyllは空のサイトを正常に生成する（要件2.4を満たす）

## References
- [GitHub Docs - Configuring a publishing source](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site) — GitHub Pagesのソース設定方法
- [GitHub Docs - Using custom workflows](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages) — カスタムワークフローの使用方法
- [actions/deploy-pages](https://github.com/actions/deploy-pages) — 公式デプロイアクション
- [Jekyll Markdown Reference](https://www.markdownguide.org/tools/jekyll/) — Jekyllのマークダウン処理について
