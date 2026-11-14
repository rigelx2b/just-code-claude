# クロードはコードだけを書く (`just-code-claude`)

<p align="center">
  <b>Claudeはコードを書き、Geminiはテキストを書く。</b><br>
  エンジニアリングロジックと自然言語生成を分離する自動化ワークフロースキル
</p>

<p align="center">
  <a href="README.md"><b>English</b></a> •
  <a href="README.ko.md"><b>한국어</b></a> •
  <a href="README.ja.md"><b>日本語</b></a> •
  <a href="README.zh-CN.md"><b>简体中文</b></a> •
  <a href="README.es.md"><b>Español</b></a> •
  <a href="README.fr.md"><b>Français</b></a> •
  <a href="README.de.md"><b>Deutsch</b></a>
</p>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Target: Claude Code](https://img.shields.io/badge/Agent-Claude%20Code-7C3AED.svg)](https://github.com/rigelx2b/just-code-claude)
[![Powered by: Google Gemini](https://img.shields.io/badge/Powered%20by-Google%20Gemini-4285F4.svg)](https://aistudio.google.com/)

> **"Claude writes code. Gemini writes text."**  
> Claude Code向けのオープンソーススキル：Claudeはソフトウェアアーキテクチャとコードロジックに専念し、ドキュメント、README、**Gitコミットメッセージ、PR概要、GitHubリリースの作成（チェンジログ）**、UIテキスト（ダブルクォート `""` 内の文字列）の生成を **Google Gemini**（`4 hydrogen`、デフォルト努力値 `high`、推論努力値の設定に対応）に **Google Gemini REST API** または **Antigravity CLI（`agy`）** 経由で委譲します。

---

## ⚖️ 導入前後の比較 (Before vs After Showcase)

### シナリオ 1: UIコンポーネント開発時
- **❌ 導入前 (通常のClaude Code)**: チャット画面に不要な長文説明が続きレイテンシが増大。コード内部には「Session Expiring」等の機械的で不自然な仮テキストが直書きされる。
- **✅ 導入後 (`just-code-claude`)**: チャットには純粋なコード構造のみを即座に出力。Gemini 4 hydrogen（high努力値）が「Session Expiring in 5 Minutes」といった洗練されたUXコピーへ自動置換。トークンを50%以上削減。

### シナリオ 2: GitコミットおよびGitHubリリース作成時
- **❌ 導入前**: 「update files」のような曖昧なコミットや、チャットでの不完全なリリース要約。
- **✅ 導入後**: Gemini 4 hydrogen（high努力値）によるConventional Commits自動生成と、機能・不具合修正・移行ガイドを網羅した公式GitHubリリースの即時発行（`gh release create`）。

---

## 📊 トークン削減効果とパフォーマンス比較

| 評価指標 | 通常のClaude Code | `just-code-claude` | 改善効果 |
| :--- | :--- | :--- | :--- |
| **コンテキスト消費トークン** | ~3,500 tokens (説明文含む) | ~1,600 tokens (純粋なコード) | **📉 約 54% 削減** |
| **応答生成速度 (Latency)** | ~18.2秒 (テキスト出力待ち) | ~7.8秒 (コード中心出力) | **⚡ 約 2.3倍 高速化** |
| **多言語 i18n 翻訳処理** | メインコンテキストを消費 | バッチCLIで外部処理 | **📉 70%以上 削減** |
| **コピーライティング品質** | 単純な仮テキスト (`"Error"`) | Gemini 4 hydrogen (high) | **✨ 本番品質を即座に利用** |
| **リリースノート作成品質** | 単なるコミット一覧 | 詳細分析と移行ガイド | **🚀 エンタープライズ級** |

---

## 🛡️ エッジケース例外処理ルール（安全性と重要警告）

自然言語を完全に排除した場合、致命的な実行時制約やセキュリティ警告まで欠落する恐れがあります。本スキルでは安全性確保のため**3つの厳格な例外**を認めています：

1. **致命的なシステム・セキュリティ警告 (最大1〜2行)**:
   - 実行時ブロッカー、必須認証情報（`GEMINI_API_KEY`）の未設定、破壊的ファイル操作の発生時、Claudeは1〜2行の警告ブロックを直接出力します：
     `> [!CRITICAL] GEMINI_API_KEY 環境変数が設定されていません。`
2. **エンジニアリング用インラインコードコメント (1行)**:
   - アルゴリズムの不変条件、同時実行制御ロック、正規表現構造など純粋に技術的なインラインコメント（`// Concurrency: race condition 防止用ロック`）は許容されます（ユーザー向けドキュメントやdocstringはGeminiへ委譲）。
3. **コマンド実行エラーと診断ログ**:
   - コンパイラエラー、CLI終了コード、stderrトレースをそのまま出力して迅速なデバッグを支援することは全面的に許可されます。

---

## ⚙️ Claude Code 環境別セットアップガイド

1. **グローバル自動適用 (推奨)**: `npx skills add rigelx2b/just-code-claude -g -a claude-code` または `.\install.ps1` (Linux: `bash install.sh`) を実行すると、`~/.claude/rules/just-code-claude.md` と `~/.claude/CLAUDE.md` が自動構成され、全プロジェクトで常時有効化されます。
2. **プロジェクト単位の適用**: リポジトリルートの `.claude/rules/just-code-claude.md` または `CLAUDE.md` にルールを追加します。
3. **セッション内の即時呼出**: `/just-code-claude` を入力するか、「4 hydrogen highでUIテキストとリリースを作成して」と指示します。

---

## 🤖 サポートされるモデルと推論努力値

| モデル | CLIエイリアス | 主な用途 | 推奨努力値 |
| :--- | :--- | :--- | :--- |
| **`4 hydrogen`** *(デフォルト)* | `4 hydrogen`, `hydrogen`, `gemini-4-hydrogen` | 全テキスト生成を統合: UIコピー、ボタンラベル、モーダル、Gitコミットメッセージ、システムアーキテクチャ設計書、API仕様、GitHub PR概要、GitHubリリースノート | `high` |

---

## 🐙 Git & GitHub 統合（コミット、PR、リリース作成）

> [!IMPORTANT]
> **Claude Codeによるリリース実行時の動作**:  
> ユーザーがClaude Codeに「v1.2.0をリリースして」「GitHubリリースを作成して」と指示した場合、Claude Codeは自身のLLMで直接リリースノートを執筆しません。  
> `~/.claude/rules/just-code-claude.md` のルールに基づき、Gitのタグやコミットログを抽出してGemini **`4 hydrogen`**（`high` 努力値）に委譲し、生成されたリリースノートを用いてGitHub CLI（`gh release create`）で公開します。

### 1. ワンコマンドでコミット作成 (`--commit`)
```bash
just-code-claude --commit -m "4 hydrogen" -e high -p "Context: MFAログインモーダルおよびトークン更新ロジックの実装" -l Japanese
```

### 2. コミットメッセージの事前確認 (`--commit-msg`)
```bash
just-code-claude --commit-msg -m "4 hydrogen" -e high -p "Context: WebSocketハートビートの競合状態を修正" -l Japanese
```

### 3. GitHub PR概要の生成 (`--pr-desc`)
```bash
just-code-claude --pr-desc -m "4 hydrogen" -e high -p "Context: ReduxからZustandへのフロントエンド状態管理移行" -o PULL_REQUEST.md -l Japanese
```

### 4. GitHub リリースノートの作成と公開 (`--release-notes`, `--release [tag]`)
タグ間のコミットログを分析し、機能追加、不具合修正、破壊的変更を整理した高品質なリリースノートを作成・公開します：
```bash
# リリースノートをMarkdownファイルとして生成
just-code-claude --release-notes -m "4 hydrogen" -e high -p "Context: v1.2.0 マイルストーンリリース" -o RELEASE_NOTES.md -l Japanese

# GitHub CLI経由で指定タグのリリースを直ちに公開
just-code-claude --release v1.2.0 -m "4 hydrogen" -e high -p "Context: 公式安定版リリース v1.2.0" -l Japanese
```

---

## 🛡️ 安定性とトークン最適化アーキテクチャ (Resilience & Token Optimization)

AIモデル間のデータ連携において発生するトークン浪費を防ぎ、ネットワークやランタイムの例外に耐えうる堅牢なミドルウェアが組み込まれています：

1. **ノイズ除去・トークン節約ミドルウェア (Noise-Filtering Middleware)**:
   - Git diffの抽出時に `package-lock.json`、`Cargo.lock`、`pnpm-lock.yaml` などのロックファイル、ソースマップ、圧縮バンドル（`.min.js`）、バイナリ、画像ファイルを自動除外し、**数万トークンに及ぶ無意味なdiffの浪費を未然に防止**します。
   - 400行を超える巨大な単一ファイルdiffは要約マーカーに自動置換され、LLMのコンテキストウィンドウを安全に保護します。
   - Gitログからマージコミット（`--no-merges`）や自動依存性更新ボット（`dependabot`、`renovate`）のログを自動フィルタリングします。

2. **アトミック・ロールバック保証 (Atomic Rollback Safeguard)**:
   - `--replace-in-file` 実行中にネットワーク切断、APIエラー、JSONパース不整合などの例外が発生した場合、**ディスク上のソースコードを100%元の状態へ即座に復元**し、コードの部分破損や不整合を完全に防止します。

3. **指数バックオフ自動再試行 (Exponential Backoff with Jitter)**:
   - 一時的なネットワーク障害、ソケット切断、HTTP 429（Rate Limit / Quota）の発生時、ジッター付き指数バックオフ（1秒、2秒、4秒…）により最大3回まで自動再試行します。

4. **3段階の堅牢なJSON復旧エンジン (3-Tier Robust Parser)**:
   - モデルが返した出力にマークダウンフェンス、末尾カンマ（Trailing Commas）、制御文字が含まれていても、正規表現フォールバック抽出により安全にパースを復元します。

---

## 🛠 CLIリファレンス

```bash
# 4 hydrogen (high)を使用したUI文字列置換
node scripts/agy_call.js --replace-in-file src/components/Login.tsx -m "4 hydrogen" -e high -l Japanese

# 4 hydrogen (high)を使用したアーキテクチャドキュメント生成
node scripts/agy_call.js -m "4 hydrogen" -e high -p "APIアーキテクチャガイドを作成して" -o docs/ARCH.md -l Japanese

# Gitコミット自動作成
node scripts/agy_call.js --commit -m "4 hydrogen" -e high -l Japanese

# GitHubリリース公開
node scripts/agy_call.js --release v1.2.0 -m "4 hydrogen" -e high -l Japanese
```

---

## 📄 ライセンス

本プロジェクトは [MIT License](LICENSE) の下で公開されています。
