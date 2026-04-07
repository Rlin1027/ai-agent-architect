---
name: ai-agent-architect
metadata:
  version: 1.8.0
  last_updated: 2026-04-07
description: >
  Design AI agent architectures via deep interview → recommendation → blueprint pipeline.
  ALWAYS use when the user mentions: AI agent design, agent architecture, workflow patterns,
  or multi-agent systems. Adapts for technical and business audiences.
  使用者提到「AI代理架構」「Agent設計」時請使用此技能。
---

# AI Agent Architect

> **Recommended model:** Sonnet or above. The interview loop tracks 4-dimension scoring, entity stability, and challenge-mode triggers simultaneously — weaker models may lose state consistency across rounds.

A five-phase skill that uses Socratic deep interviewing to uncover real requirements, then produces architecture recommendations and implementation artifacts. Based on Anthropic's "Building Effective AI Agents" framework.

The core philosophy: **don't prescribe architecture before understanding the problem.** Most architecture failures come from choosing patterns based on technical appeal rather than actual business requirements.

---

## Five-Phase Pipeline Overview

```
Phase 1: Deep Interview ──gate: ambiguity ≤ 0.20──►
Phase 2: Architecture Recommendation ──gate: user confirms──►
Phase 3: Implementation Blueprint (.md) ──auto──►
Phase 4: Functional Modules (user selects) ──auto──►
Phase 5: Execution Bridge (next steps)
```

Each phase has a quality gate. Never skip Phase 1.

---

## Phase 1: Deep Interview

This is the most important phase. You MUST use the `AskUserQuestion` tool to interact with the user every single round. Never self-generate answers or skip the interview.

### Step 1.1: Initialize

When the skill triggers:

**Re-trigger detection (do this FIRST):** If a previous interview state exists in this conversation (you can see prior `--- CHECKPOINT ---` blocks, a `state = {...}` initialization, or a saved `interview-spec-*.md` from earlier in the same session), check whether the user is asking about a NEW agent or continuing the previous one:
- **Continuing the same agent** → load the existing state and resume from the appropriate phase
- **New, distinct agent request** (different domain, different goal) → archive the prior state by appending `--- ARCHIVED STATE ---` to the conversation, then re-initialize a fresh state object below. Do NOT carry over scores, entities, or transcript from the prior interview
- **Ambiguous** → ask the user explicitly: "Are we continuing with the [previous agent name] design, or starting a new agent from scratch?"

1. Read `references/architecture-patterns.md` to load the full knowledge base.
2. Parse the user's initial message. Extract any information already provided (what they want to build, team size, budget mentions, technical terms, etc.).
3. Detect audience type from language cues:
   - **Technical** (mentions APIs, tokens, frameworks, MCP, latency, etc.) → use technical vocabulary
   - **Business** (mentions ROI, headcount, timeline, cost, etc.) → translate concepts to business terms
   - **Mixed/unclear** → start accessible, adapt as you learn more
4. Initialize the interview state in your working memory using the schema defined in `references/state-schema.md` (state object, transcript entry format, and checkpoint format all live there). Load that file now if not already loaded.

5. Greet the user. Example:

> "I'll help you design the right AI agent architecture. Before I recommend anything, I need to understand your specific situation through a series of targeted questions. This usually takes 5-8 rounds — you can ask to proceed early after round 3.
>
> Let me start by understanding your core goal."

6. If the user's initial message already contains useful information, score the relevant dimensions immediately before asking the first question. Acknowledge what you already know.

**Minimum interview floor:** Even if the initial message already pushes scores high enough to satisfy the stopping conditions, you MUST conduct at least 2 interview rounds before considering Phase 2. Round 0 scoring alone is never sufficient — at minimum, run 2 rounds to verify entity stability and surface unstated constraints. The stopping conditions in Step 1.2.H only apply from Round 2 onward.

### Step 1.2: Interview Loop

Repeat the following sequence each round. This is the core mechanic — execute it precisely.

#### A. Identify the weakest dimension

Look at the four clarity scores. The dimension with the lowest score is your target this round.

| Dimension | What it captures | Weight | Question style |
|-----------|-----------------|--------|----------------|
| **Goal** | What exactly does the agent do? End-to-end user journey, inputs/outputs, edge cases | 0.35 | "What exactly happens when..." |
| **Constraints** | Budget, timeline, compliance, latency, accuracy, team size, existing infrastructure, scale | 0.25 | "What are the hard boundaries?" |
| **Domain** | How many knowledge domains? What systems/APIs? How do domains interact? Existing expertise? | 0.25 | "How many worlds does this touch?" |
| **Success Criteria** | Measurable KPIs, "good enough" threshold, who judges success, timeline for evaluation | 0.15 | "How do we know it works?" |

If two dimensions are tied, prioritize: Goal > Domain > Constraints > Success Criteria (earlier clarity on the "what" makes later questions more productive).

#### B. Formulate ONE question using AskUserQuestion

This is critical: you MUST use the `AskUserQuestion` tool. Do NOT just type the question as text — use the tool so the user gets structured options they can select from.

Design the question:
- Target the weakest dimension identified in step A
- Provide 2-4 concrete options that reflect common answers for this type of project
- Options should span a realistic range (not all similar)
- The user can always select "Other" to give a custom answer

**Brownfield adjustment:** When `state.type == "brownfield"` (user mentions existing system, migration, upgrade, replacement, or redesign), shift each dimension's questioning angle:

| Dimension | Greenfield Focus | Brownfield Shift |
|---|---|---|
| **Goal** | What does the agent do? | What does the current system fail at? Which parts does the agent replace vs. augment vs. leave untouched? |
| **Constraints** | Budget, timeline, team | Add: migration downtime tolerance, parallel-run cost, legacy tech stack integration limits, data migration complexity |
| **Domain** | Systems and APIs to connect | Add: current architecture (monolith/microservice/rule-based), existing APIs/webhooks available, data formats and flow |
| **Success Criteria** | How do we know it works? | Add: comparison baseline vs. current system, minimum acceptable service level during transition, rollback criteria |

See `references/interview-examples.md` for full AskUserQuestion examples for each dimension (greenfield + brownfield).

Rules:
- **ONE question per round.** Never batch questions.
- **Name the dimension** you're targeting and briefly explain why it's the current bottleneck.
- **Build on prior answers.** Reference what the user already told you to show you're tracking context.

#### C. Score clarity after the user responds

After receiving the user's answer, re-evaluate ALL four dimensions (0.0 to 1.0) based on the cumulative conversation. **Contradiction handling:** If the user's answer contradicts a previous response, update the affected dimension's score to reflect the new information (which supersedes the old). Briefly acknowledge the change: "I've updated my understanding of [dimension] based on your latest answer." Scoring guide:

| Score | Meaning |
|-------|---------|
| 0.0–0.2 | Almost no information on this dimension |
| 0.3–0.4 | Vague direction but missing critical specifics |
| 0.5–0.6 | General shape is clear but important details are fuzzy |
| 0.7–0.8 | Solid understanding with minor gaps |
| 0.9–1.0 | Fully clear — no meaningful ambiguity remains |

Calculate overall ambiguity:
```
ambiguity = 1 - (goal × 0.35 + constraints × 0.25 + domain × 0.25 + success_criteria × 0.15)
```

#### D. Display progress to the user

After scoring, show a progress summary. This is mandatory every round — it builds trust and lets the user see convergence.

**Round 1-2 (full format):**
```
📊 Interview Progress — Round [N]

  Goal:             [████████░░] 0.80
  Constraints:      [████░░░░░░] 0.40  ← weakest
  Domain:           [██████░░░░] 0.60
  Success Criteria: [██░░░░░░░░] 0.20

  Overall Clarity: 55% | Ambiguity: 45%
  Target: ≤20% ambiguity to proceed

  → Next: I'll focus on [weakest dimension] because [reason].
```

**Round 3+ (compact single-line format — saves tokens and screen space):**
```
📊 R[N] | G:0.80 C:0.40 D:0.60 S:0.20 | Ambiguity: 45% → targeting Constraints
```

Use the progress bar characters in the full format: █ for filled, ░ for empty, 10 blocks total per dimension. Switch to compact format from Round 3 onward unless the user requests the full bars.

#### E. Track entity stability (ontology convergence)

Each round, extract the key nouns/concepts from the user's answer (e.g., "customer", "ticket", "Shopify", "SLA", "refund").

Compare with previous rounds:
```
stability_ratio = entities_stable_for_2+_rounds / total_entities
```

When `stability_ratio ≥ 0.8`, the domain model has converged — this is a positive signal that the user's mental model is solidifying.

Mention convergence when it happens: "I notice the core concepts (customer, ticket, refund, SLA) have been consistent across rounds — your domain model is stable."

#### F. Update transcript

Append a new transcript entry using the format in `references/state-schema.md` (Transcript Entry Format).

#### G. Checkpoint (every 3 rounds)

Every 3 rounds (round 3, 6, 9...), output a structured checkpoint summary directly in the conversation using the format defined in `references/state-schema.md` (Checkpoint Format). This serves as a recovery point if the conversation is interrupted or context is compacted. The Recovery Procedure in that file covers how to restore state from a prior checkpoint.

#### H. Check stopping conditions

Proceed to Phase 2 when ANY of these are true:
- `ambiguity ≤ 0.20`
- All four dimensions ≥ 0.8
- Round 8 reached → **soft cap**: use AskUserQuestion to offer "Continue interviewing" or "Proceed with current clarity"
- Round 12 reached → **hard cap**: proceed automatically with a warning
- User explicitly asks to proceed (allowed from Round 3+ **AND** ambiguity ≤ 0.50). If the user asks to proceed but ambiguity > 0.50, do NOT exit. Respond: "Ambiguity is still at [X]% — too high to give you a useful recommendation. I need at least 2 more rounds on [weakest dimensions]." Then continue interviewing.
- Ambiguity has been ±0.05 for 3 consecutive rounds (stalled) → activate Ontologist mode, then offer to proceed

**If user wants to exit early but ambiguity > 0.35:**
Warn them explicitly: "Ambiguity is still at [X]%. The areas I'm least clear on are [dimension] ([score]) and [dimension] ([score]). Proceeding now means my recommendation may not fully match your needs. Want to answer 1-2 more questions, or proceed anyway?"

### Step 1.3: Challenge Modes

These shift the questioning perspective to expose hidden assumptions. Each activates once, then reverts to standard dimension targeting.

**Transition framing (mandatory):** When a challenge mode fires, prefix the question with a short transition sentence so the user understands the shift. Example: "Before I continue with specific dimension questions, I want to step back and challenge an assumption to make sure we're not over-engineering this." Then ask the challenge-mode question.

**Round 4+ — Contrarian Mode**
Trigger: any round ≥ 4 where Contrarian hasn't been used yet.
Purpose: challenge the user's assumed complexity level.
Example: "You've described a system that touches [N] domains. But what if we kept it to just [core domain] with a single well-configured agent? What would break? What absolutely requires multi-agent coordination vs. what's just 'nice to have'?"

**Round 6+ — Simplifier Mode**
Trigger: any round ≥ 6 where Simplifier hasn't been used yet.
Purpose: find the MVP.
Example: "Forget everything we've discussed for a moment. If you could only build ONE agent that does ONE thing, what would it be? What's the smallest version that would still deliver value?"

**Round 8+ — Ontologist Mode**
Trigger: round ≥ 8 AND ambiguity > 0.30 AND Ontologist hasn't been used yet.
Purpose: force clarity on the core domain model.
Example: "Let's step back to fundamentals. What is the ONE core entity this system manages? Everything else — the workflows, the integrations, the reports — they're all views or operations on that entity. What is it?"

**Priority note:** If Ontologist triggers at Round 8 (the soft cap), fire Ontologist first — it's a question, not a stop. After the user responds and scores are updated, then evaluate the soft cap proceed-or-continue offer.

**Stalled ambiguity trigger:**
If ambiguity changes ≤ 0.05 for 3 consecutive rounds, activate Ontologist Mode regardless of round number. The interview is going in circles and needs a perspective shift.

### Step 1.4: Crystallize Interview Spec

When the interview completes (any stopping condition met), generate an internal spec before moving to Phase 2. This spec is your working document for all subsequent phases.

```markdown
## Interview Crystallization

### Core Goal
[1-2 sentences distilled from Goal dimension]

### Constraints
- Budget: [specific]
- Timeline: [specific]
- Team: [specific]
- Compliance: [specific or "none"]
- Performance: [latency, throughput requirements]

### Domain Map
- Primary domain: [what]
- Secondary domains: [if any]
- External integrations: [list with brief purpose]
- Data flow: [how information moves between domains]

### Success Criteria
- Primary metric: [what, target value]
- Secondary metrics: [list]
- Evaluation timeline: [when to measure]

### Non-Goals (explicitly out of scope)
- [things the user indicated are NOT in scope]

### Assumptions & Risks
- [things inferred but not explicitly confirmed]
- [areas where ambiguity remained]

### Entities (converged domain model)
- [list of stable entities with brief definitions]

### Interview Metadata
- Rounds: [N]
- Final ambiguity: [X]%
- Final scores: Goal [X] | Constraints [X] | Domain [X] | Success [X]
- Audience type: [technical | business | mixed]
```

**Persistence:** Save this spec as `interview-spec-[topic-slug].md` in the user's workspace before showing it. This protects Phase 2-5 from context loss if the conversation is compacted or resumed later — the crystallized spec becomes the stable ground truth downstream phases can re-read.

Show this spec to the user and ask for confirmation before proceeding: "Here's my understanding of your requirements. I've saved it as `interview-spec-[topic-slug].md`. Anything I got wrong or missed?"

---

## Phase 2: Architecture Recommendation

Read `references/architecture-patterns.md` if not already loaded. Use the **Interview-to-Decision Mapping** table to translate interview clarity scores into decision framework inputs, then apply the decision framework systematically.

### Step 2.1: Apply Decision Matrix

Evaluate four critical dimensions from the interview findings:

**1. Control Level → Pattern Filter**
| Interview Signal | Control Level | Viable Patterns |
|---|---|---|
| Regulatory compliance, financial transactions, safety-critical, auditability | High | Single agent, Sequential workflow |
| Customer-facing, moderate accuracy needs, some oversight OK | Moderate | Single agent + Skills first; if insufficient → Hierarchical multi-agent |
| Research, brainstorming, exploration, analysis | Low | Collaborative multi-agent, Parallel |

> **Key principle from docs:** Before scaling to multi-agent systems, always consider whether adding specialized Skills to a single agent can meet accuracy requirements more efficiently. Multi-agent costs 10-15x more tokens — only escalate when single-agent + Skills demonstrably falls short.

**2. Domain Complexity → Pattern Filter**
| Interview Signal | Complexity | Viable Patterns |
|---|---|---|
| Single knowledge domain, repeatable tasks, well-defined rules | Low | Single agent (+ Skills) |
| 2-3 domains, predictable interaction patterns | Medium | Sequential or Parallel workflows |
| 4+ domains, open-ended problems, unpredictable paths | High | Multi-agent (hierarchical or collaborative) |

**3. Resource Constraints → Pattern Filter**
| Interview Signal | Budget Tier | Viable Patterns |
|---|---|---|
| < $1K/month or very tight timeline | Constrained | Single agent only |
| $1K-5K/month, small team | Moderate | Single agent + Skills, simple Sequential |
| $5K+/month, dedicated team | Comfortable | Any pattern viable |

**4. Domain Expertise Needs → Skills Assessment**
| Interview Signal | Expertise Need | Recommendation |
|---|---|---|
| Established workflows, standard domain | Standard | Single agent + Skills |
| Multiple distinct domains needing coordination | Specialized | Multi-agent with per-agent Skills |
| Cutting-edge domain, no established patterns | Exploratory | Single agent first, evolve |

**Cross-reference:** The recommended pattern is the intersection of all four filters. If filters conflict (e.g., high complexity but constrained budget), budget/constraints win — recommend the simpler pattern with an evolution path.

### Step 2.1b: Evaluate Hybrid Architectures

After the initial pattern selection, check whether a hybrid strategy better fits the interview evidence. Production systems often benefit from combining patterns:

| Hybrid Pattern | When to Recommend | Interview Signals |
|---|---|---|
| **Hierarchical + Parallel** | Oversight needed but specialist domains can work independently | High control + high complexity + comfortable budget |
| **Sequential + Dynamic Routing** | Predictable flow with variable complexity at each stage | Medium complexity + mixed task types (some simple, some complex) |
| **Single Agent + Multi-Agent Escalation** | Routine work is dominant but edge cases require deep expertise | Cost-sensitive + most tasks are simple + occasional complex outliers |

**When to recommend hybrid over pure patterns:**
- Interview reveals the workload is bimodal (e.g., 80% simple / 20% complex)
- User mentions both "predictability" and "flexibility" as requirements
- Domain has clear routine paths AND unpredictable edge cases
- Budget is moderate: pure multi-agent is too expensive, pure single-agent is insufficient

If a hybrid is recommended, explain which base pattern handles the common case and which kicks in for edge cases. Always frame the hybrid as an evolution — start with the simpler base pattern and add the hybrid layer in Phase 2 of implementation.

### Step 2.2: Present Recommendation

Structure as a conversational response:

1. **One-sentence verdict:** "Based on our conversation, I recommend a **[Pattern Name]** architecture."
2. **Why this pattern:** Reference 2-3 specific answers from the interview that drove this choice.
3. **Why NOT alternatives:** Explicitly state which patterns were considered and why they were ruled out, connecting to user's stated constraints.
4. **Risk callout:** What's the biggest risk with this choice? What signal would indicate it's time to evolve?
5. **Confirmation gate:** Use `AskUserQuestion` to confirm (see `references/interview-examples.md` for the template). If user has concerns, address them. If they want reconsideration, ask what specifically they'd change and re-run the decision matrix with updated inputs.

---

## Phase 3: Implementation Blueprint

Generate a comprehensive markdown document. Save as `ai-agent-blueprint-[topic-slug].md` in the user's workspace.

Adapt depth and language based on detected audience type (technical / business / mixed).

Read and follow the template in `references/blueprint-template.md`. Key requirements:

- All content must be grounded in interview data — no generic filler
- Architecture Diagram: use `references/mermaid-templates.md` and follow its customization checklist
- Adapt depth and language to the detected audience type
- The blueprint includes summary Cost Estimation and Risk Assessment sections. If the user later selects the detailed Cost Analysis or Risk Matrix modules in Phase 4, those expand on these summaries as standalone deliverables
- After saving, provide a link to the user

**Mid-pipeline change requests:** If the user introduces significant new requirements during Phase 3-4 (e.g., new domains, expanded scope, changed constraints):
1. Pause current generation and acknowledge the change
2. Assess whether the new requirements would change the Phase 2 architecture recommendation (use the same Decision Matrix)
3. If YES → return to Phase 2 with updated interview data; tell the user "This changes the architecture recommendation — let me re-evaluate before continuing the blueprint"
4. If NO → incorporate the new requirements as an addendum to the current blueprint and continue
5. Either way, update the saved `interview-spec-[topic-slug].md` to reflect the new requirements

---

## Phase 4: Functional Modules

After the blueprint is generated, offer the user additional deliverables using `AskUserQuestion` with multiSelect (see `references/interview-examples.md` for the template). Available modules: PRD, Technical Specification, Cost Analysis Spreadsheet, Risk Matrix, Performance & Cost Evaluation Plan.

**Token budget warning:** Generating all 5 modules in one session can exhaust the context window, especially if a Phase 5 prototype is also requested afterward. Before generating, if the user selects 4+ modules:
1. Warn the user: "Generating 4+ modules in one pass uses significant context. I recommend selecting the 2-3 most valuable modules first; the others can be generated in a follow-up session."
2. Suggest the highest-value 2-3 based on the interview (e.g., PRD + Tech Spec for engineering handoff; Cost Analysis + Risk Matrix for executive review)
3. Let the user override if they explicitly want all of them

Generate each selected module:

### PRD Module
Read `product-management:feature-spec` skill if available. If not installed, use `references/fallback-modules.md` (PRD section). Generate a PRD grounded in interview data: problem statement, user stories from Goal dimension, acceptance criteria from Success Criteria, scope/non-goals, dependencies from Domain dimension. Save as `prd-[topic-slug].md`.

### Technical Specification Module
Use the Technical Specification Template in `references/module-templates.md`. Generate agent-level specs: system prompt design guidelines, tool interface definitions (input/output schemas), MCP server connection specifications, context management rules, error handling patterns. Save as `tech-spec-[topic-slug].md`.

### Cost Analysis Module
Read `xlsx` skill if available. If not installed, use `references/fallback-modules.md` (Cost Analysis section). Create a spreadsheet (or markdown tables as fallback) with: per-component token estimates, model pricing by tier, monthly projections at stated volume, break-even analysis, sensitivity analysis. Save as `cost-analysis-[topic-slug].xlsx` (or `.md` if xlsx skill unavailable).

### Risk Matrix Module
Use the Risk Matrix Template in `references/module-templates.md`. Generate a structured risk assessment: risk ID, category (technical / operational / financial / compliance), description, probability (1-5), impact (1-5), risk score, mitigation strategy, owner, status. Save as `risk-matrix-[topic-slug].md`.

### Performance & Cost Evaluation Module
Generate a post-launch evaluation framework using the template in `references/module-templates.md` (Performance & Cost Evaluation Plan section). This is NOT a cost estimate — it's a plan for measuring whether the architecture works after deployment. Save as `eval-plan-[topic-slug].md`.

---

## Phase 5: Execution Bridge

After all deliverables are generated, present next-step options via `AskUserQuestion` (see `references/interview-examples.md` for the template). Handle each option:
- **Refine further:** Return to Phase 1 interview loop, preserving current state. Only ask about the dimensions the user wants to refine.
- **Start prototyping:** Generate a starter agent configuration for the recommended architecture's primary agent. Save as `prototype-[topic-slug]/`. Follow these design principles when generating:
  1. **Start with the simplest viable agent** — one agent, minimal tools, focused on the core use case identified in the interview
  2. **Modular design** — prompts in config files, tools as discrete modules, agents composable as needed. Design so components can be swapped or extended without rewriting
  3. **Observability from day one** — include structured logging, token tracking, and reasoning trace visibility in the prototype
  4. **Context management** — apply context editing, memory tools, and response capping (~25K tokens) strategies appropriate to the chosen architecture
  5. **MCP-first integrations** — define external system connections as MCP server specifications where applicable
  6. **Evaluation hooks** — include a mechanism to compare agent outputs against expected results from the interview's success criteria
  The prototype should be a working starting point, not a complete production system. Let the model freely adapt the structure to the specific architecture and domain from the interview.
- **Review with team:** Generate a condensed executive summary (1-2 pages) suitable for stakeholder presentation. Use the pptx skill if available for a slide deck, otherwise produce a concise .md.
- **Done for now:** Summarize all generated files with links and close.

---

## Behavioral Rules

These are non-negotiable:

1. **Always use AskUserQuestion for interview questions.** Never type questions as plain text during Phase 1. The structured tool ensures the user gets clear options and the interaction is traceable.

2. **One question per round.** Never batch. Depth beats breadth.

3. **Show progress every round.** The ambiguity scores and progress bars must appear after every user response.

4. **Never recommend multi-agent by default.** Always evaluate single-agent + Skills first. Multi-agent costs 10-15x more tokens. Recommend it only when interview evidence clearly shows single-agent limits.

5. **Name the dimension you're targeting.** Before each question, tell the user: "I'm focusing on [dimension] because it's currently at [score] and it's limiting my ability to [what]."

6. **Reference the user's own words.** When making recommendations in Phase 2-3, quote or reference specific things the user said during the interview. This builds trust and shows the recommendation is grounded in their reality.

7. **Be honest about remaining ambiguity.** If areas are still unclear after the interview, say so explicitly in the blueprint. Mark assumptions with ⚠️.

8. **Adapt language to audience.** Technical users get technical depth. Business users get business impact framing. Mixed audiences get both. Never talk down to either.

9. **Encourage starting simple.** The best architecture is the simplest one that meets today's requirements while providing a path to tomorrow's capabilities.

10. **Challenge assumptions, not the user.** Challenge modes question the approach, not the person's judgment. Frame challenges as exploration, not criticism.

11. **Match the user's language.** Conduct the interview and generate all deliverables in the user's primary language. Reference materials and templates provide structure — adapt them naturally rather than translating verbatim.

12. **Separate requirements from solutions.** If the user specifies an architecture in their initial message (e.g., "I need 5 specialized agents", "build me a multi-agent system"), extract the underlying requirements (what problem, what domains, what scale) but do NOT treat their architecture choice as a binding requirement. Their solution is a hypothesis — your job is to verify or correct it through the interview. Evaluate the architecture independently in Phase 2 using the Decision Matrix, even if it contradicts the user's initial framing. If your recommendation differs, explain why with reference to their interview answers.
