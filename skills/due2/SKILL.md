---
name: due2
description: Manage deadline items (subscriptions, insurance, taxes, visas, etc.) via due2-cli. Use when the user asks to add, list, edit, delete, renew, archive, share, or bulk-manage deadlines, subscriptions, or reminders.
---

# Due2 CLI

Manage deadline items (subscriptions, insurance, taxes, visas, etc.) from the terminal with end-to-end encryption.

## Install

```bash
# macOS (Homebrew)
brew tap myiam-io/tap
brew install due2-cli

# Windows (winget)
winget install myiam.Due2.CLI

# Due2 desktop app (macOS cask)
brew install --cask due2
```

## Setup (required before any data command)

```bash
# 1. Login + Unlock in one step (recommended)
due2-cli login --unlock          # login → opens browser for PIN/password → unlock
due2-cli login --unlock --ttl 120  # custom TTL

# Or separately:
due2-cli login                   # login only
due2-cli unlock                  # opens browser for PIN/password (default)
due2-cli unlock --prompt         # interactive terminal prompt instead of browser
due2-cli unlock --no-browser     # print URL instead of opening browser

# Lock — clear cached DEK
due2-cli lock
due2-cli lock --forget           # also remove from keychain

# Non-interactive (CI/scripts):
DUE2_MASTER_PASSWORD=... due2-cli list --json
DUE2_PIN=1234 due2-cli list --json

# Version
due2-cli version --json
due2-cli --version
```

Encryption mode is set per user in the mobile app: `pin` (4-digit) or `password` (master password, default). The CLI auto-detects the mode from the server and prompts accordingly. Both modes use Argon2id KDF with the same parameters. The `unlock` command opens a local browser page by default for secure PIN/password input — credentials never appear in shell history or process lists.

Data commands (`list`, `add`, `edit`, `delete`, `archive`, `restore`, `bulk`, `group`, `show`, `renew`, `summary`, `pack`, `todo`) require a DEK. The DEK is auto-resolved from env vars, OS keychain cache, or interactive prompt — explicit `unlock` is not required. Group DEKs are also auto-resolved using the personal DEK when `recovery_encrypted_dek` is available. Use `unlock` explicitly only to cache credentials in the keychain or to handle legacy group keys without `recovery_encrypted_dek`. Non-data commands (`login`, `logout`, `whoami`, `lock`, `schema`, `settings`, `feedback`, `account`, `public`, `plan`, `version`) do not need a DEK.

## Output

Always use `--json` for machine-readable output. Errors go to stderr as `{"error":"..","code":N}`. Exit codes: 0=ok, 1=error, 3=bad_input, 64=usage. All list commands support `-l` (limit, 1-100, default 20) and `-p` (page, default 1). JSON output includes `total_count`, `page`, `total_pages`.

## Core CRUD

```bash
# Add
due2-cli add -t "Netflix" -d 2026-07-01 -c subscription --price 17000 --currency KRW --recurrence monthly --json
due2-cli add -t "Car repair" -d 2026-08-01 -c vehicle --price 300000 --price-max 500000 --currency KRW --json  # price range

# List (default: active, 20/page, sorted by due_at asc)
due2-cli list --json
due2-cli list -s archived --json
due2-cli list -c insurance --due-before 2026-12-31 --sort due_at --desc -l 50 --json
due2-cli list -q "netflix" --json          # client-side search within current page

# Show
due2-cli show <id> --json

# Edit (only pass changed fields)
due2-cli edit <id> -t "New title" --price 20000 --json
due2-cli edit <id> --clear memo,place --json   # clear specific fields

# Delete → trash (recoverable)
due2-cli delete <id> --json
due2-cli delete <id> --permanent --force --json  # permanent
due2-cli delete --empty-trash --force --json     # empty all trash

# Archive / Restore
due2-cli archive <id> --json
due2-cli restore <id> --json

# Renew — advance due_at by recurrence, skipping past dates
due2-cli renew <id> --json
```

### Categories

subscription, insurance, telecom, membership, health, finance, work, tax, vehicle, appointment, government, education, utility, realestate, anniversary, travel, food, coupon, warranty, delivery, event, family, hobby, visa, legal, pet, etc

### Recurrence

none, daily, weekly, biweekly, monthly, quarterly, semiannual, yearly, custom (with --recurrence-interval + --recurrence-unit)

### Post-expiry action

archive (default), delete, auto_renew

### Notification

`--notify-offsets` accepts comma-separated minutes: 0=due day, 1440=1d before, 10080=7d, 43200=30d. `--notify-time` sets time (HH:mm, default 09:00).

## Bulk Operations

Use when adding/editing/deleting 2+ items. Stdin JSON, 50-item limit.

```bash
# Bulk add
echo '[
  {"title":"Car insurance","due_at":"2026-08-15","category":"insurance","price":450000,"currency":"KRW","recurrence_type":"yearly"},
  {"title":"Visa renewal","due_at":"2026-09-01","category":"visa"}
]' | due2-cli bulk add --json

# Bulk edit
echo '[
  {"id":"<uuid>","title":"Updated title"},
  {"id":"<uuid>","price":30000}
]' | due2-cli bulk edit --json

# Bulk delete (array of IDs)
echo '["<uuid>","<uuid>"]' | due2-cli bulk delete --trash --json
echo '["<uuid>","<uuid>"]' | due2-cli bulk delete --force --json   # permanent

# Bulk archive / restore
echo '["<uuid>","<uuid>"]' | due2-cli bulk archive --json
echo '["<uuid>","<uuid>"]' | due2-cli bulk restore --json
```

## Groups & Sharing

Groups enable shared deadlines with encrypted key distribution.

```bash
# Create group
due2-cli group create -n "Family" --json

# Invite member (by email)
due2-cli group invite -e user@example.com --json
# --group <uuid> if multiple groups; auto-selected if only one

# Invitee accepts or declines
due2-cli group accept --json
due2-cli group decline --json

# Owner distributes encryption key
due2-cli group key-distribute --json

# Invitee receives key
due2-cli group key-receive --json

# Share an item to group
due2-cli share add -i <item-id> -u <user-id> -g <group-id> --json
due2-cli share add -i <item-id> -u <user-id> -g <group-id> -p write --json  # write permission

# List shares for an item
due2-cli share list <item-id> --json
due2-cli share list <item-id> -l 50 -p 2 --json    # paginated

# Remove a share
due2-cli share remove <share-id> --json

# Accept a copied item
due2-cli share accept <source-item-id> --json

# Check copy status of an item
due2-cli share status <item-id> --json

# Add item directly to group
due2-cli add -t "Rent" -d 2026-07-01 -c realestate --group <group-id> --share-mode share --targets all --json

# Bulk share
echo '[
  {"item_id":"<uuid>","user_id":"<uuid>","group_id":"<uuid>","permission":1}
]' | due2-cli bulk share --json

# List group members
due2-cli group members --json

# Other group operations
due2-cli group list --json
due2-cli group list -l 50 -p 2 --json              # paginated
due2-cli group show --json
due2-cli group update -n "New Name" --json
due2-cli group kick -u <user-id> --group <uuid> --json
due2-cli group transfer -u <user-id> --force --json
due2-cli group leave --force --json
due2-cli group delete --force --json
```

Member status flow: pending → accepted → keyReady (after key-distribute/key-receive).

## Packs

Organize related items into named collections.

```bash
# Pack CRUD
due2-cli pack create -n "2026 Tax Season" --description "Tax deadlines" --start-date 2026-01-01 --target-date 2026-05-31 --json
due2-cli pack list --json
due2-cli pack list -l 50 -p 2 --json               # paginated
due2-cli pack show <id> --json
due2-cli pack edit <id> -n "Updated Name" --json
due2-cli pack archive <id> --json
due2-cli pack restore <id> --json
due2-cli pack delete <id> --force --json

# Add item to pack (at creation)
due2-cli add -t "종합소득세 신고" -d 2026-05-31 -c tax --pack <pack-id> --json

# Move existing item to pack
due2-cli edit <item-id> --pack <pack-id> --json

# Remove item from pack
due2-cli edit <item-id> --clear pack_id --json
```

## Todo

Simple checklist items grouped into projects — no due date, unlike the core CRUD above. Both the project `name` and item `content` are encrypted (`--json` decrypts transparently). Personal-use only (no group sharing).

```bash
# Projects
due2-cli todo project add -n "Home" --json
due2-cli todo project list --json
due2-cli todo project rename <project-id> -n "Renamed" --json
due2-cli todo project delete <project-id> --force --json   # cascades: deletes all items in it

# Items
due2-cli todo add --project <project-id> -c "Buy milk" --json
due2-cli todo list --project <project-id> --json
due2-cli todo list --project <project-id> -s inProgress --json   # pending|inProgress|done
due2-cli todo edit <item-id> -c "Buy oat milk" --json
due2-cli todo status <item-id> -s done --json
due2-cli todo status-message <item-id> -m "Refactoring auth module" --json   # for agents to report progress
due2-cli todo status-message <item-id> -m "27/27 유닛 테스트 통과" -t "테스트중" --tone success --json
due2-cli todo status-message <item-id> --clear --json                       # wipe title/tone/message/timestamp together
due2-cli todo delete <item-id> --json    # soft delete, no restore command

# Old completed items
due2-cli todo list -s done --completed-before 2026-07-01 --json   # cleanup candidates: done items completed before that date
due2-cli todo list --completed-after 2026-07-01 --json            # hide stale done items, keep pending/inProgress + recent done
due2-cli todo delete --purge-done-before 2026-07-01 --force --json  # bulk soft-delete all matching done items, all pages
```

New items are placed at the end of the pending block. Changing status re-places the item within its target block: `inProgress`/`pending` go to the end of their block, `done` goes to the front of its block. Block order is always `[inProgress, pending, done]`. Transitioning to `done` auto-stamps `completed_at` (UTC); transitioning away from `done` clears it — the app uses this to hide old completed items after a few days, no action needed from the CLI side.

`--completed-before`/`--completed-after` (YYYY-MM-DD) filter `todo list` by `completed_at`: `--completed-before` only returns `done` items completed before that date (useful combined with `-s done` to find cleanup candidates); `--completed-after` excludes stale `done` items while leaving pending/inProgress items (which have no `completed_at`) untouched. `todo delete --purge-done-before <date> --force` loops every page of matching `done` items and soft-deletes them — same shape as `delete --empty-trash` for due items.

`status-message` lets an AI agent working on a todo item leave a short note on what it's currently doing. `-m/--message` is the body text; `-t/--title` is an optional short free-text label (e.g. "테스트중") shown next to the relative time in the app's badge — if omitted, the app falls back to a fixed label for the tone; `--tone` is one of `neutral` (default) / `attention` / `success` / `error` and controls the badge's color/icon only (don't invent new tones — put scenario-specific wording in `--title`/`--message`). `status_message_at` is auto-stamped in UTC by the CLI (not user-settable). Every `--message` call replaces title/tone wholesale (not merged with the previous call); `--clear` wipes all four fields and can't be combined with `--message`/`--title`/`--tone`. `todo list`/`todo edit` etc. carry these fields forward automatically — no action needed unless you're specifically updating the status message.

### Content formatting

`todo add`/`todo edit`'s `content` is a plain string in the database — the CLI never parses or stores anything about formatting. The app renders a lightweight inline syntax **for display only** (not applied to project names, not live while a field is being edited in the app), so an agent writing `content` can use it to make items more scannable:

| Syntax | Example | Renders as |
|---|---|---|
| `#tag` | `#긴급` | Colored tag (marker kept) |
| `@word` (start of string or after a space, not inside an email) | `@7/15 세금 신고` | Colored, `@` marker hidden |
| `**text**` | `**중요** 사항` | Bold, markers hidden |
| `` `text` `` | `` 이건 `code` 야 `` | Code-style highlight, backticks hidden |
| `[text]` / `{text}` / `(text)` — only at the very start of the string | `[분류] 문서 준비` | Rounded badge (brackets hidden) |
| URL / domain / email / phone number | `https://example.com` | Auto-linked |
| Date-like text (ISO/Korean/`YYYYMMDD`) | `2026-07-15` | Detected, no special color (just avoids other syntax misfiring on it) |

Escape a trigger character with `\` to keep it literal (e.g. `\#not-a-tag`). No priority/`!` syntax exists. This is purely cosmetic — don't build any of this into CLI output; `todo list`'s plain-text mode just prints the raw string as-is.

## Public Packs & Items

Browse, follow, and save public packs and items. No DEK/unlock needed — just login.

```bash
# Browse public packs
due2-cli public packs --json
due2-cli public packs -c tax --json                     # filter by category
due2-cli public packs -q "세금" --json                   # search title/description
due2-cli public packs -l 50 -p 2 --json                  # paginated

# Show pack details + items
due2-cli public show <pack-id> --json
due2-cli public show <pack-id> -l 50 -p 2 --json        # paginated items

# Follow / unfollow a pack
due2-cli public follow <pack-id> --json
due2-cli public unfollow <pack-id> --json

# List followed packs
due2-cli public following --json
due2-cli public following -l 50 -p 2 --json              # paginated

# List recommended items from followed packs
due2-cli public items --json
due2-cli public items --pack <pack-id> --json           # filter by pack
due2-cli public items -l 50 -p 2 --json                  # paginated

# Save / unsave a public item
due2-cli public save <item-id> --json
due2-cli public unsave <item-id> --json

# List saved items
due2-cli public saved --json
due2-cli public saved -l 50 -p 2 --json                  # paginated

# Submit feedback on a saved item (requires save first)
due2-cli public feedback <item-id> --accurate true --rating 4 --message "정확해요" --json
due2-cli public feedback <item-id> --accurate false --json   # accuracy only

# List feedbacks for a public item
due2-cli public feedbacks <item-id> --json
due2-cli public feedbacks <item-id> --limit 50 --offset 20 --json

# Show producer profile (tier, accuracy, rating)
due2-cli public producer <user-id> --json

# Report a public item
due2-cli public report <item-id> --reason inaccurate --json
due2-cli public report <item-id> --reason spam -m "Details here" --json
# reasons: inaccurate, spam, inappropriate, duplicate, other
# optional message: -m (max 200 chars)

# Discover popular and recent public packs
due2-cli public discover --json
due2-cli public discover --popular-limit 10 --recent-limit 10 --json
# returns: popular packs, recent packs, categories with counts
# country auto-detected from system locale
```

Follow limits by plan: Free=3, Plus=20, Pro=50. Saved items reference the original (always up-to-date, no copy).
Producer tiers: new → contributor (5+ items, 70%+ accuracy) → trusted (20+ items, 90%+ accuracy) → official.

## Dashboard & Account

```bash
# Summary — overdue, urgent (D-0~D-3), this month, upcoming
due2-cli summary --json

# Plan — tier, expiry, usage vs limits (active/archived/trashed/groups/AI)
# Avoid frequent calls; use only when quota check is needed (e.g. before bulk add)
due2-cli plan --json

# Account info
due2-cli account show --json
due2-cli whoami --json

# Settings (timezone, notify times, encryption mode — no plan info)
due2-cli settings show --json
due2-cli settings update --timezone Asia/Seoul --notify-times 09:00,18:00 --json

# Feedback (daily limit: free=1, plus=3, pro=5)
due2-cli feedback send -m "Bug report" --type bug --json
due2-cli feedback list --json
due2-cli feedback list -l 50 -p 2 --json  # paginated

# Bulk feedback (stdin JSON, sends up to daily limit remaining)
echo '[{"message":"Bug report","type":"bug"},{"message":"Feature request","type":"suggestion"}]' | due2-cli feedback bulk-send --json

# Account deletion (irreversible)
due2-cli account delete --force --json
```

## Workflow Guide

1. **First time setup** → `login --unlock` (login + unlock in one step; plain `login` also works since DEK is auto-resolved on first data command, but `--unlock` avoids a second prompt)
2. **Single item** → `add`, `edit`, `delete`, `show`
3. **Multiple items at once** → `bulk add`, `bulk edit`, `bulk delete`, `bulk archive`, `bulk restore` (stdin JSON, max 50)
4. **Need collaborators** → create `group` → `invite` → `key-distribute` → `share add` or `--share-mode share` on add
5. **Recurring items expired** → `renew` to advance to next cycle
6. **Organize items** → `pack create` → `add --pack <id>` or `edit --pack <id>` to associate items, `edit --clear pack_id` to detach
7. **Discover public schedules** → `public discover` for popular/recent overview → `public packs` to browse → `public follow <pack-id>` → `public items` to see recommendations → `public save <item-id>` to bookmark → `public report <item-id>` to flag problematic items
8. **Check what's urgent** → `summary` for overdue/urgent/upcoming overview, `plan` for tier/usage
9. **Simple checklists (no due date)** → `todo project add` → `todo add --project <id>` → `todo status <id> -s done`
10. **Scripting/CI** → set `DUE2_MASTER_PASSWORD` or `DUE2_PIN` env var, always use `--json`

## Schema

Run `due2-cli schema --json` for the full machine-readable command spec.
