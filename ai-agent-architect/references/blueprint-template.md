# Implementation Blueprint Template

Save as `ai-agent-blueprint-[topic-slug].md` in the user's workspace. Adapt depth and language based on detected audience type (technical / business / mixed).

---

```markdown
# AI Agent Architecture Blueprint
> Generated from Deep Interview Session — [date]
> Ambiguity at decision: [X]% | Interview rounds: [N]

## Executive Summary
[2-3 sentences: what we're building, why this architecture, expected impact]

## Requirements Summary
[Distilled directly from the crystallized interview spec — NOT generic assumptions]

### Goal
[from interview]

### Constraints
[from interview — budget, timeline, team, compliance, performance]

### Domain Map
[from interview — systems, integrations, data flows]

### Success Criteria
[from interview — specific metrics with target values]

## Recommended Architecture: [Pattern Name]

### Why This Pattern
[Decision rationale from Phase 2, referencing interview evidence]

### Why Not [Alternative 1]
[Specific reason tied to user's constraints]

### Why Not [Alternative 2]
[Specific reason tied to user's constraints]

### Architecture Diagram
<!-- INSTRUCTION: Generate a Mermaid diagram using the matching template from references/mermaid-templates.md. Customize with actual component names from the interview. Follow the customization checklist. Do NOT include this comment or any reference file paths in the output. -->

[Mermaid diagram here]

### Component Design

#### [Agent/Component 1]: [Role Name]
- **Responsibility:** [what it does]
- **Model recommendation:** [which model tier and why]
- **Tools/integrations:** [what it connects to via MCP or API]
- **Skills:** [domain-specific capability packages]
- **Context strategy:** [how it manages context window]

[Repeat for each component]

### Communication & Coordination
[How components interact — tool calls, message passing, shared memory]
[Context management — editing, memory tools, pagination, capping at ~25K tokens]

### Observability Plan
[Structured logging strategy]
[Prompt chain and reasoning trace visibility]
[Token consumption monitoring]
[Key dashboards and alerts]

## Implementation Roadmap

### Phase 1: Foundation (Weeks 1-N)
[Start with simplest viable version]
[Specific deliverables and acceptance criteria]

### Phase 2: Enhancement (Weeks N-M)
[Add complexity where it delivers measurable value]
[Trigger conditions for this phase]

### Phase 3: Scale (Weeks M+)
[Evolution toward full architecture]
[What signals indicate readiness]

## Cost Estimation
[Token usage projections per component]
[Monthly API cost estimate by phase]
[Comparison: current process cost vs. agent cost]
[Model tier recommendations with cost/performance tradeoff]

## Risk Assessment
[Top 3-5 risks with impact × probability]
[Mitigation strategy for each]
[Fallback plan if architecture choice proves wrong]

## Success Metrics
[Tied directly to interview Success Criteria]
[Measurement method and cadence]
[Decision thresholds: what score = success, what = pivot]

## Evolution Path
<!-- INSTRUCTION: Use the Evolution Roadmap structure from module-templates.md as a guide. Do NOT include this comment or any reference file paths in the output. -->
[Phase-by-phase architecture evolution]
[Trigger signals for each evolution step]
[Reference pattern: single → routing → specialized → multi-agent → evaluator]

## Appendix: Interview Transcript
[Full Q&A from the deep interview with scores per round]
```
