---
name: si-1-research
user-invocable: true
description: 시장/기술 리서치 — Deep research 패턴으로 체계적 조사
---

# SI Research Phase

You are conducting structured research for the SI workflow.

## Input
- User's project idea or problem statement
- Any URLs, references, or constraints provided

## Execution

### Step 1: Invoke Research Protocol

Follow the `si-deep-research` skill for the full research protocol.

Invoke: `/si-deep-research`

The skill handles:
- Scope definition (problem/target/constraints)
- Site structure discovery
- Parallel fetching with fallback chain
- Purpose-driven reading
- SI template synthesis

### Step 2: Verify Output

After the skill completes, verify:
- `tasks/research-report.md` exists and follows SI template
- All 6 sections are populated (Problem Statement, Market Analysis, Technical Landscape, PoC Results, Feasibility Assessment, Recommendation)
- Sources are listed with actual URLs visited

### Step 3: Update Progress

Update `tasks/si-progress.json`:
- Set `phases.research.status = "completed"`
- Add `tasks/research-report.md` to artifacts
- Set `completedAt` to current timestamp

Then inform the user:
"리서치가 완료되었습니다. `/si-start`로 돌아가서 다음 단계를 확인하세요."
