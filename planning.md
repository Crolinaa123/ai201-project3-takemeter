# TakeMeter — Planning

## Community
r/television is a general TV discussion with active, text-heavy discussions. Posts can range from immediate emoetional reactions after finishing a show, to bold opinions about which shows are overrated, to structured arguments about storytelling decisions, to open-ended questions that invite community debate. The key difference that matters here is how much reasoning and evidence backs a claim, and whether the post is making a claim at all. 

## Labels

### analysis
A post that makes a structured argument backed by specific episode references, 
craft observations (writing, pacing, character arcs), or thematic comparisons.

Example 1: "I Feel Like We Need To Be Less Precious About Adaptations" argues that books and TV are different meduiums, cites a specific House of the Dragon episode (Dragonseeds at the Gullet), discusses production cost constraints, and draws on personal viewing experience with named cost constraints, and draws on personal viewing experience with adaptations (White Teeth, A Woman of Substance). 

Example 2: "TV spinoffs as sequel series" — systematically categorizes spinoffs with specific show names, dates, and parent-series relationships (e.g. Fuller House 2015–20 as a sequel to Full House 1987–95). Evidence is verifiable and organized.

Uncertain case: "Yellowstone is super cringe" — the post argues that every character acts like "the toughest human being to ever walk the earth" and compares this to real tough men the author has known. It names a craft problem (character authenticity) and supports it with a real-world comparison. Could be analysis. Decision: → hot_take, because the evidence is personal impression, not traceable to a specific episode or scene.

### hot_take
A bold opinion stated confidently without supporting evidence. Asserts rather 
than argues.

Example 1: "Craig Ferguson was a genius that deserves more acclaim" — bold claim about Ferguson's greatness, mentions he hosted the Late Late Show 2005–2014 and had a niche fanbase, but never cites specific episodes, bits, or concrete comparisons to other hosts. Asserts rather than argues.

Example 2: "Yellowstone is super cringe" — claims the show is unrealistic because characters act too tough, offers general impressions about character behavior, but cites no specific scene, episode, or moment. Context exists but evidence isn't traceable.

Uncertain case: "Craig Ferguson was a genius that deserves more acclaim" — ends with "Change my mind," which is a rhetorical invitation for debate more than a pure assertion. Could be discussion. Decision: → hot_take, because the post is making a confident claim and defending it, not genuinely soliciting community input.

### reaction
An immediate emotional response to something that just aired or was announced. 
Little to no argument — expressing a feeling in the moment.

Example 1: "I love Daisy Jones and The Six" — "Am I the only one who binge-watched Daisy Jones & The Six because I absolutely loved it?" Expresses enthusiasm, calls the cast "absolutely perfect," says the soundtrack is still on repeat. Primarily sharing an emotional experience.

Example 2 (comment): "I binge watched this show for the first time last weekend and I was surprised by how good it is... I've been listening to the album since I finished and am now reading the book." — Immediate personal response after finishing a show, no argument being made.

Uncertain case: "I love Daisy Jones and The Six" — the post says "I didn't love the cheating storyline... but isn't that exactly what we're supposed to feel? It puts us on an emotional rollercoaster..." This starts to explain why the discomfort works narratively. Could be analysis. Decision: → reaction, because the post is primarily sharing personal enthusiasm; the craft observation is brief and unsupported by specific episode references.

### discussion
A question or open-ended prompt that invites the community to share opinions, lists, or recommendations. The post itself doesn't take a strong position — its purpose is to collect responses.

Example 1: "Which children's show that came out after you grew up would you have watched if it came on when you were little?" — Open-ended question, no claim, designed to solicit community answers.

Example 2: "What is your favorite series finale?" — OP mentions the Sopranos finale as their pick, but the post is structured to collect community favorites, not argue a point.

Uncertain case: "Which children's show that came out after you grew up would you have watched if it came on when you were little?" — OP doesn't share their own answer, so it's clearly a prompt. But some discussion posts include the OP's strong opinion alongside the question (like the finales post). Decision rule: if the post's primary structure is a question inviting responses → discussion, regardless of whether OP includes their own answer.

## Edge Case Decision Rule
**hot_take vs. analysis:** If a post names a craft element but doesn't trace it 
through specific episodes/moments → `hot_take`. Must have specific, traceable 
evidence to qualify as `analysis`.

"Yellowstone is super cringe" gives reasoning about character behavior but never points to a specific scene or episode → hot_take
"I Feel Like We Need To Be Less Precious About Adaptations" cites a specific HOTD episode and production constraints → analysis

** reaction vs. hot_take
If the post's primary purpose is expressing a feeling ("I loved it," "I'm devastated," "this destroyed me") → reaction. If it's primarily making a claim about a show's quality or meaning → hot_take, even if emotional language is used.


** discussion vs. reaction
If the post is structured as a question to the community → discussion. If it's primarily sharing the author's own feeling or experience (even if it ends with "anyone else?") → reaction.


## Data Collection Plan
Source: r/television posts and top-level comments, collected manually by browsing Hot, New, and Top (past month) tabs. 

Target volume: 200 examples, aiming for ~50 per label (25% each) to keep the distribution balanced. The model hint says to aim for at least 20% per label — 25% gives headroom.

Split: 140 train / 30 validation / 30 test (70/15/15).

If a label is underrepresented after 150 examples: Actively seek that type. For analysis, browse post flairs or sort by Top (all time) to find longer, more substantive posts. For reaction, browse episode discussion threads or just-finished posts. For discussion, sort by New to catch fresh prompts before they get buried.

What I will not collect: News article links with no post text, mod announcements, and posts under ~20 words (too short to classify reliably).


## Evaluation Metrics 
Overall accuracy — baseline check, but not sufficient on its own because it masks per-class failures.

Per-class F1 (precision + recall) — the primary metric. With 4 labels, a model could learn to ignore a minority class and still post decent accuracy. F1 per label reveals this. I'll report F1 for each of the four labels.

Macro F1 — unweighted average F1 across all 4 labels. This is my single headline metric because it treats all classes equally regardless of frequency.

Confusion matrix — to identify which specific label pairs the model confuses most (expected: hot_take ↔ analysis).

I'm not using weighted F1 as the headline metric because it would favor the majority class and hide failures on harder labels like analysis.

## Defintion of Success
A classifier is genuinely useful for a community tool if it agrees with a careful human annotator most of the time on the hard cases, not just the easy ones.

Minimum bar (acceptable): Macro F1 ≥ 0.65 on the test set, with no individual label F1 below 0.55. This means the model is doing meaningfully better than random (0.25) and better than always predicting the majority class.

Good enough for deployment: Macro F1 ≥ 0.75, with all per-class F1 ≥ 0.65. At this level, the model would be useful as a first-pass tagger in a real tool, with human review for low-confidence predictions.

Suspicious result: Macro F1 > 0.95 — at that point I'd check for test set leakage or label collapse.

The fine-tuned model must also outperform the Groq zero-shot baseline. If it doesn't, fine-tuning didn't help and the labels may be too easy or too vague.


## AI Tool Plan
### Label stress-testing
Before annotating 200 examples, I will give Claude my four label definitions and edge case rules and ask it to generate 10 posts that sit at the boundary between two labels — specifically hot_take vs. analysis and reaction vs. hot_take. If I can't cleanly classify the generated posts using my decision rules, I'll tighten the definitions before starting annotation.

### Annotation assistance
I will use Claude (via claude.ai) to pre-label batches of 20–30 posts at a time by providing my label definitions and asking for a label + one-sentence justification per post. I will then review every pre-labeled example myself and override any I disagree with. I'll track which examples were pre-labeled in a source column in the CSV (human or ai_prelabeled) for disclosure. Pre-labeled examples I override will be flagged as human_override.

### Failure analysis 
After fine-tuning, I will collect all test set examples where the model predicted the wrong label and paste them (with true label, predicted label, and post text) into Claude with the prompt: "Identify any systematic pattern in these misclassifications." I'll verify any patterns it identifies by manually reviewing the examples myself before including them in the evaluation report.

