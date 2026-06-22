# due2-cli Plugin for Claude Code

Claude Code plugin for [due2-cli](https://github.com/myiam-io/due2-cli) — manage deadline items (subscriptions, insurance, taxes, visas, etc.) from the terminal with end-to-end encryption.

## Install

### Option 1: Add marketplace + install

```bash
# Add this repo as a marketplace (one-time)
claude plugin marketplace add myiam-io/due2-cli-plugin

# Install the plugin
claude plugin install due2-cli@due2-cli
```

### Option 2: Local (for development)

```bash
git clone https://github.com/myiam-io/due2-cli-plugin.git
claude --plugin-dir ./due2-cli-plugin
```

## Prerequisites

Install [due2-cli](https://github.com/myiam-io/due2-cli) first:

```bash
brew tap myiam-io/tap
brew install due2-cli
```

Then login and unlock:

```bash
due2-cli login
due2-cli unlock
```

## What's included

- **`/due2` skill** — Full command reference for due2-cli covering:
  - Core CRUD (add, list, show, edit, delete, archive, restore, renew)
  - Bulk operations (add, edit, delete, archive, restore, share)
  - Groups & sharing with E2EE key distribution
  - Packs (named item collections)
  - Public packs & items (browse, follow, save, feedback, report, discover)
  - Dashboard & account management (summary, plan, settings, feedback)

## Update

```bash
claude plugin update due2-cli
```

## Uninstall

```bash
claude plugin uninstall due2-cli
```

## License

Apache 2.0. See [LICENSE](LICENSE) for details.
