# due2-cli AI 코딩 에이전트 플러그인

[English](../README.md) | [日本語](README.ja.md)

[due2-cli](https://github.com/myiam-io/due2-cli)용 AI 코딩 에이전트 명령어 레퍼런스 — 마감 항목(구독, 보험, 세금, 비자 등)을 종단간 암호화로 터미널에서 관리합니다.

## 설치

### Claude Code

```bash
# 마켓플레이스 등록 (최초 1회)
claude plugin marketplace add due2-app/due2-cli-plugin

# 플러그인 설치
claude plugin install due2@due2
```

로컬 개발:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
claude --plugin-dir ./due2-cli-plugin
```

### OpenAI Codex CLI

```bash
# 마켓플레이스 등록 (최초 1회)
codex plugin marketplace add due2-app/due2-cli-plugin

# 플러그인 설치
codex plugin install due2
```

로컬 개발:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
codex --plugin-dir ./due2-cli-plugin
```

### Google Gemini CLI

```bash
gemini extensions install https://github.com/due2-app/due2-cli-plugin
```

로컬 개발:

```bash
git clone https://github.com/due2-app/due2-cli-plugin.git
gemini extensions link ./due2-cli-plugin
```

## 사전 요구사항

[due2-cli](https://github.com/myiam-io/due2-cli) 설치:

```bash
# macOS (Homebrew)
brew tap myiam-io/tap
brew install due2-cli

# Windows (winget)
winget install myiam.Due2.CLI
```

로그인 및 잠금 해제:

```bash
due2-cli login
due2-cli unlock
```

## 포함 내용

| 에이전트 | 파일 | 범위 |
|---|---|---|
| Claude Code | `skills/due2/SKILL.md` | `/due2` 스킬 — 마감/구독 관련 질문 시 자동 트리거 |
| Codex CLI | `skills/due2/SKILL.md` | `@due2` 스킬 — 마감/구독 관련 질문 시 자동 트리거 |
| Gemini CLI | `skills/due2/SKILL.md` | extension 스킬 — 마감/구독 관련 질문 시 자동 트리거 |

모든 파일이 due2-cli 전체 명령어 레퍼런스를 포함합니다:
- 설치 및 설정 (login, unlock, lock, CI용 환경 변수)
- CRUD (add, list, show, edit, delete, archive, restore, renew)
- 벌크 작업 (add, edit, delete, archive, restore, share)
- 그룹 및 공유 (E2EE 키 배포)
- 일정팩 (항목 모음)
- 퍼블릭 일정팩 및 항목 (탐색, 팔로우, 저장, 피드백, 신고, 디스커버)
- 대시보드 및 계정 관리 (summary, plan, settings, feedback)

## 업데이트

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

## 제거

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

## 라이선스

Apache 2.0. 자세한 내용은 [LICENSE](../LICENSE)를 참고하세요.
