# OMSA
Master of Science in Analytics at Georgia Tech with the same faculty and curriculum for the online master's as the on-campus program. 
https://pe.gatech.edu/degrees/analytics 

## MS Thesis
**The Neuro-Symbolic Auditor**

Graph-Augmented Neuro-Symbolic Vulnerability Detection in Linux Kernels

- Have architected and developed a closed-loop security agent.
- Not just train a model, but built a Graph-RAG system that retrieves context, generates hypotheses using a fine-tuned 72B model, and validates them using formal methods. It achieved 95% precision on the test set, eliminating the hallucination problem common in standard LLMs.

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
