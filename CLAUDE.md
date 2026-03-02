# SI Workflow Plugin

Claude Code 플러그인 — Research → PRD → Analysis → Architect → UI Design → TDD → Develop → E2E → Acceptance 9단계 소프트웨어 개발 워크플로우.

---

## 필수 규칙 — 버전 범프

`commands/`, `skills/`, `hooks/`, `settings/` 내 파일을 변경할 때 **반드시** 아래 두 파일의 `version`을 함께 범프한다.

| 파일 | 필드 |
|------|------|
| `.claude-plugin/plugin.json` | `"version"` |
| `.claude-plugin/marketplace.json` | `plugins[0].version` |

**SemVer 기준:**
- **patch** (0.2.0 → 0.2.1): 버그 수정, 오타, 문구 변경
- **minor** (0.2.0 → 0.3.0): 새 커맨드/스킬 추가, 기존 동작 변경
- **major** (0.x → 1.0): 호환성 깨지는 변경 (커맨드 삭제, 스킬 인터페이스 변경)

> 버전을 범프하지 않으면 marketplace update 시 캐시가 갱신되지 않아 사용자가 변경사항을 받지 못한다.

---

## 크로스 레퍼런스 업데이트 체크리스트

이름이나 경로를 변경할 때 아래 참조를 함께 업데이트해야 한다.

### 커맨드 이름 변경 시

1. `commands/start.md` — Phase Router 테이블 (9개 엔트리)
2. 각 커맨드의 완료 메시지 (`/si:start` 참조)
3. `commands/7-develop.md` — `/si:6-tdd`, `/si:4-architect` 참조
4. `commands/8-e2e.md` — `/si:9-acceptance` 참조
5. `skills/tdd/SKILL.md` — `/si:8-e2e` 참조
6. `settings/templates/analysis.md` — attribution 코멘트 (`/si:3-analysis`)
7. `settings/templates/design.md` — attribution 코멘트 (`/si:4-architect`)
8. `README.md` — 커맨드 테이블

### 스킬 이름 변경 시

1. 호출하는 커맨드의 `Skill("si:...")` 호출문
2. 스킬 자체 `SKILL.md` frontmatter `name:` 필드
3. `README.md` — 스킬 테이블

### settings/rules 파일 변경 시

1. 참조하는 커맨드: `start.md`(design-review), `2-prd.md`(ears-format), `3-analysis.md`(gap-analysis), `4-architect.md`(design-review)
2. `skills/prd/SKILL.md` — ears-format 참조

### settings/templates 파일 변경 시

1. 참조하는 커맨드: `3-analysis.md`(analysis.md), `4-architect.md`(design.md), `start.md`(si-progress.json)
2. 템플릿 내부 attribution 코멘트

### tasks/ 아티팩트 경로 변경 시

| 아티팩트 | 영향 범위 |
|----------|----------|
| `tasks/si-progress.json` | 모든 커맨드 + session-start hook (가장 영향 큼) |
| `tasks/design.md` | 6개 커맨드 + 3개 스킬 |
| `tasks/requirements.md` | 5개 커맨드 + 2개 스킬 |
