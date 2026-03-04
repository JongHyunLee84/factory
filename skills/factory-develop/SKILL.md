---
name: factory-develop
user-invocable: true
description: 구현 가이드 — 에스컬레이션 체크, 중복 판정, 품질 기준 적용
---

# Factory Develop

You are the Factory development guide. You oversee implementation work, ensuring it follows the design, maintains quality standards, and catches issues early.

## Recommended Inputs

- `factory/architect/architect.md` — 빌드 명세 (필수)
- `factory/analysis/analysis.md` — 선택한 접근 방식, 컨벤션 (권장)

## Scope Boundary

**This skill**: Implement the design from `factory/architect/architect.md` — write production code per specification.

**MUST NOT:**
- Redesign architecture or modify interfaces beyond the design → `factory-architect`
- Add features or behaviors not in the design → `factory-prd`
- Write or modify E2E tests → `factory-e2e`
- Score or judge implementation quality → `factory-code-review`

**Discovery protocol** (during implementation):
- Needed refactoring → log as TODO, don't fix now (unless blocking)
- Missing requirement → log in `factory/prd/prd.md` Open Questions, don't implement
- Design flaw → STOP, note the issue, suggest returning to `factory-architect`
- Unrelated bug → log as separate issue, don't fix in this flow

**When boundary is crossed**: Check `git diff` — if changes exceed the Change Impact Map (design section 9), revert out-of-scope changes and document in your notes.

## Execution Flow

### Step 0: Decision Agenda

> 공유 프로토콜: `settings/templates/decision-agenda.md`

#### 0-1. Read Prior Decisions
이전 스킬의 decisions 파일을 읽는다 (있으면):
- `factory/prd/decisions.md`
- `factory/analysis/decisions.md`
- `factory/architect/decisions.md`
- `factory/ui-design/decisions.md`
- `factory/tdd/decisions.md`

이미 결정된 항목은 "Pre-decided"로 표시하고 skip.

#### 0-2. Generate Agenda
입력과 컨텍스트를 분석하여 아래 결정 항목을 기본 Agenda로 구성.

**Tier 1 (Strategic — 사용자 필수):**

| ID | Decision | 비위임 |
|----|----------|--------|
| DV-D-001 | **구현 순서** — 의존성이 모호할 때 사용자가 우선순위 결정 | No |
| DV-D-002 | **에스컬레이션 응답** — 설계 범위 밖 이슈 발견 시 사용자 결정 | Yes |

**Tier 2 (Tactical — AI 기본값 + 사용자 오버라이드):**

| ID | Decision | AI 기본값 |
|----|----------|----------|
| DV-D-003 | 중복 처리 방향 (4-6점 시) | 공유 추상화 추출 후 구현 |
| DV-D-004 | 컨벤션 추론 확인 | analysis.md의 Conventions Detected 기반 |

#### 0-3. Agenda Overview
`AskUserQuestion`으로 진행 방식 확인:
- **핵심만 검토 (Recommended)**: Tier 1만 직접, Tier 2는 AI 기본값
- **전체 검토**: Tier 1 + Tier 2 모든 결정을 직접
- **알아서 해줘**: AI가 결정하고 근거 기록. DV-D-002(에스컬레이션 응답)만 반드시 확인

#### 0-4. Decision Walk-through
선택에 따라 `AskUserQuestion`으로 결정 수집 (최대 4개/배치, 의존성 순서 준수).
- DV-D-001은 Step 1에서 구현 체크리스트 생성 후, 의존성 순서가 모호한 경우에만 트리거된다.
- DV-D-002는 Step 2의 Escalation Check에서 "Yes" 항목 발생 시 `AskUserQuestion`으로 사용자 결정을 구한다 (기존 "STOP and confirm" → 구조화된 질문으로 전환).

#### 0-5. Record Decisions
`factory/develop/decisions.md`에 기록:

| ID | Decision | Choice | Rationale | Decided By | Date |
|----|----------|--------|-----------|------------|------|

#### 0-6. Proceed
모든 선행 결정 기록 후 Step 1로 진행.

---

### Step 1: Implementation Checklist

From `factory/architect/architect.md`, extract all components and interfaces into an ordered implementation checklist:

```markdown
## Implementation Checklist
- [ ] [Component 1]: [interface/responsibility]
  - Dependencies: [what must exist first]
  - Files: [target paths]
- [ ] [Component 2]: ...
```

Order by dependency — implement leaf dependencies first.

### Step 2: Pre-Implementation Checks (Per Component)

Before writing each component, verify:

#### Escalation Check (shinpr pattern)
ANY "Yes" → STOP and confirm with user:
- [ ] Does this change an architecture layer boundary?
- [ ] Does this add an external dependency not in the design?
- [ ] Does this modify a data model beyond what's designed?
- [ ] Does this affect security or authentication flow?

#### Duplication Check (shinpr 5-criteria)
Score the new component against existing code:

| Criterion | Score (0-2) | Evidence |
|-----------|------------|----------|
| Same input/output signature | | [file:line] |
| Same business rule | | [file:line] |
| Same data access pattern | | [file:line] |
| Same error handling | | [file:line] |
| Same UI pattern | | [file:line] |
| **Total** | **/10** | |

- 0-3: Proceed — genuinely new code
- 4-6: Extract shared abstraction first, then implement
- 7-10: Reuse existing code — extend, don't duplicate

### Step 3: Implementation Guidance

For each component:

1. **Confirm interface** — matches `factory/architect/architect.md` typed signatures
2. **Follow conventions** — from `factory/analysis/analysis.md` conventions section
3. **Error handling** — matches `factory/architect/architect.md` error table
4. **Tests exist** — from TDD phase (if running after factory-tdd)

### Step 4: Quality Checks (Continuous)

After implementing each component:

1. **Lint/Format**: Run project linter
2. **Type Check**: Run type checker (if applicable)
3. **Tests**: Run related tests
4. **Build**: Verify compilation (if applicable)

If any check fails → fix before moving to next component.

### Step 5: Integration Verification

After all components are implemented:

1. Run full test suite
2. Check for any new warnings or deprecation notices
3. Verify no unintended changes (git diff review)
4. Confirm Change Impact Map from design — did changes stay within expected scope?

## Output

- Implementation is done directly in the project codebase.
- `factory/develop/decisions.md` — 의사결정 기록
- Detailed notes or intermediate artifacts may be saved to `factory/develop/`.

Inform:
"구현이 완료되었습니다. `/factory-e2e`로 E2E 테스트를 진행하거나 `/factory-code-review`로 코드 리뷰를 실행하세요."
