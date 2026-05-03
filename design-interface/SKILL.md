---
name: design-interface
description: Generate multiple radically different interface designs for a module using parallel sub-agents. Use when user wants to design an API, explore interface options, compare module shapes, or mentions "design it twice".
---

# Design an Interface

Based on "Design It Twice": your first idea is unlikely to be the best. Generate radically different designs in parallel, then compare.

## Workflow

### 1. Generate Designs (Parallel Sub-Agents)

Spawn 3+ sub-agents simultaneously. Each must produce a **radically different** approach.

```
Design an interface for: [module description]

Requirements: [gathered from user]

Constraint for this design: [assign a different constraint to each agent]
- Agent 1: "Minimize method count - aim for 1-3 methods max"
- Agent 2: "Maximize flexibility - support many use cases"
- Agent 3: "Optimize for the most common case"
- Agent 4: "Take inspiration from [specific paradigm/library]"

Output format:
1. Interface signature (types/methods)
2. Usage example (how caller uses it)
3. What this design hides internally
4. Trade-offs of this approach
```

### 2. Compare

After showing all designs, compare on:

- **Interface simplicity**: fewer methods, simpler params
- **General-purpose vs specialized**: flexibility vs focus
- **Implementation efficiency**: does shape allow efficient internals?
- **Depth**: small interface hiding significant complexity (good) vs large interface with thin implementation (bad)
- **Ease of correct use** vs **ease of misuse**

Discuss trade-offs in prose, not tables. Highlight where designs diverge most.

### 3. Synthesize

Ask: "Which design best fits your primary use case? Any elements from other designs worth incorporating?"

## Anti-Patterns

- Don't let sub-agents produce similar designs — enforce radical difference
- Don't skip comparison — the value is in contrast
- Don't implement — this is purely about interface shape
- Don't evaluate based on implementation effort
