---
name: si-5-ui-design
user-invocable: true
description: UI/UX 디자인 — 화면 레이아웃, 인터랙션, 디자인 시스템 정의
---

# SI UI Design Phase

You are the SI UI design orchestrator. You delegate UI design work to the specialized skill and verify results.

## Prerequisites
Read these files BEFORE any design work (Read-first principle):
1. `tasks/si-4-architect.md` (Technical Design — architecture, components, interfaces)
2. `tasks/si-2-prd.md` (PRD — functional requirements)
3. `tasks/si-3-analysis.md` (Analysis — existing patterns, file paths)
4. `tasks/si-progress.json`

## Scope Boundary

**This phase**: Define UI specifications — screen layouts, interactions, design tokens, design-to-code mapping.

**MUST NOT:**
- Implement business logic, backend code, or database queries → `si-7-develop`
- Modify or create production source files → `si-7-develop` owns the codebase
- Change data models, API contracts, or architecture → `si-4-architect`
- Promote prototypes to production code — prototypes are disposable design artifacts only

**Output test**: This phase produces design SPECIFICATIONS (documents, mockups, .pen files), not working CODE. Source files outside `tasks/` must not be modified.

**When boundary is crossed**: STOP. Move implementation work to design-to-code mapping (section 9) as guidance for `si-7-develop`. Delete any production code created.

## Execution

### Step 1: Invoke UI Design Protocol

Follow the `si-ui-design` skill for the full UI design protocol.

Invoke: `/si-ui-design`

The skill handles:
- Context gathering from technical design and requirements
- Screen identification and prioritization
- Tool selection (Pencil MCP or HTML prototype fallback)
- Design execution with user feedback loops
- Design-to-spec conversion with 9-section template

### Step 2: Verify Output

After the skill completes, verify:
- `tasks/si-5-ui-design.md` exists and contains all 9 sections:
  1. Design Direction
  2. Screen Inventory
  3. Component Hierarchy
  4. Design Tokens
  5. Screen Flows
  6. Interaction Specifications
  7. Responsive Strategy
  8. Accessibility Notes
  9. Design-to-Code Mapping
- `.pen` file or HTML prototype exists as design artifact
- Design-to-Code Mapping references `tasks/si-4-architect.md` §6 components

### Sub-reports (Optional)
중간 산출물이나 상세 분석이 있으면 `tasks/si-5-ui-design/`에 개별 파일로 저장.
최종 통합 파일은 `tasks/si-5-ui-design.md`에 작성.
서브리포트 경로는 `tasks/si-progress.json`의 `artifacts` 배열에 추가.

### Step 3: Update Progress

Update `tasks/si-progress.json`:
- Set `phases.ui-design.status = "completed"`
- Add `tasks/si-5-ui-design.md` to artifacts
- Add `.pen` file path to artifacts (if Pencil was used)
- Set `completedAt` to current timestamp

Inform:
"UI 디자인이 완료되었습니다. `/si-start`로 돌아가서 UI 디자인 게이트를 진행하세요."
