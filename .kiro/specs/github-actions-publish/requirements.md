# Requirements Document

## Introduction
このドキュメントは、SF6のメモから静的ページを生成し、オンラインに公開するための要件を定義する。
マークダウン形式で書かれたメモをGitHubにプッシュすると、Github Actionsで自動的にHTMLページが生成され、Github Pages上で閲覧可能になる仕組みを構築する。

## Requirements

### Requirement 1: ワークフロートリガー設定
**Objective:** 開発者として、mainブランチへのプッシュ時に自動でビルドが実行されることで、手動でのデプロイ作業を省略したい

#### Acceptance Criteria
1. When mainブランチへのプッシュが発生した時, the GitHub Actions Workflow shall ビルドジョブを自動的に開始する
2. When プルリクエストがmainブランチにマージされた時, the GitHub Actions Workflow shall ビルドジョブを自動的に開始する
3. The GitHub Actions Workflow shall mainブランチ以外のプッシュではビルドを実行しない

### Requirement 2: 静的サイト生成
**Objective:** 開発者として、src/配下のマークダウンファイルがHTMLに変換されることで、ブラウザで閲覧可能な形式になってほしい

#### Acceptance Criteria
1. When ビルドジョブが開始された時, the GitHub Actions Workflow shall src/配下のすべてのマークダウンファイルを検出する
2. When マークダウンファイルが検出された時, the GitHub Actions Workflow shall 各ファイルをHTMLに変換する
3. The GitHub Actions Workflow shall 変換されたHTMLファイルをデプロイ用のディレクトリに出力する
4. If マークダウンファイルが存在しない場合, then the GitHub Actions Workflow shall エラーを発生させずに空のサイトを生成する

### Requirement 3: GitHub Pagesへのデプロイ
**Objective:** 閲覧者として、オンラインでいつでもメモを閲覧できるようにしたい

#### Acceptance Criteria
1. When ビルドが成功した時, the GitHub Actions Workflow shall 生成されたファイルをGitHub Pagesにデプロイする
2. The GitHub Actions Workflow shall GitHub Pagesの公開URLでサイトにアクセス可能にする
3. If デプロイが失敗した場合, then the GitHub Actions Workflow shall エラーメッセージをログに出力する
