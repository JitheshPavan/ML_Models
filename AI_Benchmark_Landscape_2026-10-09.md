# AI benchmark landscape and opportunities for reasoning evaluations

Research snapshot: 9 October 2026. Prepared to support planning a new evaluation project with Marek Šuppa.

## Main conclusion

The most useful new evaluation would measure whether an AI system can reason reliably when the wording, language, evidence, constraints, or environment changes. Difficulty alone is an insufficient design goal. A useful benchmark must also have defensible answers, clear scoring, fresh held-out tasks, reproducible evaluation conditions, and a connection to a capability people actually need.

The landscape has three simultaneous characteristics:

- Several familiar academic tests offer little remaining separation at the frontier.
- More realistic workflows and some research problems still leave substantial room for improvement.
- The evaluation itself increasingly determines the result: tools, memory, inference budgets, grading, and dataset revisions can change scores substantially.

For this collaboration, my recommended starting point is **multilingual STEM reasoning under controlled changes, paired with an independently validated grading system**. This recommendation builds on Šuppa’s published experience; it is a proposed research direction, not a claim that the combination is already proven novel.

## Scope and how to read the numbers

This report covers language models, multimodal models, and software agents, with deeper coverage of reasoning. It is not a census of robotics, autonomous driving, medical devices, or every AI benchmark. The numerical evidence comes from benchmark owners, evaluation organizations reporting their own runs, and original research papers. No models were run for this report.

“Current” means inspected on 9 October 2026. A live leaderboard snapshot, a dated experiment, and a paper’s original baseline are different kinds of evidence and are labeled below. A score is only the best among the configurations evaluated by that source; it is not necessarily the global best result.

**Do not average the percentages in this report.** A 60% exam accuracy, a 60% workflow completion rate, and a 60% human-normalized action-efficiency score measure different things.

Useful terms:

| Term | Meaning |
|---|---|
| Benchmark | A standardized collection of tasks and scoring rules. |
| Evaluation or eval | The procedure used to assess a model or complete system; it may use a benchmark or custom tasks. |
| Harness | The surrounding software: prompts, tools, memory, retries, stopping rules, and environment. |
| Pass@1 | Expected success from one attempt per problem. Repeated runs can estimate it more precisely. |
| Pass@k | Whether at least one of several candidate attempts succeeds; this does not mean the system can identify the right answer unaided. |
| All-pass | A task receives credit only if every required criterion passes. |
| Saturation | Scores cluster near the ceiling, reducing a test’s ability to distinguish strong systems. This does not establish mastery of the entire underlying domain. |
| Contamination | Evaluation material or closely related solutions enter training, tuning, or other development processes. |

## Part one The general landscape

### A numerical snapshot

| Area and benchmark | Scale or evaluation unit | Verified numerical observation | What it tells us and its limits |
|---|---|---|---|
| Expert science — GPQA Diamond | 198 questions | Artificial Analysis lists GPT-6 Astra Xhigh at **96.3%**, Max at **96.1%**, and Gemini 3.8 Flash High at **95.3%** | Little headroom in this implementation. Scores from different effort settings are separate configurations. [GPQA results](https://artificialanalysis.ai/evaluations/gpqa-diamond) |
| Competition mathematics — AIME 2025 | 30 problems | Artificial Analysis displays **1.0**, equivalent to **100%**, for GPT-5.2 Xhigh, GPT-5 Codex High, and Gemini 3 Flash Preview Reasoning | A small, ceiling-limited test. One problem is 3.33 percentage points in a single complete run. [AIME results](https://artificialanalysis.ai/evaluations/aime-2025) |
| Broad expert questions — Humanity’s Last Exam | Artificial Analysis uses 2,158 text-only questions; full May 2025 revision has 2,500 | **61.4%** for Claude Opus 5.5 Max Default Fallback; **59.1%** for Fable 5.1 Max Default Fallback | Considerable remaining error on this text-only, no-tool implementation. It measures knowledge plus reasoning. [HLE results](https://artificialanalysis.ai/evaluations/humanitys-last-exam), [evaluation protocol](https://artificialanalysis.ai/methodology/intelligence-benchmarking) |
| Visual academic reasoning — MMMU-Pro | Multimodal academic questions | **88%** for Claude Opus 5.5 Max Default Fallback; **87%** for GPT-6 Astra Max | High performance on this test does not establish competence at acting in visual environments. [MMMU-Pro results](https://artificialanalysis.ai/evaluations/mmmu-pro) |
| Terminal agents — Terminal-Bench 4.0 | 66 tasks in this benchmark version | Opus 5.5 Max with Claude Code: **64.8% ± 3.1%**; GPT-6.1 Sol Max with Codex: **58.2% ± 3.1%** | These are model-plus-agent results. The displayed intervals are 95% confidence intervals. The associated listed run costs are about **$4,700** and **$600**, respectively; they are not per-task prices. [Owner leaderboard](https://www.tbench.ai/?version=4), [task count and protocol](https://artificialanalysis.ai/methodology/intelligence-benchmarking) |
| Professional document reasoning — GDP.pdf | 100 tasks, 10 domains, 4,592 pages, 1,275 criteria | Artificial Analysis currently lists **32.2%** all-pass for GPT-6 Astra Xhigh and **32.0%** for GPT-6.1 Sol High | Meeting all requirements is substantially harder than earning partial credit. Five attempts per task are graded criterion by criterion. [GDP.pdf results](https://artificialanalysis.ai/evaluations/gdp-pdf) |
| Long computer workflows — OSWorld 2.0 | 108 tasks; human median about 1.6 hours | Owner’s published study: Opus 4.8 maximum thinking with batched calls gets **20.6%** binary completion and **54.8%** partial score at 500 steps | A dated study result, not a verified October frontier ranking. Partial progress can greatly exceed completed work. [OSWorld 2.0](https://osworld-v2.xlang.ai/) |
| Research physics — CritPt | Artificial Analysis evaluates 70 challenges | **32.3%** for GPT-5.6 Sol Max; **31.7%** for Opus 5.5 Max Default Fallback | Evaluated without an agent harness. Authors are auditing the dataset after external feedback; scores may change with revisions. [CritPt results and caveat](https://artificialanalysis.ai/evaluations/critpt) |
| Novel interactive reasoning — ARC-AGI-3 | Semi-private interactive environments | Owner’s 3 September experiment: Astra Max scores **62.7%** under Standard and **98.6%** under Provider Adapter | The same effort setting differs by **35.9 percentage points** across harnesses. This is an efficiency-oriented benchmark, not ordinary exam accuracy. [ARC Prize experiment](https://arcprize.org/blog/astra) |
| Open mathematical research — FrontierMath Erdős | 68 formal conjectures | Initial September experiment: Astra resolves **2/68**, about **2.9%**; the other four evaluated models resolve none | This is a dated initial result. Each conjecture has a $300 and 72-hour limit, with a checked Lean proof or disproof required. [Initial results](https://epoch.ai/latest/announcing-frontiermath-erdos), [protocol](https://epoch.ai/benchmarks/frontiermath-erdos) |

The spread does not imply that one model is “96% intelligent” on one day and “3% intelligent” on another. Different tests sample different capabilities, demand different outputs, and allocate different resources.

### What each benchmark family is for

| Family | Examples | Best use | Main blind spot |
|---|---|---|---|
| Knowledge and academic exams | MMLU, MMLU-Pro, GPQA, HLE | Broad capability screening and subject-level diagnosis | Recall, cultural familiarity, and reasoning are entangled. |
| Mathematical problem solving | GSM8K, MATH, AIME, FrontierMath | Checkable multi-step quantitative work | Correct final answers can hide invalid reasoning; familiar problem styles may dominate. |
| Formal and controlled reasoning | Formal proofs, rule-based tasks, counterfactual tasks | Precise tests of consistency, deduction, and generalization | Artificial tasks may transfer poorly to real work. |
| Software engineering | SWE-bench, LiveCodeBench, Terminal-Bench | Executable correctness and tool-supported work | Test coverage, environment, data freshness, and harness choices matter. |
| Computer and workflow agents | OSWorld, professional task suites | End-to-end completion with changing state and multiple tools | Expensive evaluation; many causes of failure are mixed together. |
| Multimodal understanding | MMMU-Pro and visual reasoning suites | Combining text with diagrams and images | Perception mistakes and inference mistakes can be hard to separate. |
| Human preference | Arena-style comparisons | Which answer people prefer for a given prompt | Preference is not proof of correctness or reliability. |
| Trustworthiness | Factuality, calibration, safety, bias, and instruction-following suites | Whether capability is deployed reliably and appropriately | No single safety or trust score captures all relevant settings. |

This taxonomy is an analytical map, not a ranking. Some benchmarks belong to multiple families. For example, HLE combines domain knowledge and reasoning; Terminal-Bench combines programming, planning, tool use, and environment management.

SWE-bench illustrates why naming the version matters: the owner lists **2,294** original issues, **500** Verified issues, **300** Multilingual issues across **9 programming languages**, and **480** Multimodal issues. “Multilingual” there refers to programming languages. Its default Verified Bash Only view uses a shared mini-SWE-agent environment. [SWE-bench definitions](https://www.swebench.com/)

### What has changed recently

**Benchmark maintenance is becoming a core part of evaluation.** Artificial Analysis removed GPQA Diamond from its index in September 2026 because of saturation, and its v4.2 announcement raised private held-out weighting to **40%**. That is a dated design change, not a claim about the current index’s exact private weighting. Its current methodology identifies the index as **v4.3.2**, with ten evaluations and separate agent, coding, scientific reasoning, and general categories. [September announcement](https://artificialanalysis.ai/articles/artificial-analysis-intelligence-index-v4-2), [current methodology](https://artificialanalysis.ai/methodology/intelligence-benchmarking)

**A difficult test can also have defective questions.** FrontierMath’s owner reports that its June 2026 revision addressed errors in **42%** of problems. Version 2 has **338** problems: **295** in Tiers 1–3 and **43** in Tier 4, including public examples. The benchmark hub normally reports private-set evaluations. A secondary claim of a perfect Tier 4 score was encountered, but the primary page’s chart values were not retrievable in this review; it is therefore excluded from the verified score table. [FrontierMath v2 methodology and corrections](https://epoch.ai/benchmarks/frontiermath-tier-4-v2)

**The system surrounding the model matters.** In the matched Max-effort ARC experiment above, the listed evaluation cost fell from **$26,098** to **$17,332** while the score rose. The Provider Adapter preserves reasoning state and compacts conversations. These are aggregate evaluation costs, and this comparison does not isolate every component’s causal contribution. It demonstrates why memory and harness conditions must be reported. [ARC Prize experiment](https://arcprize.org/blog/astra)

**Success depends on how strictly completion is defined.** A document can satisfy most criteria and still omit a decisive exception. An agent can finish many steps and still fail to deliver the requested outcome. That is why partial credit and strict completion should be reported together. The GDP.pdf and OSWorld results above make this distinction concrete.

### What is needed now

The priorities below are my synthesis of the evidence, rather than a claim of field-wide consensus.

1. **Reliable completion under realistic conditions.** Include changing requirements, missing information, conflicting evidence, recovery after errors, and explicit checks that the final deliverable works.
2. **Transfer to genuinely new situations.** Hold out task families, rules, or combinations, rather than only different numbers within a known template.
3. **Verified scoring.** Audit reference answers, test suites, rubrics, and model judges. Publish corrections and an appeals process.
4. **Budget-aware evaluation.** Report quality as a function of money, latency, tokens, and tool calls. Maximum-score results should be a separate track from affordable deployment.
5. **Native multilingual and cultural coverage.** Translation alone does not guarantee equivalent difficulty or meaningful coverage.
6. **A maintenance plan.** Reserve resources for new tasks, reruns, environment repairs, access control, and versioned releases.

There is room for both practical and scientific benchmarks. A workplace test helps select usable systems. A tightly controlled reasoning test helps explain failure. Combining their scores into one number too early can obscure both purposes.

## Part two Targeted research on reasoning benchmarks

### What should reasoning mean in this project

A useful operational definition is: **using given information and learned rules to derive valid conclusions, choose informative actions, and revise a solution when relevant conditions change**.

This definition can be tested behaviorally. It does not require access to a model’s private internal reasoning, and a fluent explanation should not automatically count as evidence that the reasoning was sound.

Separate at least six capabilities:

| Capability | Diagnostic question | Example measurement |
|---|---|---|
| Deduction | Does the conclusion follow from the stated premises? | Verified validity; contradiction rate |
| Composition | Can individually mastered steps be combined? | Joint success conditional on component success |
| Abstraction and induction | Can the system infer and transfer a new rule? | Held-out rule-family success |
| Causal and counterfactual reasoning | Does an intervention change only the conclusions it should? | Correct response to changed assumptions |
| Planning and revision | Can the system execute and update a multi-step solution? | End-state success; recovery rate |
| Metacognition | Does the system recognize uncertainty or missing premises? | Calibration; useful clarification; correct abstention |

Language comprehension, perception, factual knowledge, and tool competence should be measured separately where possible. Otherwise, a low “reasoning” score may have a different cause.

### What the existing evidence establishes

**Academic reasoning is increasingly hard to measure with short familiar exams.** GPQA and AIME provide inexpensive reference points, but their current ceilings make them weak candidates for a new frontier benchmark. Keep a small anchor set if historical comparisons matter; focus new annotation on a capability those tests do not resolve.

**Harder academic questions still have value, with strong qualifications.** HLE remains useful for expert question answering, but comparisons must pin the subset, tools, grader, and revision. Its owner’s landing page still displays an older table led by Gemini 3 Pro at **38.3%**. That should not be treated as the October frontier result or directly subtracted from Artificial Analysis’s score. [HLE owner page](https://agi.safe.ai/)

The answer key also needs scrutiny. FutureHouse’s 2025 audit estimated that **29.3% ± 3.7%** of the inspected text-only biology/chemistry population had answers contradicted by research. This was an estimate for a particular subset and version, not an established error rate for all HLE questions or today’s revised data. [FutureHouse audit](https://www.futurehouse.org/research/hle-exam)

**Composition is a distinct research target.** AgentCoMa evaluates **61 models** and reports an average accuracy drop of nearly **30%** when commonsense and mathematics steps are combined, despite models often solving the steps separately. This is the paper’s reported drop, not a newly computed percentage-point estimate or a claim about every October model. [AgentCoMa, ACL 2026](https://aclanthology.org/2026.acl-long.380/)

**Correctness and explanation faithfulness are separate.** RFEval contains **7,186 instances** across **7 tasks**. Its study of **12 open-source reasoning models** reports unfaithfulness in **49.7%** of outputs under its intervention-based definition. That is evidence about the tested models and protocol, not a universal rate for all reasoning systems. Output interventions are useful probes, but do not directly reveal hidden internal computation. [RFEval, ICLR 2026](https://arxiv.org/abs/2602.17053)

**Controlled perturbations are an established method.** GSM-Symbolic’s 2024 experiments changed numerical values, complexity, and distractor clauses; the authors reported performance drops of up to **65%** in their tests. This motivates updated experiments, but cannot be reused as a claim about today’s frontier. Merely renaming variables or adding distractors would have limited novelty by itself. [GSM-Symbolic](https://machinelearning.apple.com/research/gsm-symbolic)

**Counterfactual reasoning is already an active benchmark area.** CounterBench contains **1.2K** counterfactual questions; WhatIfBench proposes **220** open-form, long-horizon what-if questions. The latter is a recent preprint. A new proposal needs a more specific contribution than “we test counterfactual reasoning.” [CounterBench, AAAI 2026](https://ojs.aaai.org/index.php/AAAI/article/view/40287), [WhatIfBench preprint](https://arxiv.org/abs/2608.27953)

**Research reasoning is moving toward verified new results.** FrontierMath Erdős requires a complete Lean proof or disproof. Its evaluation permits tools, offline mathematical literature, and an agent scaffold. Thus, the task tests a research system, not a model answering unaided. The initial 2/68 result leaves a large challenge, but creating such benchmarks requires substantial domain expertise and expensive evaluation. [Erdős methodology](https://epoch.ai/benchmarks/frontiermath-erdos)

**Long-horizon reasoning should be separated from interface execution.** METR defines a time horizon using how long a human expert takes on a task at a specified AI success probability. A “50% horizon” is not the length of time the model itself can run, and it is not dependable completion. METR also documented a modeling correction that reduced recent 50% horizon estimates by up to **20%**. [METR definition](https://metr.org/time-horizons/), [modeling correction](https://metr.org/notes/2026-03-20-impact-of-modelling-assumptions-on-time-horizon-results/)

### Why multilingual reasoning is a promising fit

Assuming your collaborator is the Marek Šuppa listed in these publications, there is relevant prior work to build on:

- **Trojsten**, co-authored with Adam Zahradník, contains **1,108** Slovak competition problems in mathematics, physics, and programming. The paper reports a best mean of **6.07/10** for GPT-4o. Its older model set is not a current baseline.
- In **36** manually translated mathematics problems, GPT-4’s mean was **3.30** for Slovak statements and **1.32** for English. This is a small experiment, not evidence that Slovak generally improves reasoning.
- The grading study reports **1.05 points** mean absolute disagreement under shared rubrics and average over-scoring of **0.9 points**. The abstract’s scale wording and the body’s solution-scoring presentation warrant clarification before reusing normalized results.

These findings make refreshed baselines, controlled language comparisons, and grader validation natural extensions. [Trojsten paper](https://aclanthology.org/2025.emnlp-main.1779.pdf)

Šuppa also co-authored **skLEP**, a **nine-task** Slovak language-understanding benchmark. This supports the relevance of multilingual evaluation to the collaboration. [skLEP](https://aclanthology.org/2025.findings-acl.1371.pdf)

The wider issue is not limited to Slovak. Global-MMLU covers **42 languages**; its authors identify culturally sensitive knowledge in **28%** of MMLU questions and report that **84.9%** of geography-dependent questions focus on North America or Europe. A new reasoning benchmark should distinguish language effects from cultural familiarity and translation artifacts. [Global-MMLU](https://arxiv.org/abs/2412.03304)

### Ranked project opportunities

The rankings are judgments about research value and practical fit. They are not measured market demand or a complete novelty assessment.

| Priority | Candidate | Existing work it must go beyond | Concrete contribution to test | Relative effort |
|---|---|---|---|---|
| 1 | Multilingual STEM reasoning under controlled changes | Trojsten, Global-MMLU, GSM-Symbolic, AgentCoMa | Native problems; paired language conditions; premise-changing variants; held-out compositions; validated grading | Medium |
| 2 | Evaluation of reasoning graders | Trojsten’s grading analysis, RFEval, benchmark answer-key audits | Can graders reject subtly invalid solutions while accepting valid alternative methods, across languages? | Medium |
| 3 | Belief revision in interactive reasoning | ARC-AGI-3, OSWorld 2.0, counterfactual benchmarks | Controlled updates to evidence with a known correct revised state; separate interface and inference tracks | Medium to high |
| 4 | Formalized open research problems | FrontierMath Erdős and other proof evaluations | New domains or proof tasks with reliable checking and defensible difficulty | Very high |

My preference is to combine priorities 1 and 2 in a focused pilot. They share data and expertise but answer different questions: whether models reason robustly, and whether the scoring system can tell.

Avoid launching a broad “general reasoning score” immediately. First establish a clear construct and show that its measurements remain meaningful after controlling for knowledge, language, grader behavior, and compute.

## A concrete proposed pilot

Everything in this section is a design proposal, not an observed result or funding quote.

### Research question

**When frontier models solve an original STEM problem, do they preserve correctness under equivalent wording and change their answer appropriately when a decisive premise changes, across languages and fixed evaluation budgets?**

A second question: **Can automatic graders detect these failures as reliably as qualified human graders?**

### Dataset design

Start with **120 original problem families**, provisionally 40 mathematics, 40 physics, and 40 algorithmic reasoning. Narrow to fewer domains if expert coverage is insufficient. Use Slovak and English initially, and add a third language only with qualified native authors and reviewers.

Each family has four conditions:

1. Original task.
2. Meaning-preserving rewording with altered names, ordering, or surface form.
3. A changed premise requiring a different valid solution or answer.
4. A deeper composition, or a deliberately under-specified version requiring a precise clarification or abstention.

With three languages, this is **120 × 4 × 3 = 1,440 instances**. It remains **120 related families**, not 1,440 independent samples. Include native authoring in each language where feasible; otherwise, explicitly report source language and translation direction.

As an illustrative split, release 30 families for development, reserve 60 for a private test, and keep 30 for later refreshes. Keep all language and variant versions of a family in the same split. Hold out some composition structures as well, because different wording of the same rule is a weaker generalization test.

### Experimental conditions

Evaluate **8 model configurations**, covering multiple providers, open-weight and closed models, and more than one cost tier. Pin exact model versions. Run **3 independent attempts** per instance in each of two tracks: no external tools and a standardized code/calculator environment.

Across the complete collection, the upper-bound count is **1,440 × 8 × 3 × 2 = 69,120 solution attempts**, before grader calls. A 20-family feasibility stage reduces that to **11,520** attempts under the same design. Use only the appropriate split for public leaderboard claims.

Freeze prompts, time limits, budgets, tool versions, sampling settings, and memory rules before the test. Report provider-native reasoning effort, but do not assume “high” means equal compute across providers. Measure actual tokens, spend, and wall-clock time.

Budget formula: **solution attempts × measured average attempt cost + judging + expert authoring/review + infrastructure**. As an illustrative sensitivity calculation, 69,120 attempts at $0.10, $1, or $5 each cost **$6,912**, **$69,120**, or **$345,600**, before the other costs. These are hypothetical unit costs, not current model price estimates.

### Scoring

| Metric | Definition and reason |
|---|---|
| Answer correctness | Exact checks, symbolic equivalence, or executable tests where possible; expert rubrics where necessary. |
| Justification validity | Whether the submitted solution’s necessary steps are valid; do not reward length or stylistic confidence. |
| Family robustness | Fraction of families solved correctly across all specified variants; also show each condition separately. |
| Invariance | Correctness preserved when meaning is unchanged. |
| Counterfactual responsiveness | Correctly revised answer when a relevant premise changes. |
| Composition gap | Joint-task performance compared with isolated component performance on matched items. |
| Language gap | Paired difference within the same task family, alongside results by native source language. |
| Appropriate uncertainty | Correct identification of missing information; error rate among answered tasks; coverage and confidence calibration. |
| Repeat reliability | Fraction of tasks correct on all three independent attempts, reported separately from mean pass@1. |
| Efficiency | Cost per successful attempt and success against budget and time. |

Use a dashboard of these metrics initially. A composite can be added only after its weights and intended decision use are justified.

### Validate the grader as a separate system

Construct an audited set of proposed solutions with known properties: correct concise answers, valid alternative methods, incorrect final answers with plausible prose, one-step logical errors, and correct answers with invalid arguments. Have at least two qualified reviewers label them independently and adjudicate disagreements.

Use executable or symbolic checks where they are genuinely sufficient. For open-ended reasoning, compare at least two grader families against the adjudicated human set. Measure false acceptance, false rejection, systematic score bias, and performance by language and difficulty. Test whether a solution’s verbosity or model identity changes its grade.

Publish the human rubric and audit protocol. Keep the final test’s solutions and sensitive grading details controlled. An LLM judge should not be the only authority used to validate both generated questions and their answers.

### Statistical discipline

Compute paired comparisons and confidence intervals at the **problem-family level**. Cluster resampling by family prevents language variants and repeated runs from creating false precision. Pre-register primary metrics; treat the rest as exploratory and account for multiple comparisons.

For intuition only, an independent binary accuracy estimate near 50% has an approximate 95% margin of error of **±9.8 percentage points at n=100** and **±4.9 points at n=400**, using 1.96 × sqrt[p(1−p)/n]. Repeated attempts do not create new independent task families. A small pilot can reveal large weaknesses but should not produce confident rankings from two-point differences.

Human comparison should match language, tool access, time, and subject expertise. Avoid a single “human baseline” assembled from experts choosing only their own specialties unless that selection is explicit.

### Evidence required before scaling

Proceed only if the pilot demonstrates that:

- Experts agree the tasks and variants measure the intended reasoning capability.
- The result is not explained mainly by ambiguous wording, translation mistakes, or missing factual knowledge.
- Automatic grading is accurate enough that the observed model differences exceed plausible grading error.
- Some tasks separate strong systems without pushing every model to near-zero or near-perfect performance.
- Results are reproducible, and at least some failures matter for a realistic use case.
- The measurement adds information beyond an existing benchmark or a simple combination of existing scores.

If language gaps disappear after better translation, that is a useful finding, but weak support for a new reasoning benchmark. If grader errors dominate, prioritize the grader project. If all models succeed, increase structural novelty or task depth; do not manufacture difficulty through ambiguity.

### Indicative eight-week sequence

| Period | Deliverable |
|---|---|
| Weeks 1–2 | Precise construct, related-work comparison, data rights and authoring protocol, 20 reviewed pilot families |
| Weeks 3–4 | Human solution audits, grader validation, reproducible runs on multiple model families |
| Weeks 5–6 | Analysis of confounds and uncertainty; expand toward 120 families only if justified |
| Weeks 7–8 | Frozen held-out evaluation, error analysis, benchmark card, public development set and reproducible runner |

This schedule assumes domain experts and evaluation infrastructure are available. Human review throughput is likely to constrain it more than data generation.

## Suggested agenda for the first meeting with Marek

1. Decide whether the main aim is a scientific paper, a practical model-selection tool, or a maintained public benchmark.
2. Review what can be reused from Trojsten and what requires permission, refresh, or new authoring.
3. Choose one primary construct: multilingual transfer, composition, premise revision, or grader reliability.
4. Agree on human expertise, languages, and acceptable evaluation spend.
5. Set a pilot success criterion and a reason to stop or redirect before a large dataset is built.

The strongest initial proposal is a **small, carefully audited extension that measures reasoning under change and validates its own scoring**. Its value should come from a trustworthy diagnosis of failure, backed by fresh evidence, rather than from a large question count alone.

## Source and uncertainty notes

All linked sources were inspected on 9 October 2026. Live pages may change. Scores should be refreshed before a paper submission, presentation, or model-selection decision.

- Leaderboards from Artificial Analysis are its own evaluation results, not necessarily the benchmark owner’s implementation. Its HLE result is text-only; GDP.pdf uses its own document delivery and judge. Those distinctions are material.
- Terminal-Bench owner results use the listed agents. Comparisons across model-plus-agent pairs do not isolate model quality.
- The ARC, OSWorld, Erdős, Trojsten, GSM-Symbolic, AgentCoMa, and RFEval study figures retain their original experimental scope. They are not all October frontier measurements.
- FrontierMath v1 and v2 should not be joined into an unqualified progress curve. Dataset repairs and changes in the evaluated private subset matter.
- Recent preprints are included to identify overlapping work, not to imply that their conclusions are settled.
- This is a targeted research review, not a systematic literature review with exhaustive inclusion and exclusion criteria. The proposed combination requires a further novelty check once its exact task design is fixed.
