# Exploration Matrix — Direction Tracking & Coverage

The exploration matrix prevents two failure modes: (1) accidentally revisiting a direction that already failed, and (2) missing an entire category of potential ideas. Maintain it across all rounds and update it after each round's evaluation.

## Template

Copy this template at the start and fill in direction categories relevant to the user's research problem.

```markdown
## Exploration Matrix

### Method Depth (deeper/better techniques within the existing paradigm)
- [ ] [Specific sub-direction] — Status: unexplored
- [✓] [Specific sub-direction] — R[N]: [Idea name] ([score]), [outcome: qualified/failed]
  - Lessons: [1 sentence takeaway]

### Problem Scope Expansion (broader settings, new constraints, new applications)
- [ ] [Specific sub-direction] — Status: unexplored

### Frontier Tech Fusion (integration with emerging technologies)
- [ ] [Specific sub-direction] — Status: unexplored

### Theoretical Foundation (formalizing, proving, bounding)
- [ ] [Specific sub-direction] — Status: unexplored

### Fundamental Challenge (questioning core assumptions)
- [ ] [Specific sub-direction] — Status: unexplored

### [Other category as needed]
- [ ] [Specific sub-direction] — Status: unexplored
```

## Direction Categories

These are the most common high-level categories. Not all apply to every project; pick the relevant ones and add project-specific categories.

| Category | Description | When to use |
|----------|-------------|-------------|
| **Method Depth** | Improving/augmenting the core technique within the existing paradigm | Always relevant |
| **Problem Scope Expansion** | Extending to new settings (heterogeneous, dynamic, multi-domain) or adding new constraints (privacy, energy, scalability) | When the current scope feels narrow |
| **Frontier Tech Fusion** | Integrating with emerging technologies (LLM/VLM, diffusion, foundation models, etc.) | When there's a hot new tech that could change the game |
| **Theoretical Foundation** | Adding formal analysis, proofs, bounds, or connecting to established theory | When the original work is empirically strong but theoretically weak |
| **Fundamental Challenge** | Questioning core assumptions of the original work | When you want a truly novel angle |
| **Evaluation/Impact** | New evaluation methods, new metrics, new benchmarks | When the contribution could be in how we measure/assess |
| **Deployment/Practicality** | Making the system more deployable, robust, maintainable | When the gap between research and practice is large |

## After Each Round

Update the matrix entry for the explored direction:

```markdown
- [✓] Method Depth — Lyapunov optimization — R2: Lyapunov Unified Inference (7.40), qualified
  - Lessons: Provides theoretical guarantees but Critic flagged "apply X to Y" risk. Need domain-specific adaptation.
```

## Before Each New Round

1. **Check the matrix**: Which categories have few or no entries? Prioritize those for breadth.
2. **Check recent lessons**: Do the last 1-2 rounds suggest a promising direction to deepen?
3. **Apply the exploration rate**: If `exploration_rate = 0.8`, then 80% of rounds should explore NEW sub-directions, 20% can refine previous ideas.

## End-of-Process Coverage Report

After all rounds, include in SUMMARY.md:

```markdown
## Direction Coverage

| Category | Sub-directions Explored | Qualified Ideas | Best Score |
|----------|------------------------|-----------------|------------|
| Method Depth | 5 | 4 | 7.90 |
| Problem Scope | 2 | 2 | 7.50 |
| Frontier Fusion | 2 | 0 | 6.90 |
| Theoretical | 3 | 3 | 8.05 |
| Fundamental | 1 | 1 | 8.55 |
| **Total** | **13** | **10** | — |

### Gaps (unexplored or under-explored)
- Deployment/Practicality: Never explored — could be worth a round
- Problem Scope — Heterogeneous devices: Mentioned but never developed
```

This coverage analysis is valuable meta-information — it tells the user not just which ideas are good, but which categories of ideas were never even considered.
