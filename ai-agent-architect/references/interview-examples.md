# Interview Question Examples

This file provides AskUserQuestion examples for each dimension. Use these as templates — adapt the specific wording and options to the user's context.

## Table of Contents
1. [Greenfield Examples](#greenfield-examples) — Goal, Constraints, Domain, Success Criteria
2. [Brownfield Examples](#brownfield-examples) — Goal, Constraints, Domain, Success Criteria
3. [Scoring Calibration Examples](#scoring-calibration-examples) — scored responses per dimension
4. [Phase 2 Confirmation Example](#phase-2-confirmation-example)
5. [Phase 4 Module Selection Example](#phase-4-module-selection-example)
6. [Phase 5 Next Steps Example](#phase-5-next-steps-example)

---

## Greenfield Examples

### Goal Dimension
```
AskUserQuestion:
  question: "When a customer submits a return request, what's the ideal end-to-end experience? This helps me understand the scope of what the agent needs to handle."
  header: "Agent Goal"
  options:
    - label: "Fully automated"
      description: "Agent handles the entire flow autonomously — validates, approves/denies, processes refund, sends confirmation"
    - label: "Triage + assist"
      description: "Agent classifies the request and drafts a response, but a human reviews and approves before action"
    - label: "Routing only"
      description: "Agent categorizes and routes to the right team/queue, humans handle the actual resolution"
    - label: "Advisory"
      description: "Agent provides recommendations and data to human agents who make all decisions"
```

### Constraints Dimension
```
AskUserQuestion:
  question: "What's the realistic monthly budget you can allocate to AI API costs (LLM tokens, embeddings, etc.)? This directly affects which architecture patterns are viable."
  header: "API Budget"
  options:
    - label: "Under $1K/month"
      description: "Tight budget — need to optimize every token. Single-agent patterns preferred."
    - label: "$1K-5K/month"
      description: "Moderate budget — can support some parallelism or specialized sub-agents"
    - label: "$5K-20K/month"
      description: "Comfortable budget — multi-agent architectures become viable"
    - label: "$20K+/month"
      description: "Enterprise budget — full multi-agent orchestration with redundancy"
```

### Domain Dimension
```
AskUserQuestion:
  question: "Which external systems does this agent need to connect to? This determines the integration complexity and influences whether multiple specialized agents are needed."
  header: "Integrations"
  options:
    - label: "1-2 systems"
      description: "Simple integration — e.g., just a database and one API"
    - label: "3-5 systems"
      description: "Moderate — e.g., CRM + billing + inventory + email"
    - label: "6+ systems"
      description: "Complex — multiple APIs, databases, internal tools, and external services"
    - label: "Not sure yet"
      description: "Still mapping out the integration landscape"
  multiSelect: false
```

### Success Criteria Dimension
```
AskUserQuestion:
  question: "If this agent were live next month, what single metric would make you say 'this is working'?"
  header: "Success Metric"
  options:
    - label: "Resolution rate"
      description: "% of requests the agent handles without human intervention"
    - label: "Cost reduction"
      description: "Measurable savings in operational costs or headcount"
    - label: "Speed improvement"
      description: "Faster response/processing time compared to current process"
    - label: "Quality/accuracy"
      description: "Accuracy rate, error rate, or customer satisfaction score"
```

---

## Brownfield Examples

Use these when `state.type == "brownfield"` (user mentions existing system, migration, upgrade, replacement, or redesign).

### Goal Dimension — Brownfield
```
AskUserQuestion:
  question: "Looking at your current system, what's the primary pain point the AI agent needs to solve? This helps me understand which parts to replace vs. keep."
  header: "Migration Goal"
  options:
    - label: "Replace entire flow"
      description: "Current system is end-of-life — agent takes over the full process"
    - label: "Augment existing flow"
      description: "Current system stays, agent handles specific weak points (e.g., triage, classification)"
    - label: "Handle overflow only"
      description: "Current system handles normal load, agent picks up spikes or edge cases"
    - label: "Shadow mode first"
      description: "Agent runs in parallel with current system for validation before any cutover"
```

### Domain Dimension — Brownfield
```
AskUserQuestion:
  question: "What's the current architecture of the system you're planning to replace or augment? This tells me what integration points and data formats the agent will need to work with."
  header: "Current Architecture"
  options:
    - label: "Monolithic application"
      description: "Single codebase, tightly coupled modules — agent will likely need a shim layer to integrate"
    - label: "Microservices with APIs"
      description: "Existing REST/GraphQL APIs the agent can call directly"
    - label: "Rule-based / workflow engine"
      description: "BPM tool, Zapier, or scripted rules — agent replaces or augments specific rule paths"
    - label: "Manual / spreadsheets"
      description: "Largely human-driven with spreadsheets or ticketing — agent digitizes the process"
```

### Success Criteria Dimension — Brownfield
```
AskUserQuestion:
  question: "Compared to your current system, what's the minimum improvement that would make the migration worth it? This sets the baseline for evaluating success."
  header: "Baseline vs. Current"
  options:
    - label: "Match current quality, lower cost"
      description: "Same accuracy/output quality but at a meaningfully lower operational cost"
    - label: "Better quality, same cost"
      description: "Measurably higher accuracy or customer satisfaction at comparable cost"
    - label: "Faster turnaround"
      description: "Significant reduction in handling time or response latency"
    - label: "Handle higher volume"
      description: "Process more requests without proportional cost or headcount increase"
```

### Constraints Dimension — Migration Risk
```
AskUserQuestion:
  question: "During migration, what level of disruption can your operations tolerate?"
  header: "Migration Risk"
  options:
    - label: "Zero downtime"
      description: "Must run old and new systems in parallel — no service interruption allowed"
    - label: "Scheduled maintenance window"
      description: "Can accept brief planned downtime (hours) for cutover"
    - label: "Gradual rollout"
      description: "Route a small % of traffic to the agent, increase over weeks"
    - label: "Full cutover OK"
      description: "Can switch entirely to the new system on a set date"
```

---

---

## Scoring Calibration Examples

Use these scored example responses to calibrate how you assign dimension clarity scores. Same response quality should yield similar scores across runs.

### Goal Dimension — Scored Responses

| User Response | Score | Rationale |
|---|---|---|
| "I want an AI agent for my business" | **0.1** | No specifics — what business, what task, what outcome are all unknown |
| "I want an agent that handles customer questions" | **0.3** | Topic is clear but scope, channels, escalation, and resolution boundaries are vague |
| "Agent triages incoming support tickets, categorizes by issue type, and drafts a reply for human review" | **0.6** | End-to-end flow is clear; missing edge cases, volume, what "categorize" means precisely |
| "Agent triages support tickets from Zendesk, classifies into 8 predefined categories, drafts replies for L1 issues (password resets, order status, return requests), escalates L2/L3 to human queue with context summary" | **0.85** | Concrete inputs, outputs, classification taxonomy, escalation rule — minor gaps only |

### Constraints Dimension — Scored Responses

| User Response | Score | Rationale |
|---|---|---|
| "Budget is flexible" | **0.2** | No actual boundaries given — "flexible" could mean $500 or $50K |
| "We have a small team, maybe 2 devs" | **0.4** | Team is specified but budget, timeline, compliance unclear |
| "Budget around $3K/month, 2 engineers for 6 weeks, no PII handling requirements" | **0.75** | Budget tier, team, timeline, compliance all specified with concrete values |

### Domain Dimension — Scored Responses

| User Response | Score | Rationale |
|---|---|---|
| "It needs to connect to our systems" | **0.2** | "Systems" is undefined — count, type, access patterns all unknown |
| "Connects to Zendesk and our Postgres database" | **0.5** | Two integrations named but unclear if there are more, what data flows between them |
| "Reads tickets from Zendesk API, queries customer records in Postgres, writes classifications back to Zendesk custom fields, pushes escalations to PagerDuty" | **0.85** | All integrations, direction of data flow, and purpose per integration are specified |

### Success Criteria Dimension — Scored Responses

| User Response | Score | Rationale |
|---|---|---|
| "It should work well" | **0.1** | No measurable criterion at all |
| "Reduce response time and improve satisfaction" | **0.3** | Directions given but no target values, no current baseline |
| "80% of L1 tickets resolved without human touch, median response time under 2 minutes, measured weekly for first 90 days" | **0.9** | Specific target, measurement method, evaluation cadence — fully actionable |

**Calibration guideline:** If a response gives a clear direction but no measurable values, cap the score at 0.5. Target values, concrete counts, or named systems are required to cross into 0.6+.

---

## Phase 2 Confirmation Example
```
AskUserQuestion:
  question: "Does this architecture direction feel right? I'll generate the full implementation blueprint based on your choice."
  header: "Confirm"
  options:
    - label: "Yes, proceed"
      description: "Generate the full implementation blueprint with this architecture"
    - label: "I have concerns"
      description: "Let me share some concerns before you proceed"
    - label: "Reconsider"
      description: "I'd like you to re-evaluate — maybe a different pattern"
```

## Phase 4 Module Selection Example
```
AskUserQuestion:
  question: "The architecture blueprint is ready. I can also generate additional documents based on what we discussed. Which would be useful?"
  header: "Additional Outputs"
  multiSelect: true
  options:
    - label: "PRD (Product Requirements)"
      description: "Formal product requirements document with user stories, acceptance criteria, and scope definition — based on our interview"
    - label: "Technical Specification"
      description: "Agent prompt design, tool specifications, MCP connection specs, API contracts, data schemas"
    - label: "Cost Analysis Spreadsheet"
      description: "Detailed .xlsx with token projections, monthly costs by component, break-even analysis vs. current process"
    - label: "Risk Matrix"
      description: "Structured risk assessment with probability × impact scoring and mitigation plans"
    - label: "Performance & Cost Evaluation Plan"
      description: "Evaluation framework with projected metrics, cost benchmarks, and go/no-go criteria for post-launch assessment"
```

## Phase 5 Next Steps Example
```
AskUserQuestion:
  question: "All deliverables are ready. What would you like to do next?"
  header: "Next Steps"
  options:
    - label: "Refine further"
      description: "Go back to the interview to add detail or change direction on specific areas"
    - label: "Start prototyping"
      description: "I'll help you write the initial agent prompt and tool configurations"
    - label: "Review with team"
      description: "I'll create a presentation-ready summary you can share with stakeholders"
    - label: "Done for now"
      description: "Save everything and wrap up — you can come back later"
```
