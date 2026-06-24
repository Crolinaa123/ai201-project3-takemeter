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


## TODO
- [ ] Read 30–40 r/television posts and validate labels
- [ ] Annotate 200 examples (CSV)
- [ ] Fine-tune DistilBERT on Colab
- [ ] Run Groq baseline comparison
- [ ] Write evaluation report
