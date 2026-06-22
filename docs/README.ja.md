# due2-cli AIコーディングエージェントプラグイン

[English](../README.md) | [한국어](README.ko.md)

[due2-cli](https://github.com/myiam-io/due2-cli)用AIコーディングエージェントコマンドリファレンス — 期限管理項目（サブスクリプション、保険、税金、ビザなど）をエンドツーエンド暗号化でターミナルから管理します。

## インストール

### Claude Code

```bash
# マーケットプレイスに登録（初回のみ）
claude plugin marketplace add due2-app/due2-cli-plugin

# プラグインをインストール
claude plugin install due2-cli@due2-cli
```

ローカル開発:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
claude --plugin-dir ./due2-cli-plugin
```

### OpenAI Codex CLI

```bash
# マーケットプレイスに登録（初回のみ）
codex plugin marketplace add due2-app/due2-cli-plugin

# プラグインをインストール
codex plugin install due2-cli
```

ローカル開発:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
codex --plugin-dir ./due2-cli-plugin
```

### Google Gemini CLI

```bash
gemini extensions install https://github.com/due2-app/due2-cli-plugin
```

ローカル開発:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
gemini extensions link ./due2-cli-plugin
```

## 前提条件

[due2-cli](https://github.com/myiam-io/due2-cli)をインストール:

```bash
# macOS (Homebrew)
brew tap myiam-io/tap
brew install due2-cli

# Windows (winget)
winget install myiam.Due2.CLI
```

ログインとアンロック:

```bash
due2-cli login
due2-cli unlock
```

## 含まれる内容

| エージェント | ファイル | スコープ |
|---|---|---|
| Claude Code | `skills/due2/SKILL.md` | `/due2`スキル — 期限・サブスクリプション関連の質問で自動トリガー |
| Codex CLI | `skills/due2/SKILL.md` | `@due2`スキル — 期限・サブスクリプション関連の質問で自動トリガー |
| Gemini CLI | `skills/due2/SKILL.md` | extensionスキル — 期限・サブスクリプション関連の質問で自動トリガー |

すべてのファイルにdue2-cliの完全なコマンドリファレンスが含まれています:
- インストールとセットアップ（login, unlock, lock, CI用環境変数）
- CRUD（add, list, show, edit, delete, archive, restore, renew）
- バルク操作（add, edit, delete, archive, restore, share）
- グループと共有（E2EE鍵配布）
- スケジュールパック（アイテムコレクション）
- パブリックスケジュールパックとアイテム（ブラウズ、フォロー、保存、フィードバック、報告、ディスカバー）
- ダッシュボードとアカウント管理（summary, plan, settings, feedback）

## アップデート

### Claude Code

```bash
claude plugin update due2-cli
```

### Codex CLI

```bash
codex plugin upgrade due2-cli
```

### Gemini CLI

```bash
gemini extensions install https://github.com/due2-app/due2-cli-plugin
```

## アンインストール

### Claude Code

```bash
claude plugin uninstall due2-cli
```

### Codex CLI

```bash
codex plugin uninstall due2-cli
```

### Gemini CLI

```bash
gemini extensions uninstall due2-cli
```

## ライセンス

Apache 2.0。詳細は[LICENSE](../LICENSE)をご覧ください。
