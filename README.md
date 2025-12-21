# OMSA — Program Activities & Research Direction (Security + AI)

## OMSA

**Master of Science in Analytics (OMSA), Georgia Tech** : Online program with the same faculty and curriculum as the on-campus program.  
Program info: https://pe.gatech.edu/degrees/analytics

With 25 years of experience in the security industry, my goal in the OMSA program is to build a CTO-level understanding of the modern AI stack—how to apply AI/ML/LLMs to real security problems, and how to evaluate and mitigate the security risks introduced by these systems (hallucination, data leakage, misuse, and model vulnerabilities).

---
## MS Thesis

### Working Title
**Engineering a Controllable AI Stack for Contract-Based Auditing of Linux Kernel Drivers**  
**Subtitle (optional):** *A Layered Neuro-Symbolic Auditor with Contract DSL, Verifier-Aligned Tuning, and Cross-Version Robustness*  
**Tagline:** *The Neuro-Symbolic Auditor*

---

### Positioning (Why this is beyond the “baseline”)
A common foundation in modern security tooling is:

- **LLM = hypothesis generator** (find suspicious logic)
- **Static analysis = verifier** (confirm/reject)

This thesis deliberately goes **beyond** that foundation.

**Core idea:** *Treat the AI stack as a controllable engineering system, not a fixed black box.*  
Rather than “use an LLM and a verifier,” we **engineer a layered stack** that makes LLM-based auditing more:

- **detectable** (contract-first specifications, structured witnesses)
- **verifiable** (validator-gated outputs and proof obligations)
- **deployable** (minimal-context retrieval, calibrated abstention, cross-version robustness)
- **self-improving** (verifier-aligned preference tuning)

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
  - **verifier-aligned preference tuning (DPO-lite)** (self-improvement layer),
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

#### Layer 4: Self-Improvement (DPO-lite)
- Use validator outcomes to form preference pairs:
  - Preferred: CONFIRMED witnesses and correct abstentions
  - Dispreferred: REJECTED outputs or hallucinated claims
- Apply a small verifier-aligned DPO/ORPO pass (LoRA/QLoRA) to improve:
  - witness correctness
  - evidence localization
  - calibrated abstention (INCONCLUSIVE quality)


*A controllable, layered AI auditing stack. Contracts define checkable intent; graph-grounded retrieval supplies minimal context; the LLM generates structured witnesses; a validator gate confirms/rejects/marks inconclusive; DPO-lite uses validator outcomes to improve witness correctness and calibrated abstention. The result is an engineered, deployable system rather than a one-shot “LLM + verifier” pipeline.*

---

### Contracts in Scope (MS)
To keep the thesis focused and measurable, implement and evaluate **two contract families**:

1) **Runtime PM contract (resource/typestate)**  
   - Example: “PM must be active before certain sensitive HW accesses,” and/or “get/put balanced across exits.”

2) **Quiesce-before-free contract (ordering/concurrency)**  
   - Example: “disable/synchronize IRQ (or cancel/flush work) before freeing/unregistering shared state.”

---

### Cross-Version Evaluation (Lightweight, Real-World Robustness)
A deployable auditor must work as the kernel evolves. This thesis will include a lightweight cross-version evaluation:

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
- improves over time with **DPO-lite** (rejected ↓, confirmed ↑, abstention calibration ↑)

**Ablation (semantic gap test):**
- With vs without identifiers/comments (or renamed identifiers) to measure reliance on semantic cues.

---

### Outputs / Deliverables (MS)
- Reproducible repository:
  - `contracts/` minimal DSL + examples + (optional) validator-rule skeleton generator
  - `pipeline/` (graph retrieval + slice → prompt → witness JSON)
  - `validators/` (Coccinelle/CodeQL harness + labeling)
  - `training/` (SFT + DPO-lite scripts)
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
- Evaluate base vs SFT vs SFT + DPO-lite.  
**Management takeaway:** estimate compute costs, debug training instability, and make practical cost/quality tradeoffs.

### Natural Language Processing (NLP) — The Data (Translator)
**Project focus:** Kernel-aware dataset construction and tokenization  
- Build contract-conditioned prompts.
- Investigate tokenization issues (macros, conditional compilation).
- Data augmentation via safe synthetic mutants + validator filtering.  
**Management takeaway:** “data is the moat”—data quality dominates outcome.

### Reinforcement Learning / Alignment (RL) — The Loop
**Project focus:** Verifier-aligned preference optimization (DPO/ORPO)  
- Train the model to prefer verified witnesses over hallucinated ones.
- Measure calibration and abstention quality.  
**Management takeaway:** safety/alignment in practice—systems that avoid being confidently wrong.

### Knowledge Graphs / Networks (KG) — The Memory (Grounding)
**Project focus:** Modeling kernel dependencies for retrieval grounding  
- Represent dependencies (call/require/guard relationships).
- Use retrieval to provide minimal context slices.
- Optionally explore GNNs or graph-based retrieval heuristics.  
**Management takeaway:** retrieval systems and grounding strategies for reliable AI products.


## Coursework

### Completed
- ISYE 6501: Intro to Analytics Modeling
- CSE 6040: Computing for Data Analysis
- ISYE 6420: Bayesian Statistics
- CSE 6242: Data and Visual Analytics
- CS 6601: Artificial Intelligence

### Planned
- ISYE 6740: Computational Data Analytics
- CS 7643: Deep Learning
- CS 6742: Reinforcement Learning
- CSE 8803: Applied Natural Language Processing
- CS 7280: Network Science (Graph)

---

## Prerequisites and Foundations

### Prerequisites
- CS 1332: Data Structures and Algorithms
- CS 1331: Introduction to Object-Oriented Programming
- MATH 1554: Linear Algebra
- MATH 1712: Calculus
- ISYE 6739: Probability and Statistics

### Self-Study
- Harvard CS197: AI Research Experiences

### Books / References
- *Book of Proof*
- *Linear Algebra with Applications* (W. Keith Nicholson)
- *Mathematics for Machine Learning*
- *An Introduction to Statistical Learning*
- *The Elements of Statistical Learning*
- *Artificial Intelligence: A Modern Approach (4th ed.)*
















# OMSA
Master of Science in Analytics at Georgia Tech with the same faculty and curriculum for the online master's as the on-campus program. 
https://pe.gatech.edu/degrees/analytics 

Given 25 years of experience in the security industry, my goal in the OMSA program is to build a CTO-level understanding of the modern AI stack—how to apply AI/ML/LLMs to real security problems, and how to evaluate and mitigate the security risks introduced by these systems. 

## MS Thesis
**The Neuro-Symbolic Auditor**

Graph-Augmented Neuro-Symbolic Vulnerability Detection in Linux Kernels

- Have architected and developed a closed-loop security agent by integrating the several projects together (described in detail at a later section)
  -  Brain (Deep Learning Model) - Provide the Fine-Tuned LLM models (optimized with QLoRA and other techniques)
  -  Memory/database (Knowledge Management) - Leveraged a Knowledge Graph to solve the "Context" problem. Instead of pasting the whole file, Our system retrieves the dependency graph via RAG (Retrieval Augmented Generation).
  -  The Translator (Natural Language Processing) - Built the Data Pipeline that cleans unstructured C-code and tokenizes it correctly, handling the specific "dialects" of kernel code.
  -  The Judge (Reinforcement Learning): Implement the Verification Loop. The LLM proposes a bug, and the Static Analyzer (Coccinelle) rewards/punishes it. This proves you understand Safety & Alignment

## The PhD Extension: "The Autonomous Architect" 

Theme: Evolution & Discovery Goal: Move from "Checking known bugs" to "Discovering unknown bugs" and "Self-Healing."

**Title: Autonomous Evolution of Security Contracts via Adversarial Reinforcement Learning**
- The Leap beyond MS: From Static to Dynamic (The Mutation Engine): Instead of just training on GitHub history (Past), use an "Attacker Model" to generate new theoretical exploits (Future) and train your Defender against them.
- From Manual to Automatic (Contract Mining): Instead of hand-writing the "Balance Power" rule, use Unsupervised Learning (NLP) to read 10 years of git logs and infer the rules automatically ("I notice developers always call Y after X... that must be a rule").
- From Local to Global: Scale the Knowledge Graph from "One Driver" to the "Entire Kernel Ecosystem," tracking dependencies across subsystems.

## Projects

### Deep Learning (DL)
- Focus: The Engine (The Model itself).
- Project Title: "Fine-Tuning Large Language Models with QLoRA for Low-Resource Hardware."
- Goal: Focus on the training dynamics. Experiment with learning rates, rank (r=8 vs r=64), and loss curves.
- Management Takeaway: leart to estimate compute costs ("How many H100s do we need?") and debug training instability (Loss spikes).

#### Natural Language Processing (NLP)
- Focus: The Data (The Input/Output).
- Project Title: "Constructing a Privacy-Aware Chain-of-Thought Dataset from Unstructured Git Logs." Experiment with Tokenization (handling C macros), Prompt Engineering (System Prompts), and Data Augmentation (synthetic samples).
- Goal: Solve the "Context Window" problem. Compare how different tokenizers handle C code.
- Management Takeaway: leart that "Data is the moat." You'll understand why your team needs weeks for "Data Cleaning" before they can start training.

### Reinforcement Learning (RL)

- Focus: The Alignment (The Loop).
- Project Title: "Direct Preference Optimization (DPO) for Aligning LLMs with Static Analysis Verifiers." Used the Static Validator (Coccinelle) as the "Reward Function, and trained the model to prefer the "Verified" trace over the "Hallucinated" trace.
- Goal: Implement the "Feedback Loop."
- Management Takeaway: leart about Safety & Alignment. You'll understand how to stop your AI from being "confidently wrong" or generating malware.

### Knowledge Graphs (KG)

- Focus: The Memory (The Context).
- Project Title: "Modeling Linux Kernel Dependencies as a Neuro-Symbolic Knowledge Graph." Instead of feeding the LLM raw text, you feed it a Graph: (Driver A) --[calls]--> (Function B) --[requires]--> (Lock C). Use a Graph Neural Network (GNN) or RAG (Retrieval Augmented Generation) to retrieve the correct context for the LLM.
- Goal: Solve the "Hallucination" problem by grounding the AI in a structured graph of kernel APIs.
- Management Takeaway: learnt about Retrieval Systems (RAG).

## Classes completed
https://pe.gatech.edu/degrees/analytics 
- [ISYE 6501: Intro to Analytics Modeling](https://omscs.gatech.edu/isye-6501-intro-analytics-modeling)
- [CSE 6040: Computing for Data Analysis](https://cse6040.gatech.edu/active/)
- [ISYE 6420: Bayesian Statistics](https://omscs.gatech.edu/isye-6420-bayesian-statistics)
- [CSE 6242: Data and Visual Analytics](https://omscs.gatech.edu/cse-6242-data-and-visual-analytics)
- [CS 6601: Artifical Intelligence](https://omscs.gatech.edu/cs-6601-artificial-intelligence)
  
## Classes planned
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
