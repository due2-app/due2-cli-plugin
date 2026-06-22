# CLAUDE.md

이 파일은 Claude Code (claude.ai/code)가 이 저장소에서 작업할 때 참고하는 가이드입니다.

## 프로젝트 개요

[due2-cli](https://github.com/myiam-io/due2-cli)용 Claude Code 플러그인. due2-cli는 마감 항목(구독, 보험, 세금, 비자 등)을 종단간 암호화로 관리하는 CLI 도구이며, 이 플러그인은 `/due2` 스킬을 통해 Claude Code에 전체 명령어 레퍼런스를 제공한다.

코드 프로젝트가 **아님** — 빌드, 테스트, 컴파일 대상 소스가 없다. 선언적 설정 파일과 스킬 마크다운 파일로만 구성된다.

## 저장소 구조

- `.claude-plugin/plugin.json` — 플러그인 메타데이터 (이름, 버전, 설명, 키워드)
- `.claude-plugin/marketplace.json` — `claude plugin` 명령어를 통한 마켓플레이스 등록 설정
- `skills/due2/SKILL.md` — `/due2` 스킬 정의. 이 저장소의 핵심 산출물로, Claude Code가 due2-cli를 조작할 때 사용하는 전체 명령어 레퍼런스. frontmatter의 `name`과 `description`이 스킬 자동 트리거 조건을 결정한다.

## 개발

로컬 실행:
```bash
claude --plugin-dir ./due2-cli-plugin
```

`due2-app/due2-cli-plugin` GitHub 저장소에 push하면 배포된다. 사용자 설치:
```bash
claude plugin marketplace add due2-app/due2-cli-plugin
claude plugin install due2-cli@due2-cli
```

## 관련 저장소

- `../due2-cli/` — due2-cli 원본 소스 (Dart). 명령어 변경 시 참조
- `../../myiam-io/homebrew-tap/` — Homebrew Formula/Cask 및 winget 매니페스트. 배포 버전/설치 경로 변경 시 참조

## 스킬 편집

`skills/due2/SKILL.md`가 가장 자주 편집하는 파일이다. due2-cli의 실제 명령어와 항상 동기화 상태를 유지해야 한다. 주요 규칙:

- 모든 명령어 예시에 `--json` 플래그 사용 (기계 판독 가능 출력)
- stdin 기반 벌크 작업은 `echo '...' | due2-cli bulk ...` 패턴 사용
- SKILL.md frontmatter의 `description` 필드가 스킬 자동 트리거를 결정하므로 의도 매칭에 정확하게 유지
- 카테고리, 반복 유형, 플랜 제한 등은 별도 파일 없이 인라인으로 열거
- 하단의 워크플로우 가이드가 사용자 의도 → 명령어 순서를 매핑
