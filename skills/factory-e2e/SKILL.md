---
name: factory-e2e
user-invocable: true
description: E2E 테스트 실행 — 수용 기준 기반 통합 테스트 검증
---

# Factory E2E

You are the Factory E2E testing guide. You verify the implementation against acceptance criteria through end-to-end testing.

## Recommended Inputs

- `factory/architect/architect.md` — 수용 기준(AC) 목록 (필수)

## Scope Boundary

**This skill**: Execute E2E tests and record results. This skill is a **verifier**, not a **fixer**.

**MUST NOT:**
- Modify production source code to fix bugs → report and route to `factory-develop`
- Alter acceptance criteria or design → `factory-architect`
- Add new features while "fixing" tests → `factory-prd`
- Skip or weaken test scenarios to achieve passing → record honest FAIL verdicts

**Bug handling protocol**: When a test fails:
1. Record failure with evidence (error output, expected vs actual)
2. Classify: Implementation Bug / Design Gap / Test Error
3. Do NOT fix. Route to `factory-develop` (bugs) or `factory-architect` (design gaps).

**When boundary is crossed**: STOP. Revert any production code changes. Re-run the test for an honest result.

## Execution Flow

### Step 0: Decision Agenda

> 공유 프로토콜: `settings/templates/decision-agenda.md`

#### 0-1. Read Prior Decisions
이전 스킬의 decisions 파일을 읽는다 (있으면):
- `factory/architect/decisions.md`
- `factory/tdd/decisions.md`
- `factory/develop/decisions.md`

이미 결정된 항목은 "Pre-decided"로 표시하고 skip.

#### 0-2. Generate Agenda
입력과 컨텍스트를 분석하여 아래 결정 항목을 기본 Agenda로 구성.

**Tier 1 (Strategic — 사용자 필수):**

| ID | Decision | 비위임 |
|----|----------|--------|
| E-D-001 | AC별 테스트 방법 (자동/스크립트/수동) | No |
| E-D-002 | **커버리지 기준 (몇 % 이상 통과)** | Yes |

**Tier 2 (Tactical — AI 기본값 + 사용자 오버라이드):**

| ID | Decision | AI 기본값 |
|----|----------|----------|
| E-D-003 | 실패 분류 확인 (버그 vs 설계갭 vs 테스트오류) | Step 3 Failure Triage에서 자동 분류 |
| E-D-004 | 회귀 범위 | 전체 테스트 스위트 (unit + integration + E2E) |

#### 0-3. Agenda Overview
`AskUserQuestion`으로 진행 방식 확인:
- **핵심만 검토 (Recommended)**: Tier 1만 직접, Tier 2는 AI 기본값
- **전체 검토**: Tier 1 + Tier 2 모든 결정을 직접
- **알아서 해줘**: AI가 결정하고 근거 기록. E-D-002(커버리지 기준)만 반드시 확인

#### 0-4. Decision Walk-through
선택에 따라 `AskUserQuestion`으로 결정 수집 (최대 4개/배치, 의존성 순서 준수).
- E-D-001은 Step 1에서 Test Inventory 작성 후, 각 AC의 Test Method를 사용자에게 확인한다.
- E-D-002는 Step 5 Coverage Report 전에 사용자에게 기준을 확인한다.

#### 0-5. Record Decisions
`factory/e2e/decisions.md`에 기록:

| ID | Decision | Choice | Rationale | Decided By | Date |
|----|----------|--------|-----------|------------|------|

#### 0-6. Proceed
모든 선행 결정 기록 후 Step 1로 진행.

---

### Step 1: E2E Test Inventory

Extract ALL acceptance criteria from `factory/architect/architect.md` section 8.
Map each to an E2E test scenario:

| AC ID | Scenario | Test Method | Status |
|-------|----------|-------------|--------|
| AC-001 | [Given/When/Then summary] | Automated / Manual | Pending |
| AC-002 | ... | | |

**Test Method Selection** (choose the highest feasible level):
- **Automated** (preferred): Project has E2E test infrastructure (Cypress, Playwright, etc.) AND scenario involves deterministic UI/API flows
- **Script**: No E2E framework but verifiable via CLI/API calls, or automated test would be disproportionately complex for the scenario
- **Manual** (last resort): Requires visual/UX verification, subjective assessment, or physical device interaction — provide exact reproduction steps

### Step 2: Execute Tests

For each scenario:

#### Automated Tests
1. Write or verify E2E test exists for the acceptance criterion
2. Run the test
3. Record: PASS / FAIL + evidence (output, screenshot, log)

#### Script Tests
1. Write a test script or sequence of commands
2. Execute
3. Record: PASS / FAIL + evidence

#### Manual Tests
1. Provide step-by-step reproduction:
   ```
   1. [action]
   2. [action]
   3. Verify: [expected state]
   ```
2. Ask user to confirm: PASS / FAIL

### Step 3: Failure Triage

For each failing test:

| AC ID | Failure | Root Cause Category | Action |
|-------|---------|-------------------|--------|
| AC-NNN | [what failed] | Implementation Bug / Design Gap / Test Error | [fix action] |

- **Implementation Bug** → Fix in code, re-run
- **Design Gap** → Note for factory-code-review, may need design revision
- **Test Error** → Fix the test, re-run

### Step 4: Regression Check

Run the complete test suite (unit + integration + E2E):

```
Test Results:
- Unit:        N/N passing
- Integration: N/N passing
- E2E:         N/N passing
- Total:       N/N passing
```

ANY failure → investigate and fix before proceeding.

### Step 5: Coverage Report

| Category | Covered | Total | Percentage |
|----------|---------|-------|-----------|
| Functional Requirements | | | |
| Non-Functional Requirements | | | |
| Error Scenarios | | | |
| **Overall** | | | **N%** |

## Output

- E2E test files in the project codebase
- Test results summary saved to `factory/e2e/`
- `factory/e2e/decisions.md` — 의사결정 기록

Inform:
"E2E 테스트가 완료되었습니다. 결과는 `factory/e2e/`와 `factory/e2e/decisions.md`에 저장되었습니다."
