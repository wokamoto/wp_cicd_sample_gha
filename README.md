# プロジェクト概要

WordPress Meetup Kobe のデモ向けリポジトリです。`wp-env` を用いてローカルで WordPress 環境を再現し、GitHub Actions を利用してステージング／本番へデプロイできる構成になっています。

## 特徴
- `.wp-env.json` で WordPress 最新コアと PHP 8.2 を指定し、`dest/wp-content` 配下のテーマ・プラグイン・言語ファイルをそのままマッピング
- `dest/` 以下に WordPress 本体一式と日本語翻訳ファイルを同梱し、日本語環境での検証を前提に構築
- GitHub Actions で PHP 構文チェック後、rsync によりステージング・本番環境へ同期可能

## ローカル開発環境

### 前提ツールのインストール
1. `brew install node`
2. `brew install --cask docker`
3. `npm install -g @wordpress/env`

### wp-env の起動
1. `wp-env start`
2. ブラウザで `http://localhost:8888` を開く
   - ユーザー名: `admin`
   - パスワード: `password`

### WP-CLI の利用
- `wp-env run cli wp <command>`

## ディレクトリ構成のポイント
- `dest/` : WordPress コアおよび配布用資産を格納
- `dest/wp-content/themes/twentytwentyfive` : デフォルトテーマ。独自テーマは未同梱
- `dest/wp-content/plugins/` : プラグイン用ディレクトリ（現状サンプル無し）
- `dest/wp-content/languages/` : 日本語翻訳ファイル一式を収録

## GitHub Actions によるデプロイ
- `.github/actions/php-syntax-check/action.yml` : `shivammathur/setup-php@v2` を使い PHP 8.2 で全 PHP ファイルの構文チェックを実行
- `.github/workflows/deploy-staging.yml` : `development` ブランチへの push で PHP 構文チェック後、GitHub Secrets に登録した認証情報を用いてステージングへ `dest/` を同期
- `.github/workflows/deploy-production.yml` : `main` ブランチへの push 時に同様のプロセスで本番環境にデプロイ

デプロイ時は共通して `wp-config.php` や `wp-content/uploads/` を除外し、サーバー側の設定やアップロードファイルを保持する設計です。
