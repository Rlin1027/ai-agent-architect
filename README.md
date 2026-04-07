# AI Agent Architect

> A Claude cowork skill that designs AI agent architectures through Socratic deep interviewing — before recommending anything.

![Version](https://img.shields.io/badge/version-1.8.0-blue)
![Claude Code](https://img.shields.io/badge/Claude-cowork-orange)
![Model](https://img.shields.io/badge/requires-Sonnet%2B-blueviolet)
![License](https://img.shields.io/badge/license-MIT-green)
![Based On](https://img.shields.io/badge/based_on-Anthropic_Building_Effective_Agents-red)

---

## Overview

Most AI agent failures come from choosing architecture patterns based on technical appeal rather than actual business requirements. This skill fixes that.

`ai-agent-architect` runs a structured five-phase pipeline: it interviews you across four clarity dimensions, scores ambiguity in real time, and only produces an architecture recommendation once it truly understands your problem. The output is a set of implementation-ready artifacts grounded in your specific context — not generic templates.

---

## Installation

This is a **Claude cowork skill**. To install:

1. Clone this repository into your Claude cowork skills directory
2. The skill auto-triggers when you describe an AI agent design problem

**Trigger phrases:** `AI agent design` · `agent architecture` · `workflow patterns` · `multi-agent systems`

---

## Five-Phase Pipeline

```
Phase 1: Deep Interview ──gate: ambiguity ≤ 20%──►
Phase 2: Architecture Recommendation ──gate: user confirms──►
Phase 3: Implementation Blueprint (.md) ──auto──►
Phase 4: Functional Modules (user selects) ──auto──►
Phase 5: Execution Bridge (next steps)
```

Each phase has a quality gate. Phase 1 is never skipped — even if you describe your requirements in detail upfront, the skill always runs at least 2 interview rounds to verify entity stability and surface unstated constraints.

---

## Phase 1 — Deep Interview

The interview tracks four clarity dimensions per round:

| Dimension | Weight | What It Captures |
|---|---|---|
| **Goal** | 35% | What exactly does the agent do? End-to-end flow, inputs/outputs, edge cases |
| **Constraints** | 25% | Budget, timeline, compliance, latency, team size, infrastructure |
| **Domain** | 25% | How many knowledge domains? What systems/APIs? How do they interact? |
| **Success Criteria** | 15% | Measurable KPIs, acceptable threshold, evaluation timeline |

After each response, the skill displays a live progress indicator:

```
📊 Interview Progress — Round 3

  Goal:             [████████░░] 0.80
  Constraints:      [████░░░░░░] 0.40  ← weakest
  Domain:           [██████░░░░] 0.60
  Success Criteria: [██░░░░░░░░] 0.20

  Overall Clarity: 55% | Ambiguity: 45%
  Target: ≤20% ambiguity to proceed
```

**Stopping conditions** — the interview exits when:
- Overall ambiguity drops to ≤ 20%, or
- All four dimensions score ≥ 0.80, or
- Round 8 is reached (soft cap — user chooses to continue or proceed), or
- Round 12 is reached (hard cap — automatic), or
- You ask to proceed early (allowed from Round 3+ if ambiguity ≤ 50%)

**Challenge modes** activate automatically to break circular answers:
- **Round 4+ — Contrarian:** challenges assumed complexity ("what if a single agent handled everything?")
- **Round 6+ — Simplifier:** finds the MVP ("what is the one thing this must do?")
- **Round 8+ — Ontologist:** forces domain model clarity ("what is the single core entity here?")

**Brownfield support:** when you mention an existing system, migration, or redesign, the interview shifts to brownfield mode — focusing on migration risk, legacy integration points, parallel-run cost, and rollback criteria.

---

## Phase 2 — Architecture Recommendation

The skill applies a four-axis decision matrix using your interview findings:

| Axis | Interview Dimensions | Logic |
|---|---|---|
| Control level | Goal + Constraints | Regulatory/safety needs filter viable patterns |
| Domain complexity | Domain + Goal | Single domain → single agent; multi-domain → workflow or multi-agent |
| Resource constraints | Constraints | Budget and team size narrow affordable options |
| Domain expertise | Domain + Success Criteria | Standard domain → add Skills; multiple domains → specialized agents |

**Key principle:** Single-agent + Skills is always evaluated first. Multi-agent systems cost 10–15x more tokens and are only recommended when the interview evidence clearly shows a single agent cannot meet requirements.

The recommendation states the chosen pattern, explains why each alternative was ruled out (with references to your specific answers), and identifies the biggest implementation risk.

---

## Phase 3 — Implementation Blueprint

Saved as `ai-agent-blueprint-[topic-slug].md`. Contains:

- Executive Summary
- Requirements Summary (from interview data — no generic filler)
- Recommended Architecture with Mermaid diagram
- Component Design (roles, model tier, tools, MCP integrations, context strategy)
- Communication & Coordination patterns
- Observability Plan (logging, token monitoring, trace visibility)
- Phased Implementation Roadmap
- Cost Estimation & Risk Assessment
- Success Metrics tied to your interview success criteria
- Evolution Path
- Full Interview Transcript

---

## Phase 4 — Functional Modules

After the blueprint, select any combination of additional deliverables:

| Module | Output File | Contents |
|---|---|---|
| **PRD** | `prd-[slug].md` | Problem statement, user stories, acceptance criteria, scope/non-goals |
| **Technical Specification** | `tech-spec-[slug].md` | System prompt design, tool interfaces, MCP specs, error handling patterns |
| **Cost Analysis** | `cost-analysis-[slug].xlsx` | Token projections, monthly costs by component, break-even analysis |
| **Risk Matrix** | `risk-matrix-[slug].md` | Probability × impact scoring, mitigation strategies, owners |
| **Evaluation Plan** | `eval-plan-[slug].md` | Post-launch measurement framework, benchmarks, go/no-go criteria |

> Generating 4+ modules in one session is context-intensive. The skill will recommend the 2–3 most valuable modules for your use case and let you generate the rest in a follow-up.

---

## Phase 5 — Execution Bridge

After all deliverables are ready, choose what happens next:

- **Refine further** — return to the interview loop on specific dimensions
- **Start prototyping** — generate a starter agent configuration (prompts, tool configs, observability hooks) saved under `prototype-[slug]/`
- **Review with team** — condensed executive summary or slide deck for stakeholders
- **Done for now** — summary of all generated files with links

---

## Architecture Patterns

The skill selects from these patterns based on your interview findings:

| Pattern | Best For | Relative Token Cost |
|---|---|---|
| **Single Agent** | Well-defined tasks, single domain, constrained budget | Lowest |
| **Routing Workflow** | High input variety, triage, cost optimization by model tier | Low |
| **Sequential Workflow** | Multi-step pipelines, approval chains, compliance processes | Low–Medium |
| **Parallel Workflow** | Independent analyses, speed-critical tasks, voting/guardrails | Medium |
| **Hierarchical Multi-Agent** | Complex multi-domain problems, oversight required | High (10–15×) |
| **Collaborative Multi-Agent** | Research, strategic planning, low-control exploration | Highest |
| **Evaluator-Optimizer** | Quality-sensitive output, iterative refinement cycles | Medium |
| **Hybrid** | Bimodal workloads — routine cases + complex edge cases | Varies |

---

## File Structure

```
ai-agent-architect/
├── README.md
├── SKILL.md                          # Main skill definition (5-phase pipeline)
└── references/
    ├── architecture-patterns.md      # Pattern knowledge base + decision framework
    ├── blueprint-template.md         # Phase 3 output template
    ├── interview-examples.md         # AskUserQuestion templates per dimension
    ├── state-schema.md               # Interview state, transcript, and checkpoint formats
    ├── mermaid-templates.md          # Architecture diagram templates
    ├── module-templates.md           # Phase 4 module output templates
    └── fallback-modules.md           # Fallback generators when optional skills are unavailable
```

---

## Generated Artifacts

A complete run produces some or all of these files in your workspace:

| File | Phase | Description |
|---|---|---|
| `interview-spec-[slug].md` | 1 | Crystallized requirements — ground truth for all downstream phases |
| `ai-agent-blueprint-[slug].md` | 3 | Full implementation blueprint |
| `prd-[slug].md` | 4 | Product Requirements Document |
| `tech-spec-[slug].md` | 4 | Technical Specification |
| `cost-analysis-[slug].xlsx` | 4 | Cost Analysis Spreadsheet |
| `risk-matrix-[slug].md` | 4 | Risk Matrix |
| `eval-plan-[slug].md` | 4 | Performance & Cost Evaluation Plan |
| `prototype-[slug]/` | 5 | Starter agent configuration |

---

## Design Principles

1. **Interview before recommending.** Architecture is never prescribed before requirements are understood.
2. **Default to simplicity.** Single-agent + Skills is always evaluated first. Multi-agent is a last resort.
3. **Ground everything in the interview.** Recommendations quote your specific answers — no generic reasoning.
4. **Name remaining ambiguity.** Gaps in the interview are marked ⚠️ in the blueprint rather than silently assumed away.
5. **Treat your architecture as a hypothesis.** If you name a pattern upfront, the skill evaluates it independently against your requirements and will recommend differently if the evidence points elsewhere.
6. **Adapt to your audience.** Technical users get depth on prompts, tools, and MCP specs. Business users get cost, risk, and ROI framing. All artifacts are generated in your primary language.

---

## Requirements

- Claude cowork
- Claude Sonnet or above (the interview loop simultaneously tracks 4-dimension scoring, entity stability, and challenge-mode triggers — smaller models may lose state consistency across rounds)
- Optional: `xlsx` skill for spreadsheet cost analysis output; `pptx` skill for slide deck generation in Phase 5

---

## License

MIT
