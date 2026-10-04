# AI Reasoning Benchmarks: Explained with Examples

> Every benchmark category and proposed direction, illustrated with concrete problems so you can see exactly what's being measured.

---

## Part A: Existing Benchmark Categories (What They Test)

---

## 1. 🧩 Constraint Satisfaction & Formal Logic

**What it measures:** Can a model juggle multiple rules at once, eliminate possibilities, and find the one valid assignment — like solving a Sudoku?

---

### ZebraLogic — Logic Grid Puzzles

Think of those magazine logic puzzles where you use a grid to cross off impossibilities.

#### Example 1 (Easy — 3×3)
> There are 3 houses in a row (left, middle, right). Each has a different color (Red, Blue, Green) and a different pet (Cat, Dog, Fish).
>
> **Clues:**
> 1. The Red house is immediately to the left of the Blue house.
> 2. The Dog lives in the Green house.
> 3. The Fish does NOT live in the middle house.
>
> **Question:** Which pet lives in each house?

**How to solve it (what the model must do):**
- From Clue 1: Red-Blue must be adjacent → either (Left=Red, Middle=Blue) or (Middle=Red, Right=Blue).
- If Left=Red, Middle=Blue → Right=Green. Clue 2: Dog in Green = Right. Clue 3: Fish not in Middle → Fish in Left. Cat in Middle. ✅ Works.
- **Answer:** Left=Red/Fish, Middle=Blue/Cat, Right=Green/Dog.

#### Example 2 (Hard — 5×5, where models collapse)
> 5 houses, each with a different nationality, drink, color, pet, and cigarette brand. 14 interlocking clues.
>
> This is the classic "Einstein's Riddle." At this scale, models must track 5×5 = 25 variable assignments with ~14 constraints. One wrong elimination at step 3 corrupts everything downstream.

**Why models fail:** They can't systematically backtrack. They make a guess, follow it forward, hit a contradiction at step 12, but can't rewind to step 3 to try the other option.

---

### TruthQuest — Knights & Knaves (Suppositional Reasoning)

On an island, **Knights** always tell the truth, **Knaves** always lie. You must figure out who is who.

#### Example 1 (Easy — 2 characters)
> **Alice says:** "Both of us are Knaves."
>
> **Question:** What is Alice? What is Bob?

**How to solve it:**
- Suppose Alice is a Knight (truth-teller). Then her statement is true → both are Knaves. But Alice can't be both a Knight AND a Knave. ❌ Contradiction.
- So Alice must be a Knave (liar). Her statement "Both are Knaves" is false → at least one is a Knight → Bob must be a Knight. ✅
- **Answer:** Alice = Knave, Bob = Knight.

#### Example 2 (Hard — 4 characters, where models collapse)
> - **Alice says:** "Bob is a Knave."
> - **Bob says:** "Charlie and Diana are the same type."
> - **Charlie says:** "Alice and Bob are different types."
> - **Diana says:** "I am a Knight."
>
> **Question:** Classify all four.

**Why models fail:** You need to make a *temporary assumption* ("suppose Alice is a Knight..."), trace its downstream implications through all 4 characters, detect if it creates a contradiction, then try the alternative. With 4 characters, there are 16 possible truth assignments. Models can't systematically branch and prune.

---

### PlanBench — Formal Planning (With Semantic Obfuscation)

Given a starting state and a goal, produce a valid sequence of actions.

#### Example 1 (Standard Blocksworld)
> **Starting state:** Block A is on Block B. Block B is on the table. Block C is on the table.
> **Goal:** Block C is on Block A. Block A is on Block B.
>
> **Valid actions:** You can only pick up a block if it has nothing on top (it's "clear") and your hand is empty.

**Answer:**
```
1. Pick up C (C is clear, hand is empty)
2. Stack C on A (A is clear)
```

Models do well here (~80%) because they've seen thousands of Blocksworld problems in training.

#### Example 2 (Obfuscated "Mystery Blocksworld" — same problem, different words)
> **Starting state:** Object-7 is groofed on Object-3. Object-3 is plooked. Object-9 is plooked.
> **Goal:** Object-9 is groofed on Object-7. Object-7 is groofed on Object-3.
>
> **Rules:** You can only bliff an object if it is zorped (nothing is groofed on it) and your glorp is empty. You can frob Object-X onto Object-Y if you are glorping Object-X and Object-Y is zorped.

**This is the exact same problem** — just with made-up words. "groofed" = "on", "plooked" = "on table", "zorped" = "clear", "bliff" = "pick up", "frob" = "stack", "glorp" = "hand."

**Why models collapse (accuracy drops ~85%):** They were never truly reasoning about state transitions. They memorized that "pick up" + "stack" solves Blocksworld. With nonsense words, that memorization is useless.

---

## 2. ➕ Mathematical Reasoning & Symbolic Robustness

**What it measures:** Can a model actually *do math*, or does it just pattern-match problems it's seen before?

---

### GSM-Symbolic — Same Problem, Trivial Changes Break It

#### Example 1 (Standard GSM8K — models ace this)
> James buys 5 packs of gum. Each pack has 12 pieces. He eats 3 pieces per day. How many days will the gum last?
>
> **Answer:** 5 × 12 = 60 pieces. 60 ÷ 3 = **20 days.** ✅ Models get 98%+.

#### Example 2 (GSM-Symbolic variant — just change the numbers)
> James buys **7** packs of gum. Each pack has **17** pieces. He eats **4** pieces per day. How many days will the gum last?
>
> **Answer:** 7 × 17 = 119. 119 ÷ 4 = **29.75 days.** Models' accuracy fluctuates by 10-15% just from changing numbers. If the math was real, changing 5→7 shouldn't matter.

#### Example 3 (GSM-NoOp — add an irrelevant sentence)
> James buys 5 packs of gum. Each pack has 12 pieces. **His friend gives him 3 packs but he returns them.** He eats 3 pieces per day. How many days will the gum last?
>
> **Answer:** Still 20 days (the "gives and returns" is a no-op). But models see "3 packs" and start adding/subtracting, causing accuracy to **collapse by up to 65%.**

**What this proves:** Models don't understand math — they template-match. An irrelevant sentence shouldn't change anything, but it does.

---

### FrontierMath — Research-Level (What Real "Hard Math" Looks Like)

#### Example (Simplified illustration of the difficulty level)
> Let $p$ be a prime and $\zeta_p = e^{2\pi i/p}$. Determine the exact number of elements of order $p^2$ in the group $(\mathbb{Z}/p^3\mathbb{Z})^\times \rtimes \text{Gal}(\mathbb{Q}(\zeta_{p^2})/\mathbb{Q})$, where the action is the natural cyclotomic character.

This requires graduate-level algebra, Galois theory, and group cohomology. Even PhD mathematicians need hours. **Models score <2%.**

---

## 3. 🧭 Spatial & Physical Reasoning

**What it measures:** Can a model think in 3D space — rotate objects mentally, navigate environments, judge depth?

---

### SPACE — Spatial Cognition

#### Example 1 (Path Integration / Navigation)
> You are standing at point A, facing North.
> - Walk 3 steps forward (North).
> - Turn right 90°.
> - Walk 4 steps forward (now East).
> - Turn right 90°.
> - Walk 1 step forward (now South).
>
> **Question:** What direction would you need to face to walk straight back to point A? How far away is it?

**Answer:** You're at position (4, 2) relative to start. Direction back ≈ Southwest. Distance = $\sqrt{4^2 + 2^2} \approx 4.47$ steps.

**Why models fail (15–35%, near random):** They can't maintain an internal coordinate system. After 3-4 turns, they lose track of which direction they're facing and where they are.

#### Example 2 (Mental Rotation — Shepard-Metzler style)
> You're shown two 3D block structures made of connected cubes. One might be a rotated version of the other, or a mirror image.
>
> **Question:** Are these two shapes the same object rotated, or are they different (mirror images)?

**Why models fail:** This requires mentally rotating a 3D object in your head. Humans do this in ~1–3 seconds. VLMs literally cannot do this and score at chance (~50% on same/different).

---

### BLINK — Instant Visual Perception

#### Example 1 (Relative Depth)
> You see a photo of a room. Two red dots are marked on the image — one on a chair, one on a table.
>
> **Question:** Which dot is closer to the camera?

Humans answer this "in a blink" (<1 second). VLMs score ~45-55% — barely above coin flip. They can label "chair" and "table" but can't reason about their spatial positions.

#### Example 2 (Multi-View Consistency)
> You see two photos of the same room taken from different angles.
>
> **Question:** Is the vase in Photo B to the left or right of the lamp, from that new camera angle?

**Why models fail:** They need to mentally map 3D positions across viewpoints. They recognize individual objects but can't build a coherent 3D model of the scene.

---

## 4. 🔗 Causal & Counterfactual Reasoning

**What it measures:** Can a model understand *why* things happen (not just *that* they happen together) — and reason about "what would have happened if..."?

---

### CounterBench — Structural Causal Counterfactuals

#### Example 1 (Basic Counterfactual)
> **Causal Model:** Rain → Sprinkler OFF, Rain → Wet Grass, Sprinkler → Wet Grass.
> **Observation:** It rained, the sprinkler was off, the grass was wet.
>
> **Question:** If it had NOT rained, would the grass still be wet?

**How to solve (the 3 steps models must do):**
1. **Abduction:** Given it rained and sprinkler was off, infer there's no other hidden cause of wet grass (no leaky pipe, etc.).
2. **Intervention:** Set Rain = No. Under the causal rules: no rain → sprinkler turns ON (maybe), but we need to check the model rules.
3. **Prediction:** With Rain=No and Sprinkler behavior re-computed → determine grass wetness.

Models often skip step 1 entirely and just say "no rain = no wet" — ignoring the sprinkler path.

#### Example 2 (Nested Counterfactual — where models score near random)
> **Causal Model:** $X \to Y \to Z$, with $Y = 2X + U_Y$ and $Z = Y^2 + U_Z$.
> **Observation:** $X=1$, $Y=5$, $Z=28$.
>
> **Question:** If $X$ had been $3$, what would $Z$ have been?

**How to solve:**
1. **Abduction:** From $Y = 2X + U_Y$ → $5 = 2(1) + U_Y$ → $U_Y = 3$. From $Z = Y^2 + U_Z$ → $28 = 25 + U_Z$ → $U_Z = 3$.
2. **Intervention:** Set $X = 3$. Recompute: $Y = 2(3) + 3 = 9$. Then $Z = 81 + 3 = 84$.
3. **Answer: $Z = 84$.**

**Why models fail:** They need to *first* recover the hidden noise variables ($U_Y$, $U_Z$) from the observations, *then* re-run the model. Most models skip the abduction step and just plug in $X=3$ into a generic formula.

---

### ExpliCa — Causality vs. "This Happened After That"

#### Example 1
> "The rooster crowed and then the sun rose."
>
> **Question:** Did the rooster's crowing CAUSE the sun to rise?

**Answer:** No — this is temporal sequence, not causation. But models frequently say "yes" because "A then B" patterns trigger causal associations.

#### Example 2
> "The patient took the medication. Her fever went down the next day."
>
> **Question:** Did the medication cause the fever to go down?

**Answer:** Can't be determined from this alone — it could be natural recovery, other treatments, etc. But models tend to say "yes" because the narrative implies it.

**What this measures:** The *post hoc ergo propter hoc* fallacy — "after it, therefore because of it." Models systematically confuse temporal order with causation.

---

## 5. ⚙️ Procedural & State-Tracking Reasoning

**What it measures:** Can a model *execute* a procedure step by step and track the changing state of the world — like being a human computer?

---

### CRUXEval — Mental Code Execution

#### Example 1 (Output Prediction — forward execution)
```python
def f(x):
    result = []
    for i in range(len(x)):
        if x[i] % 2 == 0:
            result.append(x[i] * 2)
        else:
            result.append(x[i] + 1)
    return result
```
> **Input:** `f([3, 4, 7, 2])`
> **Question:** What does this function return?

**How to solve (the model must trace each iteration):**
- `i=0`: `x[0]=3`, odd → `3+1=4`, result = `[4]`
- `i=1`: `x[1]=4`, even → `4*2=8`, result = `[4, 8]`
- `i=2`: `x[2]=7`, odd → `7+1=8`, result = `[4, 8, 8]`
- `i=3`: `x[3]=2`, even → `2*2=4`, result = `[4, 8, 8, 4]`
- **Answer:** `[4, 8, 8, 4]`

#### Example 2 (Input Prediction — backward execution, much harder)
```python
def g(s):
    return s[::2] + s[1::2]
```
> **Output:** `g(???) = "acbd"`
> **Question:** What input produces this output?

**How to solve:** `s[::2]` takes characters at indices 0,2,4... and `s[1::2]` takes 1,3,5... For output `"acbd"`: if `s` has 4 chars, then `s[::2]` = first 2 chars = `"ac"`, `s[1::2]` = last 2 chars = `"bd"`. So `s[0]='a'`, `s[2]='c'`, `s[1]='b'`, `s[3]='d'` → **`s = "abcd"`**.

**Why models fail on Input Prediction:** Forward execution is like following a recipe. Backward execution is like tasting a cake and figuring out the recipe — you need to *invert* the logic.

---

### MuSR — Multi-Step Soft Reasoning in Stories

#### Example (Murder Mystery)
> A 1,000-word story describes a dinner party. Five guests had means, motive, and opportunity. The story contains scattered clues:
> - Alice left the room at 8:15 PM (paragraph 2).
> - The victim was seen alive at 8:30 PM (paragraph 5).
> - Bob was on a phone call from 8:00–9:00 PM confirmed by phone records (paragraph 4).
> - A witness saw someone in a red jacket near the scene at 8:45 PM (paragraph 7).
> - Charlie was wearing a red jacket that evening (paragraph 1).
> - Diana's fingerprints were on the weapon, but she's the housekeeper who cleaned it earlier (paragraph 6).
>
> **Question:** Who is the most likely suspect?

**How to solve:** Must gather scattered clues, eliminate suspects with alibis (Alice left too early, Bob was on the phone), handle red herrings (Diana's prints are explained), and synthesize remaining evidence (Charlie in red jacket near the scene at the right time).

**Why models fail (60-72% vs. ~90% human):** They can't reliably track entity states across a long narrative. They forget that Alice left before the murder, or fail to connect the red jacket mention in paragraph 1 with the witness statement in paragraph 7.

---

## 6. 🔀 Compositional & Analogical Reasoning

**What it measures:** Can a model combine multiple reasoning skills at once, or see deep structural similarities between very different situations?

---

### AnaloBench — Cross-Domain Analogies

#### Example 1
> **Story A:** A small startup has a brilliant core product but limited distribution. A large corporation acquires them, providing massive distribution channels. Initially the product thrives, but gradually the corporation's bureaucratic processes slow down innovation, and the product stagnates.
>
> **Story B:** A rare medicinal plant grows only in a small forest. Scientists transplant it to large commercial greenhouses. Initially it grows abundantly, but it loses certain chemical compounds that only develop under the stress of its original wild environment.
>
> **Question:** Are these stories analogous?

**Answer:** Yes — both share the structure: *small entity with unique quality* → *transplanted to large, resource-rich environment* → *initial success* → *loss of the original quality that required the constrained environment*.

**Why models fail:** They look for surface-level keyword overlap (business terms vs. biology terms share zero words). The analogy is purely *structural* — same abstract pattern, completely different domains. Models score ~55% vs. humans at ~88%.

#### Example 2 (Distractor trap)
> **Story A:** (same startup story as above)
>
> **Story C:** A large corporation acquires a struggling startup to absorb their engineering team. The engineers are given bigger salaries and better offices, and they produce even more innovative work.
>
> **Question:** Are A and C analogous?

**Answer:** No — despite sharing all the same *surface keywords* (startup, corporation, acquisition), the structural outcome is opposite (stagnation vs. thriving). Models are easily fooled by keyword overlap.

---

### AgentCoMa — The Compositionality Gap

#### Example 1 (Math alone — models ace this)
> You have a \$50 budget. Item A costs \$12, Item B costs \$8, Item C costs \$15. What's the maximum number of items you can buy?
>
> **Answer:** All three cost \$35 total, so **3 items.** Models get ~90%.

#### Example 2 (Commonsense alone — models ace this)
> You're planning a picnic. It's going to rain. Should you bring an umbrella or sunscreen?
>
> **Answer:** Umbrella. Models get ~92%.

#### Example 3 (Math + Commonsense combined — models choke)
> You're planning a picnic for 6 people with a \$50 budget. The weather forecast says rain until 2 PM then sunny. You need: food (sandwiches \$3/person or pizza \$5/person), drinks (\$2/person), and weather gear (umbrellas \$8 each, sunscreen \$5 each, each umbrella covers 2 people). You want to minimize cost while ensuring everyone is comfortable for the full event 12 PM–5 PM.
>
> **Question:** What should you buy?

**The model must simultaneously:** do budget math, reason about weather transitions (need umbrellas for 12–2 PM AND sunscreen for 2–5 PM), optimize covering constraints (3 umbrellas for 6 people), and ensure food sufficiency.

**Why models fail (~30% accuracy drop):** Each skill alone is easy (~90%). But combining budget optimization + temporal weather reasoning + coverage constraints causes interference. Models that tracked the budget forget the weather, or vice versa.

---

### RECON — Cascading Evidence Invalidation

#### Example
> A 50,000-token legal case file unfolds chronologically:
>
> - Page 5: Fingerprints on the weapon match Suspect A. → You conclude A is likely guilty.
> - Page 12: DNA evidence also implicates A. → Confidence in A grows.
> - Page 30: The fingerprint lab is found to be contaminated — all fingerprint evidence from that lab is invalidated. → You must now *retract* the Page 5 conclusion.
> - Page 31: But the DNA evidence (from a different lab) still holds. → A remains implicated, but with weaker support.
> - Page 45: An alibi witness for A is verified. → A is now less likely.
>
> **Question:** Given everything through page 45, what's the current status of Suspect A?

**What the model must track:** A dynamic "belief graph" that updates as new evidence arrives AND retracts old conclusions when underlying evidence is invalidated. It's not just retrieval — it's *maintaining logical consistency across an evolving evidence chain*.

**Why models fail (~22% accuracy):** Expanding context windows doesn't help. Models retrieve individual facts but can't propagate the invalidation of the fingerprint evidence forward to update all downstream conclusions.

---

## 7. ♟️ Strategic & Game-Theoretic Reasoning

**What it measures:** Can a model reason under *uncertainty* about what other agents know and will do?

---

### PokerBench

#### Example
> **Texas Hold'em situation:**
> Your hand: K♠ Q♠. Community cards: J♠ 10♠ 2♥ (flop).
> Pot: \$200. Opponent bets \$100.
> You have a straight (K-Q-J-10) AND a flush draw (four spades).
>
> **Question:** What is the optimal action — call, raise, or fold? By how much should you raise?

**What the model must compute:**
1. **Hand strength:** You have an open-ended straight + flush draw = very strong.
2. **Pot odds:** Need to call \$100 into a \$300 pot = 3:1.
3. **Implied odds:** Future streets could make your flush.
4. **Opponent modeling:** What hands would they bet here? Can you extract more value by raising?

**Why models fail:** They hallucinate pot odds, can't do Bayesian updates on opponent hand ranges, and play predictable, exploitable strategies instead of game-theory optimal (GTO) play.

---

---

## Part B: Proposed New Benchmark Directions (Explained with Examples)

---

## Direction 1: 🔙 BacktrackBench — "Can It Recognize a Dead End?"

**The core question:** When a model's initial guess leads to a contradiction, can it identify *which* assumption was wrong, retract it, and try a different path?

#### Example 1
> There are 4 people (A, B, C, D) and 4 jobs (Teacher, Doctor, Artist, Chef).
>
> **Clues:**
> 1. A is not the Teacher.
> 2. B is not the Doctor.
> 3. If A is the Doctor, then C is the Chef.
> 4. If C is the Chef, then D is the Teacher.
> 5. D is not the Teacher.
>
> **The trap:** A natural first guess is "A is the Doctor" (it's not eliminated by clue 1). But follow the chain: A=Doctor → C=Chef (clue 3) → D=Teacher (clue 4) → ❌ Contradiction with clue 5!
>
> **What the model must do:**
> 1. ✅ Detect the contradiction at clue 5.
> 2. ✅ Identify that the root cause is the assumption "A is the Doctor" at step 1.
> 3. ✅ Retract that assumption.
> 4. ✅ Try A = something else and find the valid assignment.

**Grading:** The model gets points for each of these 4 steps, not just the final answer. This tells us *where* in the reasoning process the model breaks down.

#### Example 2
> A 6×6 constraint grid where the first 5 assignments are forced by clue interactions. At step 6, only a wrong assignment appears locally consistent, but it creates a contradiction that doesn't surface until step 12 (6 steps later).
>
> **The model must:** realize the contradiction at step 12, trace it back to step 6 (not step 11 or 10), undo step 6, and find the correct alternative.

**Why this is new:** ZebraLogic tells us models *fail* at complex CSPs. BacktrackBench tells us *why* — is it failure to detect contradictions? failure to identify the root cause? failure to retract? or failure to explore alternatives?

---

## Direction 2: 💻 ByteTrace — "Can It Be the Computer?"

**The core question:** Without writing or running any code, can the model mentally execute a tiny program and track every register's value at every step?

#### Example 1
> **Micro-VM with 4 registers (R0–R3), all starting at 0.**
>
> ```
> Step 1: LOAD R0, 5       (put 5 into R0)
> Step 2: LOAD R1, 3       (put 3 into R1)
> Step 3: ADD  R2, R0, R1  (R2 = R0 + R1)
> Step 4: SUB  R3, R0, R1  (R3 = R0 - R1)
> Step 5: MUL  R0, R2, R3  (R0 = R2 × R3)
> Step 6: HALT
> ```
>
> **Question:** What are the values of all registers after each step?

**Expected answer (graded per step):**

| Step | R0 | R1 | R2 | R3 |
|:---|:---|:---|:---|:---|
| After Step 1 | **5** | 0 | 0 | 0 |
| After Step 2 | 5 | **3** | 0 | 0 |
| After Step 3 | 5 | 3 | **8** | 0 |
| After Step 4 | 5 | 3 | 8 | **2** |
| After Step 5 | **16** | 3 | 8 | 2 |

#### Example 2 (With conditional jump — much harder)
> ```
> Step 1: LOAD R0, 10
> Step 2: LOAD R1, 1
> Step 3: SUB  R0, R0, R1    (R0 = R0 - 1)
> Step 4: CMP  R0, 0         (compare R0 to 0)
> Step 5: JNZ  Step 3         (if R0 ≠ 0, jump back to Step 3)
> Step 6: HALT
> ```
>
> **Question:** How many times does Step 3 execute? What is R0 at HALT?

**Answer:** Step 3 executes 10 times (R0 counts down from 10 to 0). R0 = 0 at HALT.

**Why models fail:** This requires maintaining an exact numerical state across a loop. One arithmetic error at iteration 4 corrupts all subsequent states. And the model can't "run" the code — it must *be* the CPU, step by step, in its head.

---

## Direction 3: 🔬 AbductionBench — "Can It Recover Hidden Causes?"

**The core question:** Given a causal graph with hidden variables, can the model infer the hidden values from observations, then predict what would happen under a hypothetical change?

#### Example 1
> **Causal graph** (anonymized — no real-world semantics):
> ```
> U₁ (hidden) → A
> U₂ (hidden) → B
> A → C       (C = A + B)
> B → C
> ```
> **Equations:** $A = U_1$, $B = U_2$, $C = A + B$
>
> **Observed:** $A = 4$, $B = 7$, $C = 11$
>
> **Question:** If $A$ had been $10$, what would $C$ have been?

**Step-by-step solution:**
1. **Abduction:** From $A = U_1 = 4$ → $U_1 = 4$. From $B = U_2 = 7$ → $U_2 = 7$.
2. **Intervention:** Set $A = 10$. $B$ is not affected by $A$ (they have separate causes), so $B$ stays $= U_2 = 7$.
3. **Prediction:** $C = 10 + 7 = \mathbf{17}$.

#### Example 2 (With a confounded path — the trap)
> ```
> U₁ (hidden) → A and B    (A and B share a hidden common cause!)
> A → C
> B → C
> ```
> **Equations:** $A = U_1$, $B = 2 \cdot U_1$, $C = A + B$
>
> **Observed:** $A = 3$, $B = 6$, $C = 9$
>
> **Question:** If $A$ had been $5$, what would $C$ have been?

**The trap:** A naïve model might think "just change A to 5, keep B at 6, so C = 11." **WRONG.**

**Correct solution:**
1. **Abduction:** $A = U_1 = 3$, $B = 2 \times U_1 = 6$. So $U_1 = 3$. ✅
2. **Intervention:** We *set* $A = 5$ (overriding its equation). But $B = 2 \times U_1$ and $U_1$ was inferred as $3$, so **$B$ stays at $6$** (the intervention on $A$ doesn't flow backward through the hidden cause to change $B$).
3. **Prediction:** $C = 5 + 6 = \mathbf{11}$.

Wait — but shouldn't we consider that if we're *intervening* on A (cutting it from $U_1$), then $U_1$ remains 3 and $B$ remains $2 \times 3 = 6$? Yes, exactly. This is the `do`-calculus: $\text{do}(A=5)$ severs $A$'s incoming edges, but $U_1$ doesn't change, so $B$ doesn't change.

**Why models fail:** They don't understand the difference between *observing* A=5 (which would imply $U_1=5$, changing $B$ to 10) versus *intervening* to set A=5 (which severs $A$ from $U_1$, keeping $B$ unchanged).

---

## Direction 4: ⏱️ ThinkBudget — "Does It Know When to Stop Thinking?"

**The core question:** Given problems of varying difficulty, does the model allocate proportional reasoning effort — or does it overthink trivial problems and underthink hard ones?

#### Example 1 (Trivial — should take ~10 tokens of reasoning)
> What is 7 + 13?
>
> **Optimal answer:** "20." (~5 tokens)
>
> **Penalized behavior:** A model that generates 500 tokens of chain-of-thought for this ("Let me think step by step. First, 7 + 13. I know that 7 + 10 = 17, and 17 + 3 = 20. Let me verify: 20 - 13 = 7. Yes, confirmed.") gets penalized for **overthinking.**

#### Example 2 (Hard — should take ~200–500 tokens)
> Prove that for all primes $p > 3$, the number $p^2 - 1$ is divisible by 24.
>
> **Optimal answer:** Requires a non-trivial proof (~200 tokens): $p^2 - 1 = (p-1)(p+1)$. Since $p$ is odd, both $p-1$ and $p+1$ are even, and they're consecutive even numbers, so one is divisible by 4. Thus $8 | (p^2-1)$. Since $p > 3$ and $p$ is prime, $p \not\equiv 0 \pmod{3}$, so either $p-1$ or $p+1$ is divisible by 3. Thus $24 | (p^2-1)$.
>
> **Penalized behavior:** A model that says "Yes, it's always divisible by 24" without proof → **underthinking.** A model that writes 3,000 tokens exploring multiple wrong approaches before finding this → **overthinking.**

**Scoring formula:** $\text{Score} = \text{Accuracy} \times \frac{\text{Optimal Tokens}}{\max(\text{Used Tokens}, \text{Optimal Tokens})}$

This means: getting the right answer with excessive tokens is penalized. Getting the wrong answer is penalized more. Getting the right answer efficiently is rewarded.

---

## Direction 5: ⚙️ KinemaBench — "Can It Trace Motion Through Machines?"

**The core question:** Given a chain of connected mechanical components, can the model trace how motion propagates through the system?

#### Example 1 (Simple gear train)
> ```
> Gear A (20 teeth) ──meshes with──▶ Gear B (40 teeth) ──meshes with──▶ Gear C (10 teeth)
> ```
> Gear A rotates clockwise at 100 RPM.
>
> **Questions:**
> 1. What direction does Gear B rotate? → **Counter-clockwise** (meshing gears reverse direction)
> 2. What speed? → **50 RPM** (20/40 ratio = 1/2)
> 3. What direction does Gear C rotate? → **Clockwise** (reverses again)
> 4. What speed? → **200 RPM** (40/10 ratio = 4, applied to 50 RPM)

#### Example 2 (Perturbation query — "what if?")
> Same gear train, but now: **What happens if Gear B's axle is welded to the frame (locked)?**
>
> **Answer:** The entire mechanism jams. Gear A cannot rotate because it meshes with a locked Gear B. Gear C also cannot rotate. Degrees of freedom = 0.

#### Example 3 (Pulley system)
> A compound pulley with 4 pulleys. A rope threads through them. You pull one end with 10 N of force.
>
> **Questions:**
> 1. How much weight can this system lift? → Depends on the mechanical advantage (number of rope segments supporting the load).
> 2. If you pull 4 meters of rope, how far does the weight rise? → Conservation of energy: if MA = 4, then the weight rises 1 meter.

**Why models fail:** They can't trace force/motion through a chain of components. They may know "gears reverse direction" as a fact, but can't apply it through a 5-stage chain with varying gear ratios, or handle "what-if" removal of a component.

---

## Direction 6: 📅 IntervalSAT — "Can These Events All Fit?"

**The core question:** Given a set of events with duration and relationship constraints, can the model determine if a valid schedule exists?

#### Example 1 (Satisfiable)
> - Meeting A lasts 1 hour.
> - Meeting B lasts 30 minutes.
> - Meeting C lasts 45 minutes.
> - A must finish before B starts.
> - B must overlap with C (they share at least some time).
> - Available window: 9:00 AM – 12:00 PM.
>
> **Question:** Can all three meetings be scheduled? If so, give a valid schedule.

**Answer:** Yes. A = 9:00–10:00, B = 10:00–10:30, C = 10:00–10:45. B and C overlap during 10:00–10:30. ✅

#### Example 2 (Unsatisfiable — the model must recognize this)
> - Meeting A lasts 2 hours.
> - Meeting B lasts 2 hours.
> - Meeting C lasts 2 hours.
> - A must finish before B starts.
> - B must finish before C starts.
> - Available window: 9:00 AM – 2:00 PM (5 hours).
>
> **Question:** Can all three be scheduled?

**Answer:** No. Sequential A→B→C requires 6 hours minimum, but the window is only 5 hours. ❌

#### Example 3 (Tricky — partial information)
> - Event A happens sometime between 1 PM and 4 PM (duration unknown, at least 30 min).
> - Event B lasts exactly 1 hour and must be *during* A (completely contained within A).
> - Event C lasts 45 minutes and must *meet* A (C ends exactly when A starts, or A ends exactly when C starts).
>
> **Question:** What is the minimum possible duration of A?

**Answer:** A must contain B (1 hour), so A ≥ 1 hour. C just needs to touch A's boundary, so C doesn't constrain A's duration. Minimum A = **1 hour.**

**Why models fail:** Allen's Interval Algebra has 13 relations (before, meets, overlaps, starts, during, finishes, equals, and their inverses). Composing these across 5+ events requires systematic constraint propagation — exactly what LLMs lack.

---

## Direction 7: 🧬 ComposeStress — "How Many Skills Can It Juggle?"

**The core question:** If a model can handle skill A alone and skill B alone, what happens when it must use both at once? What about A + B + C?

#### Example (3-skill intersection: Math + Spatial + Temporal)
> You are at position (0,0) on a grid at 9:00 AM. You walk at 1 block/minute.
>
> - At 9:00 AM, walk to the bakery at (3, 0). You arrive at 9:03 AM. Buy 6 croissants at \$2.50 each.
> - At 9:03 AM, walk to the florist at (3, 4). You arrive at 9:07 AM. Buy a bouquet for \$15.
> - You must reach the café at (0, 4) by 9:15 AM.
>
> **Questions:**
> 1. *(Spatial)* What is the shortest path distance from the florist to the café? → 3 blocks.
> 2. *(Temporal)* Do you have enough time to get there? → You arrive at florist at 9:07, café is 3 min away → arrive 9:10 AM. Yes ✅.
> 3. *(Math)* What is your total spend? → (6 × \$2.50) + \$15 = \$30.
> 4. *(Combined)* If you also need to stop at the bank at (1, 4) for 3 minutes on the way to the café, can you still make it by 9:15 AM? → Florist(3,4) → Bank(1,4) = 2 min walk + 3 min stop = 5 min. Bank(1,4) → Café(0,4) = 1 min. Total = 9:07 + 8 min = 9:15 AM. **Just barely.** ✅

**The measurement:** Run the same model on: (a) pure spatial problems, (b) pure temporal problems, (c) pure math problems, then (d) the combined problem. Measure how much accuracy drops from (a,b,c) to (d). That drop is the "Compositionality Tax."

---

## Direction 8: 🌀 BranchWorld — "What If the Rules Were Different?"

**The core question:** If we change a fundamental assumption about how the world works, can the model trace all the downstream consequences?

#### Example 1 (Simple counterfactual physics)
> **Modified rule:** Water freezes at 50°C instead of 0°C.
>
> **Question:** You leave a glass of water (at room temperature, 25°C) outside on a summer day (35°C). What happens to the water?
>
> **Answer:** It stays liquid — 25°C and 35°C are both below the new freezing point of 50°C, so the water would actually be frozen at these temperatures in this world! Wait — *freezing* at 50°C means anything below 50°C is solid. So at 25°C, the water would be **ice**. It would stay frozen because 35°C < 50°C.
>
> **Common model error:** Models default to real-world knowledge ("25°C water stays liquid") and ignore the modified rule.

#### Example 2 (Legal hypothetical)
> **Existing law:** Contracts require signatures from both parties to be valid.
> **Modified rule:** Contracts are valid if EITHER party signs (unilateral signature suffices).
>
> **Scenario:** Alice drafts a contract to buy Bob's car for \$1,000. Alice signs it. Bob doesn't sign but also doesn't object.
>
> **Question:** Is the contract valid? What are Bob's obligations?
>
> **Answer:** Under the modified rule, yes — Alice's signature alone makes it valid. Bob is obligated to sell his car for \$1,000 even though he never signed.
>
> **Common model error:** Models fall back on real-world contract law and say the contract is invalid without both signatures.

#### Example 3 (Cascading consequences)
> **Modified rule:** In this world, humans can photosynthesize (derive energy from sunlight like plants).
>
> **Question:** How would this affect the restaurant industry?
>
> **Answer:** This requires tracing a cascade:
> 1. Humans need less food → demand for meals drops.
> 2. Restaurants lose customers on sunny days (people "eat" sunlight instead).
> 3. Restaurants might shift to operate mainly at night or in winter.
> 4. Indoor restaurants might advertise "shade dining" as a feature that keeps you hungry enough to eat.
> 5. The agriculture industry shrinks dramatically.
>
> **Why models fail:** They either ignore the modified rule entirely, or apply it too literally without tracing second/third-order consequences.

---

## Summary: What Each Benchmark Direction Really Boils Down To

| Direction | One-Line Description | The Key Question |
|:---|:---|:---|
| **BacktrackBench** | Forced dead-end puzzles | "Can it undo a mistake?" |
| **ByteTrace** | No-code CPU simulation | "Can it *be* the computer?" |
| **AbductionBench** | Hidden-cause inference | "Can it find what's unseen?" |
| **ThinkBudget** | Reasoning efficiency meter | "Does it know when to stop?" |
| **KinemaBench** | Mechanical chain tracing | "Can it follow motion through gears?" |
| **IntervalSAT** | Schedule constraint puzzles | "Can these events all fit?" |
| **ComposeStress** | Multi-skill intersection | "How many skills can it juggle?" |
| **BranchWorld** | Rule-change consequences | "What if the rules were different?" |
