# Role Definitions & Scoring Rubrics

This file defines the default four-role evaluation panel and provides domain-specific variants. All roles, dimensions, and weights are configurable.

## Default Role Panel

### 1. Domain Expert

**Perspective**: Deep expertise in the specific research field. Knows the literature, the SOTA, the standard baselines, and the unspoken assumptions of the community.

**Scoring Dimensions** (each 1-10):

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| **Feasibility** | 0.25 | Can this actually be implemented and evaluated? Are there hidden blockers (data availability, compute requirements, tooling maturity)? Is the experimental setup realistic? |
| **SOTA Differentiation** | 0.25 | How clearly does this differ from existing published work? Would a knowledgeable reviewer immediately recognize the contribution as distinct? Or does it blur into existing methods? |

**Feasibility Rubric**:
- 9-10: Immediately implementable with standard tools and datasets. Clear path to evaluation.
- 7-8: Feasible but has non-trivial implementation challenges that are solvable.
- 5-6: Significant engineering or data hurdles. May require infrastructure not commonly available.
- 3-4: Major blockers (no suitable dataset, requires unavailable hardware, etc.).
- 1-2: Effectively impossible under realistic constraints.

**Differentiation Rubric**:
- 9-10: Opens a genuinely new direction. Would be cited as "the first paper to do X."
- 7-8: Clearly distinct from SOTA with a novel combination or application.
- 5-6: Incremental improvement over existing work. Different but not dramatically so.
- 3-4: Marginal difference from published methods. Reviewer would struggle to articulate the contribution.
- 1-2: Essentially identical to existing work with cosmetic differences.

### 2. Cross-Domain Generalist

**Perspective**: Broad knowledge across adjacent fields (ML systems, networking, computer vision, control theory, information theory, etc.). Identifies connections that domain specialists might miss.

**Scoring Dimensions** (each 1-10):

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| **Cross-domain Transfer Potential** | 0.15 | Can methods/theories from other fields be productively applied? Is there a natural bridge? Does this idea open a two-way street (benefits both the target field and the source field)? |
| **Generalizability** | 0.10 | Does this idea apply beyond the specific problem setting? Could it be a general principle rather than a point solution? Would researchers in other domains find it useful? |

**Transfer Potential Rubric**:
- 9-10: Transfers a deep theoretical framework from another field in a non-trivial way. The connection is insightful, not obvious.
- 7-8: Productively borrows from another field with meaningful adaptation.
- 5-6: References another field but the connection is superficial or the adaptation is trivial.
- 3-4: The "transfer" is just name-dropping without real methodological connection.
- 1-2: No cross-domain connection exists or is attempted.

**Generalizability Rubric**:
- 9-10: The core insight applies to a whole class of problems across multiple domains.
- 7-8: Applicable to several related problem settings beyond the target.
- 5-6: Could generalize but only with substantial modification.
- 3-4: Tightly coupled to the specific problem; hard to imagine elsewhere.
- 1-2: A pure point solution with zero generalizability.

### 3. Devil's Advocate (Critic)

**Perspective**: Relentlessly critical. Assumes nothing. Identifies every weakness, hidden assumption, and potential attack vector. The goal is NOT to be fair — it's to stress-test the idea to destruction.

**Scoring Dimensions** (each 1-10):

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| **Innovation (Genuine Novelty)** | 0.15 | Is there a real, non-trivial insight here? Or is this "apply X to Y", "X but with Y", or "X meets Y"? Does it challenge a core assumption or reveal a previously unnoticed trade-off? |
| **Defensibility** | 0.10 | How well does this idea withstand reviewer attacks? What are the most damaging critiques, and how hard would they be to rebut? If the core assumption fails, does the whole idea collapse? |

**Innovation Rubric**:
- 9-10: Genuinely surprising insight. Challenges a core assumption. "I never thought of it that way." Not "apply X to Y."
- 7-8: Has a real insight, even if the technique is borrowed. The application reveals something new.
- 5-6: Incremental novelty. "Apply X to Y" but X is adapted in a non-trivial way.
- 3-4: "Apply X to Y" with trivial adaptation. The connection is obvious.
- 1-2: No novelty. Repackaging of existing ideas with new terminology.

**Defensibility Rubric**:
- 9-10: The core claim is robust even if specific implementation details are questioned. Multiple lines of defense.
- 7-8: Main claims are defensible with standard experimental evidence. Minor vulnerabilities.
- 5-6: Several claims require generous assumptions. A determined reviewer could land damaging critiques.
- 3-4: Core assumption is fragile. If any of 2-3 key premises fail, the whole thing collapses.
- 1-2: Fatal flaw visible on first inspection. Would not survive review.

**The "Apply X to Y" Detection Checklist**:
When evaluating innovation, the Critic MUST explicitly check:
1. Is the core technique X a well-known method from another field?
2. Is the problem Y a standard problem in the target field?
3. Is the adaptation of X for Y trivial (change input/output format, rename variables)?
4. Has X been applied to something similar to Y before?
5. Does the idea reveal something NEW about Y through the application of X?

If answers are (yes, yes, yes, ?, no) → likely "Apply X to Y" → innovation score should be ≤ 5.

### 4. Mock Reviewer

**Perspective**: Simulates a real program committee reviewer reading the final paper. This is an **advisory role only** — their scores are NOT included in the weighted formula but are captured in the log.

**Scoring Dimensions** (each 1-10, advisory):

| Dimension | Weight | What it measures |
|-----------|--------|------------------|
| **Overall Contribution Significance** | advisory | If this were a paper submission, would the contribution warrant acceptance? Is this a "nice to have" or a "need to have"? |
| **Clarity of Motivation** | advisory | Is it immediately clear WHY this work is needed? Is the problem real and well-motivated, or does it feel constructed? |

**Contribution Significance Rubric**:
- 9-10: Clear accept. Significant contribution that advances the field.
- 7-8: Weak accept. Solid contribution but not groundbreaking.
- 5-6: Borderline. Would need strong championing in PC discussion.
- 3-4: Weak reject. Incremental or unclear contribution.
- 1-2: Strong reject. Fundamentally flawed or trivial.

**Clarity of Motivation Rubric**:
- 9-10: The problem is obviously important. The motivation section writes itself.
- 7-8: Well-motivated but may need some framing work.
- 5-6: The problem exists but its importance needs to be argued carefully.
- 3-4: The motivation feels constructed or the problem is niche without clear impact.
- 1-2: "A solution in search of a problem."

## Domain-Specific Role Variants

### CS Systems / Networking Research

Same four roles, but calibrate the **Expert** with these domain-specific reference points:

**What CS systems reviewers look for**:
- Is there a real implementation? (Simulation alone is increasingly insufficient at top venues)
- Are the baselines fair and comprehensive?
- Is the improvement practically significant (not just statistically significant)?
- Does the system make unreasonable assumptions about the deployment environment?
- Is the overhead of the proposed mechanism justified by the benefit?

**Common rejection patterns in CS systems**:
- "The technique is well-known; the application is new but doesn't require non-trivial adaptation."
- "The evaluation is too narrow (single dataset, single hardware config, no sensitivity analysis)."
- "The problem is real but the proposed solution is an engineering optimization, not a research contribution."

### ML / AI Research

Calibrate with these reference points:

**What ML reviewers look for**:
- Theoretical justification OR surprising empirical findings (need at least one)
- Comparison against the RIGHT baselines (not just the easiest ones)
- Ablation studies that isolate the contribution of each component
- Generalization beyond the specific dataset/architecture used

**Common rejection patterns in ML**:
- "Incremental improvement over [recent paper] without fundamental new insight."
- "Missing key baselines; the comparison is unfair or incomplete."
- "The method works on one dataset but there's no reason to believe it generalizes."

### Custom Domain Configuration

When working in a domain not covered above, configure the roles by answering:

1. What are the top 3 venues in this field, and what do their reviewers value most?
2. What are the most common rejection reasons in this field?
3. What is the most overused/"saturated" narrative in this field right now?
4. What counts as a "significant contribution" vs "incremental work" in this field?

Feed the answers into the Expert and Reviewer role descriptions.
