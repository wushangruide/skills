---
name: research-ideation
description: Use this skill when the user wants to systematically generate, evaluate, and iterate on research ideas — especially when facing problems like insufficient innovation, blocked research direction, or need for structured brainstorming. Triggers on phrases like "generate research ideas", "improve innovation", "brainstorm systematically", "need novel directions for my paper", or when the user describes a research problem and explicitly asks for structured idea exploration. Do NOT trigger for casual discussion of research ideas, one-off brainstorming, or when the user is actively implementing code. The skill orchestrates an iterative loop with multi-role expert review, weighted scoring, knowledge accumulation, and structured output.
---

# Research Ideation — Iterative Idea Generation with Multi-Role Review

Systematically generate, evaluate, refine, and curate research ideas through an iterative loop with structured multi-role expert review.

## When to Use

The user has a research problem (paper revision, project direction, thesis topic) and wants **systematic exploration** — not a one-off suggestion, but a structured process that produces multiple high-quality candidate ideas with rigorous evaluation.

**Trigger signals**: "generate multiple ideas", "systematic brainstorming", "need to improve innovation", "my paper was rejected for X, help me find new directions", "iterate on research ideas until we find something good".

**Do NOT trigger**: casual "what do you think about X?", one-off brainstorming ("give me an idea for Y"), implementation work in progress.

## Workflow Overview

```
┌──────────────────────────────────────────────────┐
│              Knowledge Base (accumulating)         │
│  Explored directions | Literature finds | Lessons   │
└──────────────────────────────────────────────────┘
                        ↓ injects into
┌──────────────────────────────────────────────────┐
│  Round N:                                         │
│  ① Idea Generation  →  ② Multi-Role Review        │
│       ↑                       ↓                   │
│       |               ③ Weighted Scoring           │
│       |                       ↓                   │
│       |            ≥ threshold → Output .md + log │
│       |            < threshold → Archive           │
│       |                       ↓                   │
│       └────── ④ Feedback Extraction ──────────────┘│
│                                                    │
│  Stop: high-score early stop | consecutive failures│
│        | round cap                                 │
└──────────────────────────────────────────────────┘
                        ↓
           Cross-Idea Comparison (final pass)
                        ↓
                  SUMMARY.md + log.json
```

## Configuration

Before starting the first round, **confirm the configuration** with the user. The defaults below are the recommended starting point; every parameter is adjustable.

### Role Panel (default: 4 roles)

Roles and their scoring dimensions. See `references/roles.md` for detailed role definitions and domain-specific variants.

| Role | Perspective | Scoring Dimensions (each 1-10) | Default Weight |
|------|------------|-------------------------------|----------------|
| **Domain Expert** | Deep expertise in the specific field | Feasibility, SOTA Differentiation | 0.25 + 0.25 = 0.50 |
| **Cross-Domain Generalist** | Broad knowledge across adjacent fields | Cross-domain Transfer Potential, Generalizability | 0.15 + 0.10 = 0.25 |
| **Devil's Advocate** | Relentlessly critical, finds weaknesses | Innovation (genuine novelty), Defensibility (against reviewer attacks) | 0.15 + 0.10 = 0.25 |
| **Mock Reviewer** | Simulates a real reviewer reading the final paper | Overall Contribution Significance, Clarity of Motivation | 0.00 (advisory only) |

The **Mock Reviewer** is an advisory role — their scores are NOT included in the weighted formula, but their qualitative feedback is captured in the log and may reveal fatal flaws the other roles missed.

### Scoring (default)

```
Final Score = Expert(Feasibility × 0.25 + Differentiation × 0.25)
            + Generalist(Transfer × 0.15 + Generalizability × 0.10)
            + Critic(Innovation × 0.15 + Defensibility × 0.10)

Pass Threshold: ≥ 7.5 / 10
```

### Iteration Control (default)

| Parameter | Default | Description |
|-----------|---------|-------------|
| `threshold` | 7.5 | Minimum score to output idea as candidate |
| `early_stop_score` | 8.5 | Score that triggers immediate early stop (exceptionally strong idea) |
| `max_consecutive_failures` | 3 | Stop after this many consecutive rounds without a qualified idea |
| `max_rounds` | 15 | Hard cap on total rounds |
| `exploration_rate` | 0.8 | Fraction of rounds that explore NEW directions (vs refining previous ideas) |

## Round Execution

Each round follows this exact sequence:

### Step 1: Direction Selection

Before generating the idea, decide what direction to explore. Maintain an **exploration matrix** — a running list of directions already explored and their outcomes.

```
Directions explored:
- [✓] Method depth — Lyapunov optimization (7.40)
- [✓] Method depth — MI selective sharing (7.50)
- [ ] Problem scope — heterogeneous devices
- [ ] Frontier fusion — diffusion policy for scheduling
- ...
```

For the first few rounds, prioritize **breadth** (different directions). Later rounds can revisit promising directions for **depth** (refining a high-scoring approach). The exploration matrix prevents repeating failed directions and ensures coverage.

### Step 2: Literature Check

Before finalizing the idea, do a **two-tier literature check**:

1. **Existing knowledge scan**: Draw on training data to identify obvious collisions with well-known methods. Flag if the idea is too close to an established technique.
2. **Real-time search** (if WebSearch is available): Search for the specific combination of concepts in the idea. This catches very recent work (last 12 months) that would not be in training data.

If a direct collision is found, either modify the idea to differentiate, or pivot to a new direction. Document the search in the log.

### Step 3: Idea Generation

Generate exactly **one idea per round**. The idea should be:

- **Concrete**: Not "use X for Y", but a specific mechanism/approach with enough detail to evaluate
- **Self-contained**: Readable without reference to previous rounds
- **Differentiated**: Clearly state how it differs from existing work (including the user's own baseline)

Structure each idea with:
- **Core insight** (1 sentence — the "aha")
- **Specific design** (3-5 concrete components)
- **How it differs from SOTA** (explicit comparison, not hand-waving)
- **Why now** (what makes this feasible/applicable today that wasn't before)

### Step 4: Multi-Role Review

Each role evaluates the idea from their perspective, producing:

1. **Dimension scores** (1-10 for each of their assigned dimensions)
2. **Qualitative analysis** (2-4 paragraphs covering: what works, what's fragile, what's missing)
3. **Critical concerns** (the one or two things most likely to cause rejection/failure)

The review should be **adversarial yet fair** — the Critic and Reviewer especially should simulate real pushback, not rubber-stamp. The Expert and Generalist should bring their unique knowledge to bear.

### Step 5: Scoring and Decision

Compute the weighted score. Then:

- **Score ≥ 8.5**: Exceptional. Output immediately. Consider early stop (ask user).
- **Score ≥ 7.5**: Qualified. Output as candidate .md file + log entry.
- **Score < 7.5**: Archive. Extract lessons only. Do NOT output .md.

### Step 6: Feedback Extraction

The most important step for iteration quality. From the reviews, extract **3-5 specific lessons** that will guide the next round:

```
Round N Lessons:
1. [Specific thing to avoid, with reason from which role]
2. [Promising direction to explore, with reason from which role]
3. [Assumption that needs validation]
```

These lessons are **injected into the next round's prompt** under the Knowledge Base section. This is what makes later rounds better than early ones.

Lessons should be **actionable** ("avoid LLM-for-X patterns because Critic identified this as a saturated narrative") rather than vague ("need more innovation").

## Output Structure

### Per-Idea File (`ideas/idea_NN_short_name.md`)

Each qualified idea gets a standalone markdown file:

```markdown
# Idea #N: [Title]
**Round**: N | **Score**: X.XX/10 | **Direction**: [category]
**Status**: Candidate ✅

## Core Insight
[1 sentence]

## Specific Design
[3-5 components with enough detail to evaluate]

## How It Differs from SOTA
[Explicit comparison table or bullet list]

## Evaluation Summary
[Per-role scores in table format]

## Strengths
## Weaknesses & Risks
## Recommended Next Steps
```

### Round Log (`log.json`)

Structured record of every round (including failed ones):

```json
{
  "workflow": "research-ideation",
  "started": "2026-06-10T...",
  "config": {
    "threshold": 7.5,
    "roles": ["expert", "generalist", "critic", "reviewer"],
    "weights": {...}
  },
  "rounds": [
    {
      "round": 1,
      "direction": "frontier-fusion",
      "idea_title": "VLM Semantic Coordinator",
      "score": 6.90,
      "qualified": false,
      "lessons": ["avoid LLM-for-X narrative", "need theoretical depth"],
      "literature_searches": ["vlm edge video analytics 2025"],
      "exploration_matrix_update": "frontier-fusion ✓ (failed, low innovation)"
    }
  ],
  "cross_idea_comparison": {...}
}
```

### Summary (`SUMMARY.md`)

After all rounds complete, generate:

1. **Score ranking table** — all qualified ideas ranked by score
2. **Score trend chart** — ASCII bar chart showing score progression across rounds
3. **Direction coverage** — which directions were explored, which were missed
4. **Top 3-5 deep dives** — expanded analysis of the highest-scoring ideas
5. **Recommended synthesis** — how the best ideas could combine into a coherent contribution
6. **Process quality assessment** — what worked, what didn't, suggested parameter adjustments for next time

## Cross-Idea Comparison (Final Pass)

After the iteration loop ends, do a **pairwise comparison** of the top-scoring ideas:

1. Identify **synergies** — ideas that complement each other (e.g., one provides theory, another provides mechanism)
2. Identify **conflicts** — ideas that compete or contradict
3. Identify **gaps** — important directions that were NOT covered by any qualified idea
4. Recommend a **composite strategy** — how 2-3 ideas could be woven into a single, stronger contribution

This final pass adds value beyond the per-round evaluations by revealing relationships between ideas that were generated in isolation.

## Key Principles

### For the Evaluator (you)

- **The Critic is the MVP**. The Critic's "Apply X to Y" radar is the single most valuable quality filter. If the Critic says the idea is fundamentally "X applied to Y" without non-trivial adaptation, take it seriously — it's probably true.
- **Don't let the Expert and Generalist rubber-stamp**. Early excitement is common. Correlate their scores with the Critic's to detect over-optimism.
- **Literature checks prevent wasted rounds**. A 2-minute WebSearch before evaluation can save an entire round of wasted evaluation.
- **Feedback injection is what makes iteration work**. Vague lessons ("need more novelty") don't help. Specific lessons ("the cosine similarity component is the weakest link — ideas that replace it with something principled score higher") do.

### For the User

- This process does NOT replace human judgment. It generates structured options and rigorous evaluation to make your judgment more efficient.
- The best ideas often come in rounds 5-10, after the knowledge base has accumulated. Don't stop too early.
- The threshold is a filter, not a quality guarantee. A 7.6 and a 7.4 may not be meaningfully different. Read the qualitative reviews, not just the scores.

## References

- `references/roles.md` — Detailed role definitions, scoring dimension rubrics, and domain-specific role variants
- `references/exploration-matrix.md` — Template and guidance for maintaining the exploration matrix across rounds
