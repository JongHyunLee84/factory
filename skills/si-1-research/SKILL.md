---
name: si-1-research
user-invocable: true
description: 시장/기술 리서치 — 인터뷰 + 토픽 분해 + 병렬 Agent 실행 + 종합
---

# SI Research Phase

You are conducting structured research for the SI workflow. You decompose the research scope through an interview, propose focused topics, execute them in parallel via Agent subagents, and synthesize a unified report.

## Input
- User's project idea or problem statement
- Any URLs, references, or constraints provided

## Execution

### Step 1: Research Interview

Use `AskUserQuestion` to understand what needs to be researched. Batch up to 4 questions per call.

**Core questions** (skip any already answered by user input):

```
AskUserQuestion(questions=[
  {
    question: "리서치가 필요한 주요 영역은 무엇입니까?",
    header: "리서치 영역",
    multiSelect: true,
    options: [
      { label: "시장/경쟁 분석", description: "경쟁 제품, 시장 규모, 포지셔닝" },
      { label: "기술 스택 평가", description: "라이브러리, 프레임워크, 서비스 비교" },
      { label: "API/서비스 조사", description: "외부 API, SaaS, 가격/제한 확인" },
      { label: "사용자/도메인 리서치", description: "사용자 행동, 도메인 지식, 규제" }
    ]
  },
  {
    question: "특별히 조사해야 할 기술이나 서비스가 있습니까?",
    header: "특정 기술",
    multiSelect: false,
    options: [
      { label: "있음 (직접 입력)", description: "Other에 구체적 이름을 작성해주세요" },
      { label: "없음, 자유롭게 탐색", description: "리서치 중 발견되는 기술을 포함" }
    ]
  },
  {
    question: "리서치 깊이는 어느 수준입니까?",
    header: "깊이",
    multiSelect: false,
    options: [
      { label: "탐색적 (Recommended)", description: "넓게 훑고 유망한 방향 식별" },
      { label: "심층 비교", description: "주요 옵션 2-3개를 깊이 비교" },
      { label: "PoC 포함", description: "핵심 기술 가정을 코드로 검증" }
    ]
  },
  {
    question: "리서치에서 제외할 영역이 있습니까?",
    header: "제외 영역",
    multiSelect: false,
    options: [
      { label: "없음", description: "모든 관련 영역을 포함" },
      { label: "있음 (직접 입력)", description: "Other에 제외할 영역을 작성해주세요" }
    ]
  }
])
```

Follow-up questions are allowed if answers reveal new ambiguity. Stop when the research scope is clear.

### Step 2: Topic Proposal

Based on interview answers, propose **3-7 research topics**. Each topic:

```markdown
## Proposed Research Topics

| # | Topic | Description | Focus Questions |
|---|-------|-------------|-----------------|
| 1 | [제목] | [1문장 설명] | 1. [질문1] 2. [질문2] |
| 2 | ... | ... | ... |
```

Design principles:
- Each topic is **independently researchable** (no cross-topic dependencies)
- Cover all areas identified in the interview
- Each topic has 2-3 specific focus questions that guide the research
- Topics should be sized for a single Agent subagent (not too broad, not too narrow)

### Step 3: User Approval

Present the topic list and ask for approval:

```
AskUserQuestion(questions=[
  {
    question: "위 리서치 토픽 목록이 적절합니까?",
    header: "토픽 승인",
    multiSelect: false,
    options: [
      { label: "좋아요, 진행해요", description: "이 토픽들로 병렬 리서치를 시작합니다" },
      { label: "일부 수정 필요", description: "수정할 내용을 알려주세요" },
      { label: "토픽 추가 필요", description: "빠진 토픽을 알려주세요" }
    ]
  }
])
```

Iterate until the user approves. Then proceed.

### Step 4: Parallel Research Execution

Spawn **one Agent subagent per topic** in a single turn (all in parallel).

Each Agent receives:
- `subagent_type`: `"general-purpose"`
- `mode`: `"bypassPermissions"`
- Topic title, description, and focus questions
- Project context (project name, problem statement, user-provided URLs)
- Output path: `tasks/research/topic-N-[slug].md`
- Instruction to read `skills/si-deep-research/SKILL.md` and follow **Focused-Topic Mode**

**Agent prompt template:**

```
You are a research agent for the SI workflow.

## Your Topic
- Title: [topic title]
- Description: [topic description]
- Focus Questions:
  1. [question 1]
  2. [question 2]
  3. [question 3]

## Project Context
- Project: [project name]
- Problem: [problem statement]
- URLs/References: [if any]

## Instructions
1. Read `skills/si-deep-research/SKILL.md`
2. Follow the **Focused-Topic Mode** described in that skill
3. Write your sub-report to: [output path]
4. Use the Topic Sub-Report template from that skill

Do NOT ask the user questions — work autonomously with the information provided.
```

**IMPORTANT**: Spawn ALL agents in the same turn using multiple Agent tool calls. Do not spawn them sequentially.

### Step 5: Collect & Verify

After all agents complete:

1. Verify each sub-report file exists at `tasks/research/topic-N-[slug].md`
2. Check each sub-report has the required sections (Focus Questions, Key Findings, Gaps & Unknowns, Sources)
3. If any sub-report is missing or incomplete, note which topics need attention

### Step 6: Synthesize

Read ALL sub-reports and synthesize into `tasks/research-report.md`.

This is **integration, not concatenation**:
- Cross-reference findings across topics
- Identify patterns, contradictions, and gaps
- Build a unified narrative

Use the SI research template from `si-deep-research/SKILL.md` Step 4:

```markdown
# Research Report: [Project Name]

## 1. Problem Statement
## 2. Market/Competition Analysis
## 3. Technical Landscape
## 4. PoC Results (if conducted)
## 5. Feasibility Assessment
## 6. Recommendation
## Sources
```

Each section draws from multiple sub-reports. Add a "Synthesized from" note:
```markdown
> Synthesized from [N] topic reports in `tasks/research/`
```

### Step 7: Update Progress

Update `tasks/si-progress.json`:
- Set `phases.research.status = "completed"`
- Add `tasks/research-report.md` to `artifacts`
- Add each sub-report path to `artifacts`
- Set `completedAt` to current timestamp
- Set `phases.research.data`:

```json
{
  "topics": [
    { "id": "topic-1", "title": "[title]", "status": "completed", "subReport": "tasks/research/topic-1-[slug].md" }
  ],
  "synthesizedFrom": "tasks/research/"
}
```

Then inform the user:
"리서치가 완료되었습니다. `/si-start`로 돌아가서 다음 단계를 확인하세요."

## Output

- `tasks/research-report.md` — final synthesized report
- `tasks/research/topic-N-*.md` — individual topic sub-reports
