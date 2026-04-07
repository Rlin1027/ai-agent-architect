# Fallback Module Guidance

When external skills (e.g., `product-management:feature-spec`, `xlsx`) are not installed, use these self-contained guidelines to ensure consistent quality in Phase 4 module generation.

**Priority:** Always attempt to load the external skill first (`Read the relevant skill if available`). Only fall back to this file if the skill is unavailable.

---

## PRD Fallback (replaces product-management:feature-spec)

### Structure
A well-structured PRD for an AI agent project includes:

1. **Problem Statement** — 2-3 sentences describing the user problem, who experiences it, frequency, and cost of not solving it. Ground in interview evidence.

2. **Goals** — 3-5 specific, measurable outcomes. Distinguish user goals (what users get) from business goals (what the company gets). Goals are outcomes, not outputs ("reduce resolution time by 50%" not "build an agent").

3. **Non-Goals** — 3-5 things explicitly out of scope. For each, briefly explain why. Prevents scope creep.

4. **User Stories** — Format: "As a [specific user type], I want [capability] so that [benefit]". Include edge cases, error states, and different user types. Order by priority.

5. **Requirements** — Categorized as:
   - **P0 (Must-have):** Cannot ship without. Ask: "Would we not ship without this?"
   - **P1 (Nice-to-have):** Improves experience but core works without it
   - **P2 (Future):** Out of scope but guides architectural decisions
   Each requirement needs: clear description, acceptance criteria, technical considerations, dependencies.

6. **Success Metrics** — Two types:
   - Leading indicators (days-weeks): adoption rate, task completion rate, time to complete, error rate
   - Lagging indicators (weeks-months): retention impact, revenue impact, support ticket reduction
   Set specific targets: "50% adoption within 30 days" not "high adoption". Define measurement method and evaluation timeline.

7. **Acceptance Criteria** — Use Given/When/Then format:
   - Given [precondition]
   - When [user action]
   - Then [expected outcome]
   Cover happy path, error cases, and edge cases.

8. **Open Questions** — Tag each with who should answer. Distinguish blocking vs. non-blocking.

9. **Timeline** — Hard deadlines, dependencies, suggested phasing.

### Agent-Specific PRD Additions
When writing a PRD for an AI agent (as opposed to a traditional feature), also include:
- **Agent Autonomy Level:** What decisions can the agent make alone vs. requiring human approval?
- **Escalation Criteria:** When should the agent hand off to a human?
- **Failure Modes:** What happens when the agent makes a wrong decision? What's the blast radius?
- **Training/Evaluation Data:** What data is needed to validate the agent works correctly?

---

## Cost Analysis Spreadsheet Fallback (replaces xlsx skill)

### Structure
The cost analysis spreadsheet should contain these sheets:

**Sheet 1: Token Estimates**
| Component | Role | Model Tier | Avg Input Tokens | Avg Output Tokens | Calls/Day | Daily Tokens |
|---|---|---|---|---|---|---|
| [Agent name] | [Role] | [Haiku/Sonnet/Opus] | [estimate] | [estimate] | [from interview] | [calculated] |

**Sheet 2: Monthly Cost Projection**
| Component | Model | Token Rate (input) | Token Rate (output) | Monthly Volume | Monthly Cost |
|---|---|---|---|---|---|
| [Agent] | [Model] | [$/1M tokens] | [$/1M tokens] | [daily × 30] | [calculated] |
| **Total** | | | | | **[sum]** |

Use current Anthropic pricing (search if unsure):
- Claude Haiku: lowest cost, best for classification/routing
- Claude Sonnet: mid-tier, best for most agent tasks
- Claude Opus: highest cost, best for complex reasoning

**Sheet 3: Break-Even Analysis**
| Metric | Current Process | With Agent | Savings |
|---|---|---|---|
| Monthly operational cost | [from interview] | [agent cost + reduced human cost] | [difference] |
| Time per task | [from interview] | [estimated with agent] | [difference] |
| FTE equivalent | [from interview] | [reduced FTE] | [difference] |
| Break-even point | — | [months until savings > investment] | — |

**Sheet 4: Sensitivity Analysis**
Show what happens to monthly cost if:
- Volume doubles (2x calls/day)
- Volume triples (3x calls/day)
- Model tier changes (Haiku → Sonnet, Sonnet → Opus)
- Average token count increases 50%

### Formatting Guidelines
- Use Excel formulas, not hardcoded calculations (so the spreadsheet stays dynamic)
- Blue text for user-editable inputs (model prices, volume assumptions)
- Black text for formulas
- Include a summary row/section at the top of each sheet
- Format currency as $#,##0; percentages as 0.0%

---

## General Fallback Behavior

When any external skill is unavailable:
1. Log internally: "Note: [skill-name] not available, using built-in fallback guidance"
2. Do NOT tell the user the output quality may be lower — just produce the best output possible
3. Follow the guidance in this file as closely as possible
4. If the module requires capabilities this file doesn't cover (e.g., actual .xlsx file generation without openpyxl knowledge), generate the content as a well-structured markdown table instead and note that the user can convert it to .xlsx
