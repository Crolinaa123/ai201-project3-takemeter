# TakeMeter — r/television Discourse Classifier

A fine-tuned DistilBERT text classifier that categorizes r/television posts and comments by discourse type: `analysis`, `hot_take`, `reaction`, and `discussion`.

---

## Community

**r/television** is a general TV discussion subreddit with active, text-heavy discourse. The same show generates emotional reactions from casual viewers, bold unsupported takes from fans, structured critiques from serious watchers, and open-ended prompts from people seeking engagement. This variety makes the classification task meaningful — the labels map onto real distinctions that regulars in the community would recognize.

---

## Labels

### `analysis`
A structured argument backed by specific, verifiable references — episode names, characters, production decisions, or named comparisons. Evidence must be traceable, not just general impressions.

> *Example 1:* "The second season is much better because they made it storyboard driven instead of writing scripts — one of the few cartoons that lets animators make a cartoon." (Cites a specific production decision as the reason for quality improvement.)

> *Example 2:* "Peeves was cast for the first movie but his scenes were cut and they didn't use him after that." (Specific verifiable production history.)

### `hot_take`
A bold or confident opinion stated without traceable supporting evidence. Asserts a position rather than arguing for it.

> *Example 1:* "Spongebob hasn't been good since season 5 before the original movie came out." (Confident quality claim, no cited episodes or scenes.)

> *Example 2:* "The Agency on Paramount is a top 20 best show ever." (Bold ranking claim with no supporting argument.)

### `reaction`
An immediate emotional response to something watched or announced. Primarily expresses a feeling rather than making an argument.

> *Example 1:* "I was pleasantly surprised at how funny this show was — I was expecting more horror, but was happy to laugh instead." (Shares personal emotional experience of watching.)

> *Example 2:* "I don't care if these shows keep getting shoveled out but I just wish Avatar was given half the amount of love SpongeBob gets." (Expresses frustration and longing, no argument.)

### `discussion`
A question or open-ended prompt inviting the community to share opinions, lists, or recommendations. The post itself does not take a strong position.

> *Example 1:* "What's a TV show you started with low expectations that completely blew you away?" (Open community question, no OP claim.)

> *Example 2:* "What, if anything, do you miss about television in the days before streaming platforms?" (Invites community reflection, OP doesn't assert a position.)

**Hardest boundary:** `hot_take` vs. `analysis` — a post that names a craft problem but never cites a specific scene or episode is a `hot_take`, regardless of how confident or detailed it sounds.

---

## Data

- **Source:** r/television posts and top-level comments collected manually by browsing Hot, New, and Top tabs. Sidebar "related posts" were also mined for `discussion` examples.
- **Total examples:** 200
- **Label distribution:** 50 per label (perfectly balanced)
- **Split:** 140 train / 30 validation / 30 test (70/15/15)
- **Annotation:** AI pre-labeled in batches of 20–30, then reviewed by human annotator. See `source` column in `dataset.csv` (`ai_prelabeled` = assistant labeled, `human` = confirmed, `human_override` = corrected).

### Three difficult-to-label examples

**1. Yellowstone critique → `hot_take` (not `analysis`)**
> *"Yellowstone is super cringe… every single character acts like they're the toughest human being to ever walk the earth… Real tough guys usually don't spend all day trying to sound tough."*

This post is long and structured, which makes it feel like `analysis`. But it never cites a specific scene, episode, or character name — it argues from general impression only. Decision: `hot_take`, because the evidence is personal perception, not traceable to any specific moment.

**2. Reaction with specific scene reference → `reaction` (not `analysis`)**
> *"I still think the Genealogy scene was one of the funniest bits I've ever watched — and this is a psychological horror."*

This names a specific scene ("Genealogy scene") and makes a genre observation, which looks like `analysis`. But the post's purpose is expressing enthusiasm and surprise — the scene name is emotional context, not argumentative evidence. Decision: `reaction`.

**3. Emmy prediction → `hot_take` (not `analysis`)**
> *"With its current momentum I feel like it's guaranteed a nomination for outstanding comedy series, as well as acting noms for Matthew Rhys and Kate O'Flynn."*

This names specific people and award categories, which resembles `analysis`. But it's a confident prediction with no cited evidence — no viewership data, no critical consensus, no comparison to past seasons. Decision: `hot_take`.

---

## Fine-Tuning Approach

- **Base model:** `distilbert-base-uncased` fine-tuned for sequence classification
- **Hardware:** Google Colab T4 GPU
- **Final hyperparameters:** 5 epochs, learning rate 1e-5, batch size 16, weight decay 0.01, warmup steps 50

**Hyperparameter decision:** The first training run used the default 3 epochs and learning rate 2e-5. Validation accuracy peaked at 46.7% and the loss barely moved (1.385 → 1.369), indicating the model was not learning. I reduced the learning rate to 1e-5 (to slow gradient updates and give the model more time to find a good direction) and increased epochs to 5. The second run still failed to converge (final validation accuracy 30%), confirming the issue was not hyperparameter choice alone but a fundamental mismatch between the task's pragmatic signal and the model's capacity with 140 training examples.

## Baseline

**Model:** Groq `llama-3.3-70b-versatile`, zero-shot (no examples provided)

**Prompt used:**

```
You are classifying posts and comments from r/television.
Assign each post to exactly one of the following categories.

analysis: A structured argument backed by specific, verifiable references...
hot_take: A bold or confident opinion stated without traceable supporting evidence...
reaction: An immediate emotional response to something watched or announced...
discussion: A question or open-ended prompt inviting the community to share opinions...

Respond with ONLY the label name. Do not explain your reasoning.
Valid labels: analysis, hot_take, reaction, discussion
```

**Collection:** Each of the 30 test examples was passed to the API individually with temperature=0. All 30 responses parsed cleanly (0 unparseable).

---

## Evaluation Results

### Overall Accuracy

| Model | Accuracy |
|---|---|
| Zero-shot baseline (Groq `llama-3.3-70b-versatile`) | 0.700 |
| Fine-tuned DistilBERT | 0.400 |
| Δ improvement | −0.300 |

The fine-tuned model performed significantly worse than the zero-shot baseline. This is the primary finding of this evaluation.

### Per-Class Metrics — Baseline (Groq Zero-Shot)

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| analysis | 0.88 | 0.88 | 0.88 | 8 |
| hot_take | 0.50 | 0.29 | 0.36 | 7 |
| reaction | 0.55 | 0.75 | 0.63 | 8 |
| discussion | 0.86 | 0.86 | 0.86 | 7 |
| **macro avg** | **0.69** | **0.69** | **0.68** | 30 |

### Per-Class Metrics — Fine-Tuned DistilBERT

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| analysis | 0.33 | 1.00 | 0.50 | 8 |
| hot_take | 0.33 | 0.14 | 0.20 | 7 |
| reaction | 0.00 | 0.00 | 0.00 | 8 |
| discussion | 1.00 | 0.43 | 0.60 | 7 |
| **macro avg** | **0.42** | **0.39** | **0.32** | 30 |

---

## Confusion Matrix — Fine-Tuned Model

Rows = true label, columns = predicted label.

| | pred: analysis | pred: hot_take | pred: reaction | pred: discussion |
|---|---|---|---|---|
| **true: analysis** | **8** | 0 | 0 | 0 |
| **true: hot_take** | 1 | 0 | 0 | **6** |
| **true: reaction** | **5** | 0 | 0 | 3 |
| **true: discussion** | 3 | 0 | 0 | **4** |

The model only ever predicted `analysis` or `discussion`. It never predicted `hot_take` or `reaction` — a complete label collapse for those two classes.

---

## Failure Analysis

### Pattern identified

The model collapsed to predicting only `analysis` and `discussion`. The two dominant error patterns are:

1. **`hot_take` → predicted `discussion`** (6 of 7 hot_take examples): Short opinion statements with no specific evidence look like discussion prompts to the model, possibly because both are brief and non-argumentative at the surface level.

2. **`reaction` → predicted `analysis`** (5 of 8 reaction examples): Reaction posts frequently cite specific episode names, character names, or scene details as emotional context ("the Genealogy scene," "Everyone Else Burns," "Stephen Root"). The model learned that specific proper nouns predict `analysis`, but in reactions those references are context for a feeling, not evidence for an argument.

### Three specific wrong predictions

**Example 1 — True: `hot_take`, Predicted: `discussion`**

> *"They need to revive this show, it was too ahead of its time."*

This is a bold, unsupported claim — no specific evidence, no citation of what made it "ahead of its time." It's a `hot_take`. The model predicted `discussion` because the post is short, opinionated without arguing, and lacks the structural markers it associated with `hot_take` in training. The boundary between a bold claim and a discussion seed is pragmatic (what is the poster trying to do?) rather than lexical, and the model never learned this distinction.

**Example 2 — True: `reaction`, Predicted: `analysis`**

> *"I still think the Genealogy scene was one of the funniest bits I've ever watched — and this is a psychological horror."*

This is a reaction: the poster is expressing enthusiastic surprise at a tonal contrast. But it names a specific scene ("Genealogy scene") and observes a genre property ("psychological horror"), exactly the kind of specific verifiable reference the model associated with `analysis`. The model correctly learned that `analysis` uses specific references — but incorrectly generalized this to any post that mentions a scene name, regardless of whether the reference is argumentative or merely contextual.

**Example 3 — True: `reaction`, Predicted: `analysis`**

> *"We'd only just finished watching Everyone Else Burns when we saw her again in Widows Bay. She's so completely different in those roles, definitely deserves an award."*

This is a reaction: personal viewing context + emotional assessment of a performance. But it names two specific shows (Everyone Else Burns, Widows Bay) and makes a comparison between roles. The model pattern-matched on the named shows as evidence of `analysis`, missing that the post's primary purpose is sharing a feeling of impressed surprise, not arguing a claim.

### Why the boundary is hard

The `reaction`/`analysis` boundary requires understanding *why* a specific reference appears — as evidence for an argument, or as context for a feeling. This is a pragmatic distinction, not a lexical one. DistilBERT with 140 training examples learned surface correlates ("specific show names → analysis") rather than communicative intent. More training data, longer training, or a larger pretrained model would be needed to learn the subtler signal.

The `hot_take`/`discussion` boundary fails for the opposite reason: both can be short and opinionated, and the model never learned to distinguish asserting a position from soliciting one.

---

## Sample Classifications

Five posts run through the fine-tuned model (note: confidence scores were not exported in the evaluation pipeline; qualitative confidence is noted based on prediction behavior):

| Post (truncated) | True Label | Predicted Label | Correct? |
|---|---|---|---|
| "Peeves was cast for the first movie but his scenes were cut and they didn't use him after that." | analysis | analysis | ✓ |
| "What's a TV show you started with low expectations that completely blew you away?" | discussion | discussion | ✓ |
| "They need to revive this show, it was too ahead of its time." | hot_take | discussion | ✗ |
| "I still think the Genealogy scene was one of the funniest bits I've ever watched." | reaction | analysis | ✗ |
| "Spongebob hasn't been good since season 5 before the original movie came out." | hot_take | discussion | ✗ |

**Why the first prediction is reasonable:** "Peeves was cast for the first movie but his scenes were cut" contains a specific, verifiable production fact about a named film. This is exactly the kind of traceable reference that defines `analysis`, and the model correctly identifies it.

---

## Reflection: What the Model Captured vs. What I Intended

I intended the model to distinguish communicative *intent* — is the poster arguing, reacting, asserting, or inviting? Instead, it learned surface lexical correlates:

- **What it captured:** Specific proper nouns (show names, episode names, character names) predict `analysis`; question-format posts predict `discussion`.
- **What it missed:** The pragmatic function of those references. A post can cite three show names while expressing nothing more than personal nostalgia (reaction), and a post can assert a confident claim without a single specific reference (hot_take). The model never learned to ask "what is this post trying to do?" — only "what words does it contain?"

This gap between surface features and communicative intent is the core reason the model failed. Fixing it would require either substantially more training data so the model sees enough variety within each class, or a larger model with stronger semantic understanding that can distinguish reference-as-evidence from reference-as-context.

---

## Spec Reflection

**One way the spec helped:** The hard boundary rule — "must have specific verifiable references to qualify as `analysis`" — gave me a clear, consistent decision rule during annotation. Without it, I would have labeled many structured-sounding `hot_takes` as `analysis`, which would have made the training signal even noisier.

**One way my implementation diverged:** The spec assumed the fine-tuned model would improve on the baseline. In practice, the zero-shot Groq baseline (macro F1: 0.68) substantially outperformed the fine-tuned DistilBERT (macro F1: 0.32). This divergence reveals that for a task where the signal is pragmatic and subtle, a large pretrained model with strong language understanding applied zero-shot outperforms a small model fine-tuned on 140 examples. The spec's framing treats fine-tuning as an improvement by default — this project is a counterexample that depends on that assumption not holding for all task types and data sizes.

---

## AI Tool Usage

**Instance 1 — Batch annotation:** I used Claude to pre-label batches of 20–30 Reddit posts at a time by providing the four label definitions and asking for a label plus one-sentence justification per post. Claude produced a first-pass label for each entry. I then reviewed every label myself and overrode any I disagreed with (tracked in the `source` column as `human_override`). The most common overrides were `hot_take` entries Claude labeled as `analysis` because they mentioned show names without actually making a traceable argument.

**Instance 2 — Failure pattern analysis:** After observing label collapse in the confusion matrix, I pasted representative `hot_take` and `reaction` examples from the dataset into Claude and asked it to identify common textual patterns that might cause a model to misclassify them. Claude identified that reaction posts frequently contain specific named references (scene names, show names) as emotional context — the same surface feature that marks `analysis`. This surfaced the core failure mode: the model learned specific-reference-as-proxy-for-analysis without learning that reactions use references as context, not evidence. I then verified this pattern by manually re-reading the misclassified examples, which confirmed it.

**Instance 3 — Prompt engineering for Section 5:** I used Claude to write the Groq zero-shot classification prompt for Section 5, including the four label definitions, one example per label, and explicit decision rules for the hard boundaries. The key instruction was to output only the label name with no explanation, which kept parse failures to 0/30.

---

## Files

| File | Description |
|---|---|
| `dataset.csv` | 200 labeled examples (50 per class) with `text`, `label`, `source`, `notes` columns |
| `planning.md` | Full label definitions, edge case rules, data collection plan, evaluation metrics reasoning |
| `evaluation_results.json` | Baseline and fine-tuned accuracy, improvement delta, label map |
| `confusion_matrix.png` | Fine-tuned model confusion matrix (test set, n=30) |
