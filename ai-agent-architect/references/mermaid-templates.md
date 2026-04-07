# Mermaid Diagram Templates for Architecture Blueprints

Use these templates when generating the "Architecture Diagram" section in Phase 3. Select the template matching the recommended pattern, then customize with actual component names from the interview.

## Rules

1. **Always use top-down (TD) layout** for consistency
2. **Label every edge** with the interaction type (tool call, message, data, event)
3. **Color-code by role:** use `:::supervisor` for orchestrators, `:::worker` for agents, `:::tool` for integrations. These are the base classes; templates may add pattern-specific classes as needed (e.g., `:::skill`, `:::branch`, `:::merge`, `:::gen`, `:::eval`, `:::router`)
4. **Keep it readable:** max 8-10 nodes per diagram. If more complex, split into overview + detail diagrams
5. **Include a legend** as a comment at the top

---

## Single Agent

```mermaid
graph TD
    User([User]) -->|request| Agent[Agent<br/>Model: Sonnet]
    Agent -->|tool call| T1[Tool: Database]
    Agent -->|tool call| T2[Tool: API]
    Agent -->|tool call| T3[Tool: File System]
    Agent -->|response| User

    classDef worker fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef tool fill:#7BC47F,stroke:#4A9A4E,color:#fff
    class Agent worker
    class T1,T2,T3 tool
```

---

## Single Agent + Skills

```mermaid
graph TD
    User([User]) -->|request| Agent[Agent<br/>Model: Sonnet]
    Agent -->|activate| S1[Skill: Domain A]
    Agent -->|activate| S2[Skill: Domain B]
    Agent -->|tool call| T1[Tool: System 1]
    Agent -->|tool call| T2[Tool: System 2]
    Agent -->|response| User

    S1 -.->|context| Agent
    S2 -.->|context| Agent

    classDef worker fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef skill fill:#F5A623,stroke:#D4891A,color:#fff
    classDef tool fill:#7BC47F,stroke:#4A9A4E,color:#fff
    class Agent worker
    class S1,S2 skill
    class T1,T2 tool
```

---

## Hierarchical Multi-Agent

```mermaid
graph TD
    User([User]) -->|request| Supervisor[Supervisor Agent<br/>Model: Sonnet]
    Supervisor -->|delegate| A1[Specialist A<br/>Model: Haiku]
    Supervisor -->|delegate| A2[Specialist B<br/>Model: Sonnet]
    Supervisor -->|delegate| A3[Specialist C<br/>Model: Haiku]
    A1 -->|tool call| T1[Tool: System 1]
    A2 -->|tool call| T2[Tool: System 2]
    A3 -->|tool call| T3[Tool: System 3]
    A1 -->|result| Supervisor
    A2 -->|result| Supervisor
    A3 -->|result| Supervisor
    Supervisor -->|synthesized response| User

    classDef supervisor fill:#9B59B6,stroke:#7D3C98,color:#fff
    classDef worker fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef tool fill:#7BC47F,stroke:#4A9A4E,color:#fff
    class Supervisor supervisor
    class A1,A2,A3 worker
    class T1,T2,T3 tool
```

---

## Sequential Workflow

```mermaid
graph TD
    Input([Input]) -->|start| S1[Stage 1: Classify<br/>Model: Haiku]
    S1 -->|handoff| S2[Stage 2: Process<br/>Model: Sonnet]
    S2 -->|handoff| S3[Stage 3: Validate<br/>Model: Haiku]
    S3 -->|handoff| S4[Stage 4: Output<br/>Model: Haiku]
    S4 -->|result| Output([Output])

    S2 -->|tool call| T1[Tool: External API]
    S3 -->|tool call| T2[Tool: Validation DB]

    classDef stage fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef tool fill:#7BC47F,stroke:#4A9A4E,color:#fff
    class S1,S2,S3,S4 stage
    class T1,T2 tool
```

---

## Parallel Workflow

```mermaid
graph TD
    Input([Input]) -->|fan-out| P1[Branch A<br/>Perspective 1]
    Input -->|fan-out| P2[Branch B<br/>Perspective 2]
    Input -->|fan-out| P3[Branch C<br/>Perspective 3]
    P1 -->|result| Merge[Aggregator<br/>Merge & Resolve]
    P2 -->|result| Merge
    P3 -->|result| Merge
    Merge -->|final output| Output([Output])

    classDef branch fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef merge fill:#F5A623,stroke:#D4891A,color:#fff
    class P1,P2,P3 branch
    class Merge merge
```

---

## Evaluator-Optimizer

```mermaid
graph TD
    Input([Input]) -->|initial request| Gen[Generator<br/>Model: Sonnet]
    Gen -->|draft| Eval[Evaluator<br/>Model: Sonnet]
    Eval -->|feedback| Gen
    Eval -->|approved| Output([Final Output])

    Gen -->|tool call| T1[Tool: Data Source]
    Eval -->|tool call| T2[Tool: Validation Rules]

    classDef gen fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef eval fill:#E74C3C,stroke:#C0392B,color:#fff
    classDef tool fill:#7BC47F,stroke:#4A9A4E,color:#fff
    class Gen gen
    class Eval eval
    class T1,T2 tool
```

---

## Hybrid: Single Agent + Multi-Agent Escalation

```mermaid
graph TD
    User([User]) -->|request| Router{Complexity<br/>Check}
    Router -->|simple| Single[Single Agent<br/>+ Skills]
    Router -->|complex| Supervisor[Supervisor Agent]
    Single -->|response| User
    Supervisor -->|delegate| A1[Specialist A]
    Supervisor -->|delegate| A2[Specialist B]
    A1 -->|result| Supervisor
    A2 -->|result| Supervisor
    Supervisor -->|synthesized| User

    classDef router fill:#F5A623,stroke:#D4891A,color:#fff
    classDef single fill:#4A90D9,stroke:#2C5F8A,color:#fff
    classDef supervisor fill:#9B59B6,stroke:#7D3C98,color:#fff
    classDef worker fill:#3498DB,stroke:#2471A3,color:#fff
    class Router router
    class Single single
    class Supervisor supervisor
    class A1,A2 worker
```

---

## Customization Checklist

When adapting a template for a specific blueprint:

1. Replace generic names (Specialist A, System 1) with actual names from the interview (e.g., "Order Agent", "Shopify API")
2. Add the recommended model tier to each agent node (Opus/Sonnet/Haiku)
3. Label edges with specific data types (e.g., "order JSON", "customer record")
4. Add any MCP connections as tool nodes
5. If brownfield: add a "Legacy System" node with dashed lines showing integration points
6. Keep the color scheme consistent across all diagrams in the same blueprint
