# Interview State Schema, Transcript & Checkpoint Format

This file defines the data structures the Phase 1 interview loop maintains internally. Load this when initializing Step 1.1 or restoring from a checkpoint.

## Table of Contents
1. [Interview State Object](#interview-state-object)
2. [Transcript Entry Format](#transcript-entry-format)
3. [Checkpoint Format](#checkpoint-format)
4. [Recovery Procedure](#recovery-procedure)

---

## Interview State Object

Initialize this structure in working memory at the start of Phase 1:

```
state = {
  id: "[topic-slug]-[YYYYMMDD-HHMM]",  # e.g. "customer-support-agent-20260406-1430"
  type: "greenfield",        # or "brownfield" if user mentions existing system
  round: 0,
  clarity: {
    goal: 0.0,                # What is the agent supposed to do?
    constraints: 0.0,         # Budget, timeline, compliance, latency, team
    domain: 0.0,              # How many knowledge domains? How do they interact?
    success_criteria: 0.0     # How do we know this works? What metrics?
  },
  weights: {
    goal: 0.35,
    constraints: 0.25,
    domain: 0.25,
    success_criteria: 0.15
  },
  ambiguity: 1.0,             # 1 - weighted sum of clarity scores
  entities: [],               # Key concepts extracted each round
  entity_history: [],         # Per-round entity snapshots for stability tracking
  audience: "unknown",        # technical | business | mixed | unknown
  challenge_modes_used: [],   # Track which challenge modes have fired
  transcript: []              # See Transcript Entry Format below
}
```

**Ambiguity formula:**
```
ambiguity = 1 - (goal × 0.35 + constraints × 0.25 + domain × 0.25 + success_criteria × 0.15)
```

**Entity stability:**
```
stability_ratio = entities_stable_for_2+_rounds / total_entities
```
When `stability_ratio ≥ 0.8`, the domain model has converged.

---

## Transcript Entry Format

Append one entry per round:

```
{
  round: N,
  dimension_targeted: "goal",            # which dimension this round addressed
  question: "...",                       # the AskUserQuestion question text
  answer: "...",                         # the user's selected option / Other text
  scores: { goal: 0.8, constraints: 0.4, domain: 0.6, success_criteria: 0.2 },
  ambiguity: 0.45,
  entities_this_round: ["customer", "refund", "Shopify"],
  challenge_mode: null                   # or "contrarian" | "simplifier" | "ontologist"
}
```

---

## Checkpoint Format

Every 3 rounds (round 3, 6, 9...), output this structured summary directly in the conversation. It serves as a recovery point if the conversation is interrupted or context is compacted.

```
--- CHECKPOINT (Round [N]) ---
State: {type: "[greenfield/brownfield]", audience: "[technical/business/mixed]"}
Scores: Goal [X] | Constraints [X] | Domain [X] | Success [X] | Ambiguity [X]%
Entities: [comma-separated stable entities]
Key findings:
- Goal: [1-sentence summary of what we know]
- Constraints: [1-sentence summary]
- Domain: [1-sentence summary]
- Success: [1-sentence summary]
Challenge modes used: [list or "none yet"]
--- END CHECKPOINT ---
```

---

## Recovery Procedure

If you detect that a previous checkpoint exists in the conversation (e.g., after a session continuation or context compaction):

1. Parse the most recent `--- CHECKPOINT ---` block
2. Reconstruct the state object from the parsed values
3. Resume from `round + 1`
4. Acknowledge to the user: "I found a checkpoint from Round [N]. Resuming the interview from there."

If both a checkpoint AND a saved `interview-spec-[topic-slug].md` file exist, the spec file is the authoritative ground truth — use it to populate the crystallized fields and use the checkpoint only for in-progress interview metadata.
