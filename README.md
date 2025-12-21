# OMSA — Program Activities & Research Direction (Security + AI)

## OMSA

**Master of Science in Analytics (OMSA), Georgia Tech** : Online program with the same faculty and curriculum as the on-campus program.  
Program info: https://pe.gatech.edu/degrees/analytics

With 25 years of experience in the security industry, my goal in the OMSA program is to build a CTO-level understanding of the modern AI stack—how to apply AI/ML/LLMs to real security problems, and how to evaluate and mitigate the security risks introduced by these systems (hallucination, data leakage, misuse, and model vulnerabilities).

---
## MS Thesis

### Working Title
**Engineering a Controllable AI Stack for Contract-Based Auditing of Linux Kernel Drivers**  
**Subtitle (optional):** *A Layered Neuro-Symbolic Auditor with Contract DSL, Verifier-Guided Tuning, and Cross-Version Robustness*  
**Tagline:** *The Neuro-Symbolic Auditor*

---

### Positioning (Why this goes beyond the “baseline”)
A common foundation in modern security tooling is:

- **LLM = hypothesis generator** (find suspicious logic)
- **Static analysis = verifier** (confirm/reject)

This thesis deliberately goes **beyond** that foundation.

**Core idea:** *Treat the AI stack as a controllable engineering system, not a fixed black box.*  
Rather than “use an LLM and a verifier,” we **engineer a layered stack** that makes LLM-based auditing more:

- **detectable** (contract-first specifications + structured witnesses)
- **verifiable** (validator-gated outputs and proof obligations)
- **deployable** (minimal-context retrieval, calibrated abstention, cross-version robustness)
- **self-improving** (verifier-guided tuning driven by validator outcomes)

This turns “LLM + static analysis” into a real-world auditing pipeline that can be deployed and maintained as the kernel evolves.

---

### Motivation
Many kernel-driver security issues are not simple “pattern matches.” They often arise from **implicit lifecycle intent gaps** (what developers intended vs. what code actually enforces), including:

- resource/lifetime mismanagement (UAF-like risk, double free, leaks)
- teardown ordering violations (race windows)
- runtime power management misuse
- missing synchronization during remove/unregister paths

These issues are difficult to detect reliably because logic spans multiple functions/macros/paths, and purely rule-based tools can generate high alert volume without strong prioritization.

---

### Thesis Hypothesis
**Explicit “contracts”** (resource/ordering/state invariants) provide a stable interface between stochastic reasoning and deterministic checking.

- A fine-tuned LLM can act as an **untrusted hypothesis generator**, producing **structured, evidence-grounded violation witnesses** (not free-form claims).
- A deterministic validator (Coccinelle/CodeQL) can **confirm / reject / mark inconclusive**, shifting trust away from the model and toward verifiable evidence.
- We further improve the stack by adding:
  - a minimal **Contract DSL** (specification layer),
  - **verifier-guided tuning (DPO-lite or RAFT)** (self-improvement layer),
  - and **cross-version evaluation** (real-world robustness).

---

### System Overview (Layered, Closed-Loop Security Auditor)

#### Layer 0: Contract Spec (Minimal DSL)
- Define contracts as first-class objects using a compact DSL (YAML/JSON) describing:
  - scope (probe/remove/suspend/resume/ISR)
  - acquire/release events and obligations
  - sensitive actions (preconditions)
  - ordering rules (happens-before constraints)
  - exceptions
- **Output:** a contract file + (optionally) a validator-rule skeleton generated from it.

#### Layer 1: Memory / Grounding (Graph + Retrieval)
- Instead of pasting entire driver files, retrieve **minimal context** needed to evaluate a contract:
  - dependency/call relationships
  - related helper functions
  - relevant API usage patterns
- Retrieval improves grounding but does not remove errors; it is part of the controlled stack.

#### Layer 2: Brain (LLM)
- Fine-tuned LLM generates **structured witnesses** rather than summaries.
- Output is intentionally checkable:
  - evidence spans (file:function:line ranges)
  - witness path (call/goto/return sketch)
  - assumptions + falsifiers
  - confidence / uncertainty

#### Layer 3: Judge (Validator Gate)
- Static validator (e.g., Coccinelle and/or CodeQL) adjudicates each hypothesis:
  - **CONFIRMED** (high confidence *under validator semantics*)
  - **REJECTED**
  - **INCONCLUSIVE** (missing config/macro/callee info, tool limits)
- This gate is the primary safety mechanism for deployment.

#### Layer 4: Self-Improvement via Verifier-Guided Tuning (DPO-lite or RAFT)
- Use validator outcomes to create an automated improvement loop.
- **Primary plan:** verifier-aligned **DPO/ORPO** using preference pairs:
  - Preferred: CONFIRMED witnesses and correct abstentions
  - Dispreferred: REJECTED outputs or hallucinated claims
- **Mitigation / fallback (MS scope):** if DPO is too finicky or computationally expensive, use **RAFT (Rejection Sampling Fine-Tuning)**:
  - generate multiple candidate witnesses,
  - keep only validator-confirmed (or high-quality abstention) outputs,
  - fine-tune the model on those “accepted” examples.
- Both approaches are framed under one layer: *learning from the validator to improve verifiability and calibration.*

*A controllable, layered AI auditing stack. Contracts define checkable intent; graph-grounded retrieval supplies minimal context; the LLM generates structured witnesses; a validator gate confirms/rejects/marks inconclusive; verifier-guided tuning (DPO-lite or RAFT) uses validator outcomes to improve witness correctness and calibrated abstention. The result is an engineered, deployable system rather than a one-shot “LLM + verifier” pipeline.*


---

### Contracts in Scope (MS)
To keep the thesis focused and measurable, implement and evaluate **two contract families**:

1) **Runtime PM contract (resource/typestate)**  
   - Example: “PM must be active before certain sensitive HW accesses,” and/or “get/put balanced across exits.”

2) **Quiesce-before-free contract (ordering/concurrency)**  
   - Example: “disable/synchronize IRQ (or cancel/flush work) before freeing/unregistering shared state.”

---

### Cross-Version Evaluation (Lightweight, Real-World Robustness)
A deployable auditor must work as the kernel evolves. This thesis includes a lightweight cross-version evaluation:

- Train/tune on one kernel version (or release range)
- Evaluate on a newer version (or different subsystem snapshot)
- Track causes of INCONCLUSIVE:
  - macro/config changes
  - helper refactors / API drift
  - missing callgraph edges
- Report how retrieval and contract DSL adjustments reduce inconclusive rates.

---

### Success Criteria (MS evaluation)
Primary success is not “finding CVEs.” It is producing a controllable pipeline that:

- reduces triage burden (**alert reduction vs static-only**)
- improves confirmed yield (**confirmed / reviewed**)
- localizes evidence well (near validated site)
- handles uncertainty honestly (**high-quality INCONCLUSIVE** with explicit missing assumptions)
- improves over time with verifier-guided tuning (rejected ↓, confirmed ↑, calibration ↑)

**Metric additions (usability-focused):**
- **Abstention Precision:** when the model outputs **INCONCLUSIVE**, how often is it *truly missing information* (macro/config/callee/context limits) versus abstaining unnecessarily?
- **Abstention Recall (optional):** among cases that *should be* inconclusive under available context, how often does the model correctly abstain?

**Robustness ablation (semantic gap test):**
- **Robustness against naming variations:** evaluate with identifiers/comments removed or systematically renamed to test whether the model is learning contract logic rather than memorizing superficial naming patterns (e.g., `ret`, `err`, `rc`).

---

### Outputs / Deliverables (MS)
- Reproducible repository:
  - `contracts/` minimal DSL + examples + (optional) validator-rule skeleton generator
  - `pipeline/` (graph retrieval + slice → prompt → witness JSON)
  - `validators/` (Coccinelle/CodeQL harness + labeling)
  - `training/` (SFT + verifier-guided tuning via DPO-lite or RAFT)
  - `eval/` (metrics + ablations + cross-version protocol)
- Benchmark suite using **public** bug-fix pairs and hard negatives (tricky but safe code), plus optional synthetic mutants filtered by validators.
- Paper-style write-up suitable for course deliverables and thesis chapters.

---

## PhD Extension (Forward-looking Roadmap)

### Working Title
**Autonomous Evolution of Kernel Security Contracts via Contract Mining and Verifier-Aligned Training**  
**Tagline:** *The Autonomous Architect*

### Theme
Evolution & discovery: move from “checking known contract families” to **discovering new contract families** and aligning model reasoning to verifiable outputs at scale.

### PhD Thesis Hypothesis (extension)
**Bug-fix commits encode latent intent.** By mining fix commits over long time spans, we can infer and maintain a taxonomy/library of implicit kernel contracts. By using validator outcomes as preference feedback, we can align LLMs to produce verifier-confirmable witnesses (or calibrated abstentions), improving generalization and reducing hallucinations.

### Extension Components
1) **Contract Mining (Spec discovery)**
- Mine contracts from git history using:
  - fix commit heuristics (Fixes tags, stable backports, subsystem paths)
  - patch delta features (added/removed calls, reorderings, new guards, unwind changes)
  - clustering into contract templates
- Start with “resource lifecycle & ordering” contract families before expanding.

2) **Verifier-aligned DPO / ORPO at scale**
- Multi-contract, multi-validator alignment
- Drift-aware preference generation across kernel versions

3) **Safe adversarial stress testing**
- Generate constrained counterexamples / contract-violation candidates (**not weaponized exploit code**) to stress-test robustness and improve generalization.

---

## Projects mapped to OMSA / course areas

### Deep Learning (DL) — The Model (Brain)
**Project focus:** Fine-tuning LLMs (QLoRA) for structured witness generation  
- Study training dynamics: learning rate, LoRA rank, stability, loss curves.
- Evaluate base vs SFT vs SFT + verifier-guided tuning (DPO-lite or RAFT).  
**Management takeaway:** estimate compute costs, debug training instability, and make practical cost/quality tradeoffs.

### Natural Language Processing (NLP) — The Data (Translator)
**Project focus:** Kernel-aware dataset construction and tokenization  
- Build contract-conditioned prompts.
- Investigate tokenization issues (macros, conditional compilation).
- Data augmentation via safe synthetic mutants + validator filtering.  
**Management takeaway:** “data is the moat”—data quality dominates outcome.

### Reinforcement Learning / Alignment (RL) — The Loop
**Project focus:** Verifier-guided alignment using DPO/ORPO (or RAFT as MS fallback)  
- Train the model to prefer verified witnesses over hallucinated ones.
- Measure calibration and abstention quality.  
**Management takeaway:** safety/alignment in practice—systems that avoid being confidently wrong.

### Knowledge Graphs / Networks (KG) — The Memory (Grounding)
**Project focus:** Modeling kernel dependencies for retrieval grounding  
- Represent dependencies (call/require/guard relationships).
- Use retrieval to provide minimal context slices.
- Optionally explore GNNs or graph-based retrieval heuristics.  
**Management takeaway:** retrieval systems and grounding strategies for reliable AI products.

---

## Coursework

### Completed
https://pe.gatech.edu/degrees/analytics 
- [ISYE 6501: Intro to Analytics Modeling](https://omscs.gatech.edu/isye-6501-intro-analytics-modeling)
- [CSE 6040: Computing for Data Analysis](https://cse6040.gatech.edu/active/)
- [ISYE 6420: Bayesian Statistics](https://omscs.gatech.edu/isye-6420-bayesian-statistics)
- [CSE 6242: Data and Visual Analytics](https://omscs.gatech.edu/cse-6242-data-and-visual-analytics)
- [CS 6601: Artifical Intelligence](https://omscs.gatech.edu/cs-6601-artificial-intelligence)
  
### Planned
- [ISYE 6740 Computational Data Analytics](https://www2.isye.gatech.edu/~yxie77/ISyE6740-2025Spring-Xie-Syllabus)
- [CS 7643: Deep Learning](https://omscs.gatech.edu/cs-7643-deep-learning) 
- [CS 6742: Reinforcement Learning](https://omscs.gatech.edu/cs-7642-reinforcement-learning) 
- [CSE 8803: Applied Natural Language Processing](https://omscs.gatech.edu/cs-7650-natural-language-processing)
- [CS 7280: Nework Science (Graph)](https://omscs.gatech.edu/cs-7280-network-science)  

## Pre-requisites
- [CS 1332: Data Structure and Algorithm](https://www.edx.org/certificates/professional-certificate/gtx-data-structures-and-algorithms) 
- [CS 1331: Introduction to Object-Oriented Programming](https://www.edx.org/certificates/professional-certificate/gtx-introduction-to-object-oriented-programming-with-java)  
- [Math 1554: Linear algebra](https://math.gatech.edu/courses/math/1554)
- [Math 1712: Calculus](https://math.gatech.edu/courses/math/1712) 
- [ISYE 6739: Probability and Statistics](https://www2.isye.gatech.edu/~sman/courses/6739/) 

## Self-Studies
- [Harvard CS197: AI Research Experiences](https://www.cs197.seas.harvard.edu/)

## Books I like
- [Book of Proof](https://richardhammack.github.io/BookOfProof/)
- [LINEAR ALGEBRA with Applications by W.Keith Nicholson](https://open.umn.edu/opentextbooks/textbooks/533)
- [Mathematics for Mahine Learning](https://mml-book.github.io/)
- [An Introduction to Statistical Learning](https://www.statlearning.com/)
- [The Elements of Statistical Learning](https://hastie.su.domains/Papers/ESLII.pdf)
- [Artificial Intelligence: A Modern Approach, 4th US ed.](https://aima.cs.berkeley.edu/)






