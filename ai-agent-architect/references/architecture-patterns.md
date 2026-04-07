# AI Agent Architecture Patterns Reference

## Table of Contents
1. [Single-Agent Systems](#single-agent-systems)
2. [Routing Workflows](#routing-workflows)
3. [Multi-Agent Hierarchical Systems](#multi-agent-hierarchical-systems)
4. [Multi-Agent Collaborative Systems](#multi-agent-collaborative-systems)
5. [Sequential Workflows](#sequential-workflows)
6. [Parallel Workflows](#parallel-workflows)
7. [Evaluator-Optimizer Workflows](#evaluator-optimizer-workflows)
8. [Emerging Patterns](#emerging-patterns)
9. [Hybrid Strategies](#hybrid-strategies)
10. [Decision Framework](#decision-framework)

---

## Single-Agent Systems

**How it works:** One AI agent operates in a continuous loop: perceive → decide → act → observe → adjust. The agent formulates a plan, executes actions via tools, evaluates results, and iterates until the task is done or a stopping condition is reached.

**Core components:**
- AI model as reasoning engine
- Prompt defining role and capabilities
- Toolkit of integrations for external systems
- Optional: Agent Skills for specialized domain knowledge

**Best for:**
- Customer service for well-defined product categories
- Document processing with clear business rules
- Code review and basic development tasks
- Routine analysis and reporting
- Open-ended problems where the path forward is unclear

**Avoid when:**
- You need 100% accuracy on the first try
- The problem spans multiple distinct domains
- Tasks require multiple specialized perspectives simultaneously

**Cost profile:** Lowest token usage. Best starting point for proving ROI.

**Real-world example:** A research agent connected via MCP to content repos, business tools, and dev environments. User asks about remote work productivity tools — agent decomposes the query, runs parallel web search + SQL queries, iterates with refined searches, and synthesizes a correlated report.

---

## Routing Workflows

**How it works:** A lightweight classifier (LLM call or rule-based) inspects the input and routes it to one of several specialized handlers. Each handler is optimized for its specific input class. This is the simplest form of conditional logic in agent systems and corresponds to Anthropic's "Routing" workflow pattern.

**Core components:**
- Classifier (LLM with a tight prompt, or programmatic rules)
- N specialized downstream handlers (each can be a single agent, a workflow, or a static response)
- Optional fallback handler for unclassifiable inputs

**Best for:**
- Customer support triage (billing questions → billing agent; technical questions → tech agent)
- Multi-language input routing (route to language-specific pipelines)
- Cost optimization (route simple queries to cheap models, complex to expensive)
- Cases where input variety is the main complexity, not task complexity within each branch

**Avoid when:**
- All inputs require similar processing (no real branching benefit)
- Classification is unreliable (misrouting cost > routing benefit)
- A single agent with conditional logic in its prompt can handle the variety just as well

**Cost profile:** Low — one classifier call per input plus one handler call. Often cheaper than a single large agent because each handler can use a smaller model.

**Why this matters:** Many users describe a "multi-agent" need that is actually a routing problem. Recognizing routing as its own pattern prevents over-engineering into a hierarchical orchestrator.

**Real-world example:** A support inbox where incoming tickets are classified into 6 categories by an LLM, then routed: refund requests → automated refund agent; bug reports → engineering ticketing API; feature requests → product feedback log; etc.

---

## Multi-Agent Hierarchical Systems

**How it works:** A supervisor/orchestrator agent analyzes incoming requests, routes them to specialist agents, and synthesizes their outputs. Subagents are treated as tools. Subagents can also have their own subagents (nested hierarchy).

**Implementation variations:**
- **Full orchestration:** Supervisor maintains complete control over user interactions and task execution
- **Routing-focused:** Supervisor specializes in delegation decisions, may hand off user communication to specialists
- **Hybrid coordination:** Supervisor selectively involved based on task complexity

**Best for:**
- Complex problem-solving requiring diverse expertise
- Marketing campaign development (supervisor + research + creative + copy + media agents)
- Scenarios needing flexibility with oversight
- Multi-domain tasks with clear delegation boundaries

**Key challenge — Context management:**
- Context window overflow as interactions grow
- Solutions: context editing (clearing stale data), memory tools (file-based persistence), pagination/truncation on tool responses (cap at ~25,000 tokens)

**Cost profile:** 10-15x more tokens than single agents. Justified for high-value, complex tasks.

**Real-world example:** Marketing campaign system — Marketing Director agent delegates to Market Research, Creative Design, Copywriting, and Media Planning agents, then synthesizes integrated campaign.

---

## Multi-Agent Collaborative Systems

**How it works:** Multiple specialized agents work as autonomous peers, communicating directly, negotiating roles dynamically, and solving problems through distributed intelligence. Coordination emerges from interactions rather than central authority.

**Implementation variations:**
- **Group chat orchestration:** Agents solve problems via shared conversation thread
- **Event-driven coordination:** Events as shared language for context sharing
- **Blackboard architectures:** Shared knowledge repository as collective memory

**Best for:**
- Research and analysis requiring diverse perspectives
- Competitive intelligence gathering
- Strategic planning where multiple viewpoints improve quality
- Low control requirements where unpredictability is a feature

**Key challenges:**
- Communication complexity increases computational costs
- Emergent behaviors can be unpredictable
- Risk of agents bouncing tasks indefinitely
- Need robust conflict resolution mechanisms

**Cost profile:** Highest complexity and token usage. Use only when distributed intelligence clearly outperforms hierarchical control.

**Real-world example:** Competitive intelligence system — Pricing, Product, Marketing, Financial, Social Media, and Strategic agents cross-reference findings in real-time, validate across sources, and produce corroborated competitor profiles.

---

## Sequential Workflows

**How it works:** Predetermined control flow with defined execution paths. Agents hand off work in a fixed sequence. Can use software-defined decision points or AI-driven routing at each transition.

**Best for:**
- Multi-step approval processes
- Content creation pipelines (draft → review → publish)
- Data transformation and validation chains
- Compliance checking with multiple criteria
- Document processing: outline → validate → write

**Avoid when:**
- A single agent can handle all stages
- Agents need to collaborate rather than hand off
- Workflow requires backtracking or iteration

**Key advantage:** Operational predictability — you can map the entire flow, estimate costs, debug specific stages.

**Real-world example:** Automated data science pipeline — Scoping Agent → Data Engineering Agent → Analysis Agent → Review/Escalation → Deliver Insights.

---

## Parallel Workflows

**How it works:** Independent tasks distributed across multiple agents simultaneously, with results merged or processed concurrently. Fan-out/fan-in pattern. Agents operate independently without handing off results to each other.

**Best for:**
- Multiple perspectives improve quality (code review from different angles)
- Independent analyses can run simultaneously
- Speed matters more than coordination overhead
- Risk assessment requiring diverse viewpoints
- Guardrails (one agent processes, another screens)
- Voting patterns (multiple prompts evaluate same content)

**Avoid when:**
- Agents need to build on each other's work
- Tasks require specific order of operations
- No clear conflict resolution strategy for contradictory results
- Resource constraints (model quotas) make parallel processing inefficient

**Real-world example:** Financial risk assessment — Credit Risk, Market Risk, Operational Risk, and Regulatory Compliance agents all analyze a loan application simultaneously, then results are aggregated by a decision engine.

---

## Evaluator-Optimizer Workflows

**How it works:** Two AI systems in iterative cycles — a Generator creates content, an Evaluator assesses against criteria and provides feedback. Repeats until quality standards are met. Like writer-editor collaboration.

**Best for:**
- Content creation requiring nuance (literary translation)
- Code generation with security requirements
- Professional communications where tone matters
- Research tasks needing multi-step reasoning with validation
- API documentation creation

**Avoid when:**
- First-attempt quality already meets requirements
- Evaluation criteria are subjective or unclear
- Real-time responses needed
- Simple classification tasks
- Strict token budgets

**Typical cycle count:** 2-4 iterations.

**Real-world example:** API documentation creator — Generator analyzes codebase → Technical Evaluator validates accuracy → Refinement cycle until criteria met → Published documentation.

---

## Emerging Patterns

### Dynamic Agent Generation
Agents created at runtime by assembling components from libraries of prompts, tools, and configurations, then dissolved after task completion. Still experimental — no production systems yet, but frameworks like AutoGen and Semantic Kernel provide foundations.

### Network/Peer-to-Peer Systems
Many-to-many agent communication without hierarchy. Early benchmarks show swarm architecture slightly outperforms supervisor architecture because agents collaborate directly without supervisory translation layers.

---

## Hybrid Strategies

Production systems often evolve into hybrids:

- **Hierarchical + Parallel:** Supervisor delegates to specialists who each run parallel workflows within their domain
- **Sequential + Dynamic Routing:** Linear process that routes to different agent types based on intermediate results
- **Single Agent + Multi-Agent Escalation:** Simple agent handles routine tasks, triggers multi-agent system for edge cases (optimizes cost while maintaining capability)

---

## Interview-to-Decision Mapping

This table shows how the four interview dimensions map to the four decision framework questions. Use this when transitioning from Phase 1 (interview) to Phase 2 (recommendation).

| Decision Framework Question | Primary Interview Dimension | Secondary Dimension | What to Extract |
|---|---|---|---|
| 1. Control Level | **Goal** (0.35) | **Constraints** (0.25) | From Goal: autonomy level, human-in-the-loop needs, auditability. From Constraints: compliance requirements, regulatory mentions |
| 2. Domain Complexity | **Domain** (0.25) | **Goal** (0.35) | From Domain: number of systems, API count, domain interactions. From Goal: scope breadth, edge case complexity |
| 3. Resource Constraints | **Constraints** (0.25) | — | Budget tier, team size, timeline pressure, infrastructure limits |
| 4. Domain Expertise | **Domain** (0.25) | **Success Criteria** (0.15) | From Domain: established vs. novel domain, existing workflows. From Success: accuracy thresholds suggesting deep specialization |

**Scoring translation:** When interview clarity scores are high (≥0.7) for a dimension, you have enough signal to confidently place the user on the corresponding decision axis. When scores are moderate (0.4-0.6), note the uncertainty in your recommendation. When low (<0.4), flag that the recommendation carries risk in that dimension.

---

## Decision Framework

### Four Critical Questions

**1. What level of control do you need?**
| Control Level | Recommendation |
|---|---|
| High (regulatory, financial, safety-critical) | Single agents or sequential workflows |
| Moderate (customer support, content, data analysis) | Single agent + Skills first; if insufficient → Hierarchical multi-agent |
| Low (research, brainstorming, analysis) | Collaborative multi-agent |

**2. How complex is your problem domain?**
| Complexity | Recommendation |
|---|---|
| Single domain, repeatable | Single agent |
| Multi-domain but predictable | Sequential or parallel workflows |
| Complex, open-ended | Multi-agent architectures |

**3. What are your resource constraints?**
| Constraint | Recommendation |
|---|---|
| Limited budget/tokens | Single agents (parallel workflows involve multiple model calls — avoid under tight budget) |
| Time-to-market pressure | Single agent first, evolve later |
| Long-term strategic initiative | Modular design for evolution |

**4. Do you need deep domain expertise?**
| Expertise Need | Recommendation |
|---|---|
| Single domain with established workflows | Single agent + specialized Skills |
| Multiple domains requiring coordination | Multi-agent + specialized Skills per agent |

### Evolution Path Example (E-commerce)
Phase 1: Single agent for customer inquiries → Phase 2: Routing pattern → Phase 3: Specialized agents with shared context → Phase 4: Multi-agent with inventory/payment/shipping coordination → Phase 5: Evaluator agents for QA

---

## Terminology Reference

Users and documentation may use different terms for the same concepts. Treat these as synonyms:

| Canonical Term (used in this skill) | Synonyms |
|---|---|
| Hierarchical multi-agent | Supervisory, Orchestrator pattern, Router pattern, Manager-worker |
| Collaborative multi-agent | Swarm, Peer-to-peer, Group chat, Decentralized agents |
| Sequential workflow | Pipeline, Chain, Waterfall, Assembly line |
| Parallel workflow | Fan-out/fan-in, Concurrent, Map-reduce |
| Evaluator-Optimizer | Generator-critic, Writer-editor, Refinement loop |
| Agent Skills | Tool packages, Capability modules, Domain plugins |
| Single agent + Skills | Enhanced single agent, Skill-augmented agent |

When the user uses any synonym, map it to the canonical term internally but mirror their preferred language in conversation.

---

## Best Practices

1. **Start simple, scale intelligently** — prove ROI with single agents first
2. **Choose the right model for the job** — balance capabilities, speed, and cost
3. **Practice modular design** — prompts in config files, tools as discrete modules, agents composed as needed
4. **Build observable systems** — structured logging, prompt chain visibility, token monitoring, reasoning workflow traces
5. **Use Agent Skills** — modular capability packages for domain expertise, composable and independently updatable
6. **Manage context actively** — context editing, memory tools, pagination, response capping (~25K tokens)
