# Functional Module Templates

## Table of Contents
1. [Technical Specification Template](#technical-specification-template)
2. [Risk Matrix Template](#risk-matrix-template)
3. [Evolution Roadmap Template](#evolution-roadmap-template)
4. [Performance & Cost Evaluation Plan Template](#performance--cost-evaluation-plan-template)

> **Note:** PRD Template and Cost Analysis structure live in `fallback-modules.md` (single source of truth, used by Phase 4 when external skills are unavailable).

---

## Technical Specification Template

```markdown
# Technical Specification: [Agent Name]
> Architecture: [Pattern from Phase 2] | Date: [date]

## 1. System Architecture

### Architecture Pattern
[Pattern name and rationale summary]

### System Diagram
[Mermaid diagram]

### Component Inventory
| Component | Type | Model | Purpose |
|-----------|------|-------|---------|
| [name] | Agent / Tool / Skill | [model tier] | [brief purpose] |

## 2. Agent Design

### Agent: [Name]

#### System Prompt Design
[Guidelines for the agent's system prompt — role, constraints, output format]
[Recommended prompt structure with placeholders]

#### Tool Specifications
| Tool | Input Schema | Output Schema | MCP Server |
|------|-------------|--------------|------------|
| [name] | { field: type } | { field: type } | [server name or API] |

#### Context Management
- Max context: [token limit]
- Context editing strategy: [when and how to trim]
- Memory tool: [if persistent state needed]
- Response capping: [max output tokens per tool call]

#### Error Handling
| Error Type | Detection | Recovery |
|-----------|-----------|----------|
| Tool failure | [how detected] | [fallback behavior] |
| Context overflow | [threshold] | [trimming strategy] |
| Hallucination risk | [guardrail] | [verification step] |

## 3. Integration Specifications

### MCP Server: [name]
- Transport: stdio / SSE
- Capabilities: tools / resources / prompts
- Authentication: [method]
- Rate limits: [if any]

[Repeat for each integration]

## 4. Data Flow
[Step-by-step data flow from user input to final output]
[Include error/fallback paths]

## 5. Observability
### Logging
[What to log at each step — structured JSON format]

### Tracing
[Distributed trace IDs, prompt chain tracking]

### Metrics
| Metric | Type | Alert Threshold |
|--------|------|----------------|
| [name] | counter/gauge/histogram | [value] |

## 6. Security & Compliance
[Data handling, PII considerations, access control]
[From interview Constraints — compliance requirements]
```

---

## Cost Analysis Structure

For Cost Analysis sheet structure and formatting guidelines, see `fallback-modules.md` (Cost Analysis Spreadsheet Fallback section). That file is the single source of truth for the 4-sheet layout (Token Estimates, Monthly Cost Projection, Break-Even Analysis, Sensitivity Analysis) and includes formatting rules for dynamic spreadsheets.

---

## Risk Matrix Template

```markdown
# Risk Assessment Matrix: [Agent Name]

## Risk Register

| ID | Category | Risk | Probability (1-5) | Impact (1-5) | Score | Mitigation | Owner | Status |
|----|----------|------|--------------------|--------------|-------|------------|-------|--------|
| R-001 | Technical | [description] | [N] | [N] | [P×I] | [strategy] | [role] | Open |

## Risk Categories
- **Technical:** Model performance, integration failures, context limits, latency
- **Operational:** Volume spikes, staff training, process changes, monitoring gaps
- **Financial:** Cost overruns, budget changes, vendor pricing, ROI shortfall
- **Compliance:** Regulatory changes, data privacy, audit requirements

## Risk Heat Map

|            | Impact 1 | Impact 2 | Impact 3 | Impact 4 | Impact 5 |
|------------|----------|----------|----------|----------|----------|
| Prob 5     | 🟡 Mod   | 🟠 High  | 🔴 Crit  | 🔴 Crit  | 🔴 Crit  |
| Prob 4     | 🟢 Low   | 🟡 Mod   | 🟠 High  | 🔴 Crit  | 🔴 Crit  |
| Prob 3     | 🟢 Low   | 🟡 Mod   | 🟡 Mod   | 🟠 High  | 🔴 Crit  |
| Prob 2     | 🟢 Low   | 🟢 Low   | 🟡 Mod   | 🟡 Mod   | 🟠 High  |
| Prob 1     | 🟢 Low   | 🟢 Low   | 🟢 Low   | 🟡 Mod   | 🟡 Mod   |

## Top 3 Risks & Detailed Mitigation
[Expand on the highest-scoring risks with specific action plans]
```

---

## Evolution Roadmap Template

```markdown
# Architecture Evolution Roadmap

## Current State → Target State

### Phase 1: [Pattern] (Now)
[Current architecture — what's being built first]
Trigger to evolve: [specific metric or condition]

### Phase 2: [Enhanced Pattern] (When triggered)
[What changes and why]
Trigger to evolve: [specific metric or condition]

### Phase 3: [Full Pattern] (Long-term)
[Final architecture vision]

## Evolution Decision Tree

Each evolution step must be triggered by measurable signals, not intuition:

If [resolution rate plateaus below target] → add specialized Skills to current agent
If [single agent + Skills still insufficient] → split into supervisor + specialist agents
If [quality variance is high across outputs] → add evaluator/optimizer layer
If [response time exceeds SLA due to sequential bottleneck] → add parallel processing
If [edge cases consuming disproportionate resources] → add single→multi-agent escalation

## Evolution Principles (from Anthropic docs)

1. **Start simple, scale intelligently** — prove ROI with single agents first before adding complexity
2. **Measure everything** — add complexity only when it delivers measurable value
3. **Design for modular evolution** — even your first single agent should have interfaces that support adding more agents later
4. **The best architecture is the simplest one** meeting today's requirements while providing a path to tomorrow's capabilities
5. **Time-to-market reality** — single agent deploys in weeks; multi-agent takes months to get right

## Reference: E-Commerce Evolution Example

This real-world progression illustrates how architecture should evolve with proven need:

| Phase | Architecture | Trigger to Evolve |
|---|---|---|
| 1 | Single agent for customer inquiries | Proving value, measuring baseline metrics |
| 2 | Routing pattern separating order status, product questions, complaints | Single agent accuracy drops as query types diversify |
| 3 | Specialized agents for each category with shared context | Routing alone insufficient; categories need deep domain knowledge |
| 4 | Multi-agent with inventory, payment, shipping coordination | Cross-domain tasks require real-time data from multiple systems |
| 5 | Evaluator agents for QA and continuous improvement | Quality variance across specialist agents needs systematic checking |

**Key takeaway:** Each phase was justified by a specific limitation of the previous phase, not by technical ambition. The organization that can rapidly iterate between simple and complex approaches as business requirements evolve is the one that wins.
```

---

## Performance & Cost Evaluation Plan Template

Save as `eval-plan-[topic-slug].md`. This is a post-launch evaluation framework — NOT a cost estimate (that's in the blueprint).

```markdown
# Performance & Cost Evaluation Plan

## Evaluation Timeline
- **Week 1-2:** Baseline measurement period (shadow mode or limited rollout)
- **Week 3-4:** Primary evaluation window
- **Month 2-3:** Trend analysis and optimization cycle

## Performance Metrics
| Metric | Source | Target | Minimum Acceptable | Measurement Method |
|---|---|---|---|---|
| [From interview Success Criteria] | [System/logs/API] | [Target value] | [Go/no-go threshold] | [How to measure] |

## Cost Metrics
| Cost Category | Projected (from blueprint) | Actual | Variance | Action if Over |
|---|---|---|---|---|
| Token usage (input) | [from cost analysis] | [measure] | | [scale down model / reduce context / add caching] |
| Token usage (output) | [from cost analysis] | [measure] | | [shorten outputs / adjust prompts] |
| API calls / month | [from cost analysis] | [measure] | | [add batching / reduce retry loops] |
| Total monthly cost | [from cost analysis] | [measure] | | [trigger architecture simplification] |

## Go/No-Go Decision Framework
- **GREEN (continue):** All performance metrics ≥ minimum AND cost within 120% of projection
- **YELLOW (optimize):** Performance meets minimum BUT cost 120-200% of projection → optimize prompts, reduce context, consider model tier downgrade
- **RED (pivot):** Performance below minimum OR cost > 200% → reassess architecture choice, consider simpler pattern from evolution path

## Architecture Evolution Triggers
- If single agent resolution rate plateaus < [target] → evaluate adding Skills
- If single agent + Skills still insufficient → trigger multi-agent evaluation
- If cost exceeds budget by > 50% for 2+ consecutive months → simplify architecture
- If latency exceeds SLA for > 5% of requests → evaluate parallel processing or model tier change

## Optimization Playbook
1. **First:** Tune prompts (lowest cost intervention)
2. **Second:** Adjust context management (cap token usage, paginate tool responses)
3. **Third:** Switch model tiers for non-critical components (e.g., Haiku for classification, Sonnet for generation)
4. **Fourth:** Restructure architecture (only if 1-3 insufficient)
```
