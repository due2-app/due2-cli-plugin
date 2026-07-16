# due2-cli Plugin for AI Coding Agents

[한국어](docs/README.ko.md) | [日本語](docs/README.ja.md)

[due2-cli](https://github.com/myiam-io/due2-cli) command reference for AI coding agents — manage deadline items (subscriptions, insurance, taxes, visas, etc.) from the terminal with end-to-end encryption.

## Install

### Claude Code

```bash
# Add this repo as a marketplace (one-time)
claude plugin marketplace add due2-app/due2-cli-plugin

# Install the plugin
claude plugin install due2@due2
```

Local development:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
claude --plugin-dir ./due2-cli-plugin
```

### OpenAI Codex CLI

```bash
# Add this repo as a marketplace (one-time)
codex plugin marketplace add due2-app/due2-cli-plugin

# Install the plugin
codex plugin install due2
```

Local development:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
codex --plugin-dir ./due2-cli-plugin
```

### Google Gemini CLI

```bash
gemini extensions install https://github.com/due2-app/due2-cli-plugin
```

Local development:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
gemini extensions link ./due2-cli-plugin
```

## Prerequisites

Install [due2-cli](https://github.com/myiam-io/due2-cli):

```bash
# macOS (Homebrew)
brew tap myiam-io/tap
brew install due2-cli

# Windows (winget)
winget install myiam.Due2.CLI
```

Then login and unlock:

```bash
due2-cli login
due2-cli unlock
```

## What's included

| Agent | File | Scope |
|---|---|---|
| Claude Code | `skills/due2/SKILL.md` | `/due2` skill — auto-triggers on deadline/subscription queries |
| Codex CLI | `skills/due2/SKILL.md` | `@due2` skill — auto-triggers on deadline/subscription queries |
| Gemini CLI | `skills/due2/SKILL.md` | Extension skill — auto-triggers on deadline/subscription queries |

All files cover the full due2-cli command reference:
- Install & setup (login, unlock, lock, env vars for CI)
- Core CRUD (add, list, show, edit, delete, archive, restore, renew)
- Bulk operations (add, edit, delete, archive, restore, share)
- Groups & sharing with E2EE key distribution
- Schedule packs (named item collections)
- Public schedule packs & items (browse, follow, save, feedback, report, discover)
- Dashboard & account management (summary, plan, settings, feedback)

## Update

### Claude Code

```bash
claude plugin update due2
```

### Codex CLI

```bash
codex plugin upgrade due2
```

### Gemini CLI

```bash
gemini extensions install https://github.com/due2-app/due2-cli-plugin
```

## Uninstall

### Claude Code

```bash
claude plugin uninstall due2
```

### Codex CLI

```bash
codex plugin uninstall due2
```

### Gemini CLI

```bash
gemini extensions uninstall due2
```

## License

Apache 2.0. See [LICENSE](LICENSE) for details.
