---
name: llm-council
description: "Run any high-stakes, judgment-call decision through a council of independent AI advisors who each reason from a different method, peer-review each other anonymously, and synthesize a final verdict. Adapted from Karpathy's LLM Council. MANDATORY TRIGGERS: 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this'. STRONG TRIGGERS (use when a real tradeoff is at stake): 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'am I crazy to', 'validate this', 'get multiple perspectives', 'I can't decide', 'I'm torn between', 'poke holes in this'. Do NOT trigger on factual lookups, simple yes/no questions, creation tasks (write/summarize), or casual 'should I' with no real stakes (e.g. 'should I use markdown'). DO trigger whenever the user faces a genuine decision with competing options, real downside, and context suggesting they want it pressure-tested from several angles."
---

# LLM Council

Ask one AI a question and you get one answer. It might be great. It might be mid. You can't tell, because you only saw one perspective — and because Claude is agreeable by default, the same idea framed two different ways ("should I do this?" vs. "is this a mistake?") can get you two opposite answers.

The council is a structured way around that. It runs your question through five advisors who each reason from a *different method*, has them anonymously critique each other, stress-tests the consensus they reach, and then has a chairman synthesize everything into one verdict that tells you where they agreed, where they genuinely clashed, what they all missed, and what you should actually do.

This is adapted from Andrej Karpathy's LLM Council, which dispatches a query to several *different model providers*, has them peer-review each other anonymously, then has a chairman produce the final answer. Read the honest caveat in "Why this works (and where it doesn't)" before you rely on it — the single-model version below trades away some of what makes the original strong, and the steps here are built specifically to claw that back.

---

## When to run the council

The council is for decisions where being wrong is expensive and there is **no single correct answer to look up** — judgment calls with competing options, real downside, and genuine uncertainty.

Good council questions:
- "Should I launch a $97 workshop or a $497 course?"
- "Which of these three positioning angles is strongest?"
- "I'm thinking of pivoting from X to Y. Am I crazy?"
- "Should we rewrite this service in Go or keep patching the Python one?"
- "Here's my plan to leave my job and go solo in 6 months. Poke holes in it."

Bad council questions:
- "What's the capital of France?" — one right answer, just answer it.
- "Write me a tweet" — a creation task, not a decision.
- "Summarize this article" — a processing task, not judgment.
- "What's the bug in this function?" — verifiable; a single careful pass beats five opinions.

The council earns its cost when the value is *coverage of perspectives*, not factual correctness. On tasks with a checkable answer (math, logic, a specific bug), multiple advisors don't reliably beat one careful pass — so don't reach for the council there. Reach for it when the hard part is that reasonable people would disagree, and you want the disagreement surfaced instead of smoothed over. If the user already knows the answer and just wants a cheerleader, the council will probably tell them something they don't want to hear. That's the point.

---

## The five advisors

Each advisor is a **reasoning method**, not a personality or a job title. They are chosen to pull in different directions, because the single biggest factor in whether a council is useful is how genuinely different the advisors are from each other. Five Claude sub-agents share the same underlying instincts, so the method assignments below have to do the heavy lifting of forcing real divergence. Lean hard into them.

### 1. The Contrarian — pre-mortem and inversion
Assume the decision has already failed and work backward to why. Invert the question: instead of "how does this succeed?", ask "what's the most likely way this blows up, and what early sign would I ignore?" Surface the fatal flaw, the hidden cost, the question the user is avoiding. Not a pessimist — the friend who stops you signing a bad deal.

### 2. The First Principles Thinker — strip and rebuild
Ignore the framing. Ask "what are we actually trying to achieve, underneath this question?" Strip away inherited assumptions and rebuild the problem from base facts. The most valuable output here is often "you're solving the wrong problem" — e.g., the user asks how to price a course when the real goal is authority, which a course is a slow path to.

### 3. The Expansionist — second-order effects and upside
Look for the upside everyone else is discounting. What compounds if this works better than expected? What adjacent opportunity does this unlock? What's being undervalued? Ignore downside (that's the Contrarian's job) and chase the question "what does the best realistic version of this become in two years?"

### 4. The Outsider — zero context and the base rate
Has no knowledge of the user, their field, or their history, and responds only to what's literally on the page. Catches the curse of knowledge: jargon that means nothing to a buyer, an offer that's obvious to the user but confusing to everyone else. Then takes the **outside view**: forget this specific plan — what usually happens to ventures, pivots, or launches that look like this one? Anchor to the base rate before the details.

### 5. The Executor — constraints and the first move
Cares about one thing: can this actually be done, with what resources, in what order, starting Monday morning? Ignores theory and big-picture strategy. If an idea is brilliant but has no concrete first step, the Executor says so, then names the step.

**Why these five:** they create real tension. Contrarian vs. Expansionist (downside vs. upside). First Principles vs. Executor (rethink everything vs. just start). The Outsider sits across both, keeping everyone honest with fresh eyes and the base rate.

**Adapting the advisors to the domain.** The five methods are general, but the *register* should match the question. For a technical/architecture call, the Executor talks migration cost and blast radius and the Contrarian runs a failure-mode analysis. For a personal/life decision, the Outsider's base rate and the First Principles "what do you actually want" matter most; keep it humane, not business-coded. For a research or strategy question, you can swap an advisor for a better-fit method (e.g., replace the Expansionist with a "Steelman the opposing camp" advisor). Swapping is fine — preserving genuine divergence is the rule; the specific roster is not. See `references/advanced-modes.md` for domain rosters and formal-framework variants.

---

## How a council session works

**Where the value actually is — read this first.** Be honest about what's doing the work. The engine of this whole process is Step 3 (anonymized critique) and Step 4 (forced dissent): naming the blind spots and arguing the strongest opposing case is where the non-obvious insight comes from. The five separate advisors in Step 2 are the most expensive part and the part most likely to be redundant, because five instances of one model tend to produce one model's answer five times. So:
- Run the **full flow** when the decision is consequential, you have sub-agents or multiple models (real divergence), and the user wants thoroughness.
- Run a **lean version** when you're short on time or compute, or on a single model with no sub-agents: skip the five-advisor convening, write two or three genuinely different first takes (or even one strong take), then still do Step 3's critique, Step 4's forced dissent, and Step 5's synthesis. You keep ~80% of the value for a fraction of the cost.
Don't perform a fifteen-call ceremony to dress up what is really "give a take, attack it honestly, argue the other side, then decide." If you find the advisors all agreeing easily, that's not the council working — it's a sign the lean version would have served the user just as well.

### Step 0 — decide how to run it

The council needs the five advisors to think *independently*. How you achieve that depends on the surface:

- **If you have sub-agents (Claude Code, Cowork):** spawn the advisors and reviewers as parallel sub-agents. This is the strong path — each agent has its own context and can't see the others, so independence is real.
- **If you do NOT have sub-agents (claude.ai chat and most other surfaces):** run the council yourself in a single context by role-playing each advisor in turn. This works, but independence is now on the honor system, so enforce it deliberately: write each advisor's opinion in its own block, commit to it fully before moving to the next, and do not let an earlier advisor's framing leak into a later one. If you catch yourself echoing a previous advisor, that's the signal to push *harder* into the current advisor's distinct method, not to agree.
- **Optional, strongest of all — genuine model diversity:** if you can call other models (e.g., via an API key, OpenRouter, or a connected model provider), assign some advisors to genuinely different models. This restores Karpathy's original mechanism and is the single most effective upgrade available. Details in `references/advanced-modes.md`. Don't block the session on this; offer it when the stakes clearly justify the setup.

Pick the path silently and proceed — don't make the user choose unless they ask.

### Step 1 — frame the question

**A. Gather context (only if you have file access).** The user's question is usually the tip of the iceberg. If you're on a surface with a workspace, quickly look for files that would let advisors give specific, grounded advice instead of generic takes: a `CLAUDE.md` or project notes (business context, constraints, preferences), a `memory/` folder (audience, past decisions, voice), anything the user referenced or attached, recent council transcripts in the folder (so you don't re-litigate settled ground), and anything topic-specific (asking about pricing → look for revenue or past-launch numbers). Spend ~30 seconds, grab the two or three files that matter, and stop. On surfaces with no file access, just use what the user gave you.

**B. Restate before you frame (the problem-restate gate).** In one line, restate what you believe the real decision is. If your restatement differs noticeably from the user's literal words, surface that — a misframed question is the most expensive error the council can make, and catching it here is worth more than any later step. If the question is too vague to restate ("council this: my business"), ask exactly one clarifying question, then proceed.

**C. Frame it neutrally.** Turn the raw question plus the gathered context into one clear prompt all five advisors will receive. Include the core decision, the key context from the user and from any files (stage, audience, constraints, real numbers, past results), and what's at stake. Don't inject your own opinion or steer toward an answer — but do give every advisor enough to be specific. Save the framed question for the chairman and transcript.

### Step 2 — first opinions, independent

Get each advisor's response to the framed question. Each one:
1. Receives its method (from the descriptions above) and the framed question.
2. Is told to respond independently, not hedge, not try to be balanced, and lean fully into its method. If it sees a fatal flaw, say it plainly; if it sees massive upside, say it plainly. Other advisors cover the other angles.
3. Gets a **sycophancy guardrail**: do not soften the take to please the user, do not agree with a position just because it seems to be the user's, and do not praise the idea unless the praise is earned and specific.

Hold every advisor to **150–300 words**. This isn't just for readability — keeping all responses the same length neutralizes the well-documented tendency of AI judges to favor longer answers in the peer-review round. Equal length keeps the review honest.

**Advisor prompt template:**
```
You are [Advisor Name] on an LLM Council. Your reasoning method: [method from above].

A user brought this question to the council:
---
[framed question]
---

Respond using your method only. Be direct and specific. Don't hedge, don't try to be
balanced, don't soften your take to please anyone. Lean fully into your assigned method —
the other advisors cover the angles you don't.

Ground claims in real specifics, not invented ones. Use the user's actual numbers and facts
where they gave them. If a number would strengthen your point but the user didn't provide it,
do NOT manufacture a precise-looking figure — name it as the missing input ("this hinges on
your runway, which we don't know") or label it openly as an assumption ("assume ~5% monthly
churn; if it's higher, this gets worse"). Fabricated precision is worse than honest vagueness,
because it launders a guess as analysis.

150–300 words. No preamble. Go straight into the analysis.
```

### Step 3 — anonymized peer review

This is the step that makes a council more than "ask five times."

Collect the five responses and label them Response A–E. **Randomize which advisor maps to which letter, and present them in a different order to each reviewer.** AI reviewers favor whatever sits in a particular position regardless of content; rotating the order per reviewer cancels that out. Keep a private key of letter → advisor for the chairman step.

Each reviewer (one per advisor) sees all five anonymized responses and answers three questions:

**Reviewer prompt template:**
```
You're reviewing the outputs of an LLM Council. Five advisors independently answered:
---
[framed question]
---
Anonymized responses (review on merit; you don't know who wrote which):

**Response A:** [response]
**Response B:** [response]
**Response C:** [response]
**Response D:** [response]
**Response E:** [response]

Answer, referencing responses by letter. Ground every claim — point to the specific
assumption, missing fact, or unsupported leap, not a general impression. Judge reasoning,
not length or confidence.

1. Which response is strongest, and what specifically makes it strong?
2. Which has the biggest blind spot, and what exactly is it missing?
3. What did ALL five miss that the council should consider?

Under 200 words. Be direct.
```

### Step 4 — consensus check and forced dissent

Before synthesis, look at how much the advisors actually diverged.

- **If they genuinely disagree** (different recommendations, real tension): good — the council is working. Skip to synthesis; the dissent is already on the table.
- **If four or five converged on the same recommendation:** treat that convergence with suspicion, not relief. When every advisor is the same underlying model, agreement is partly shared priors talking to itself, not five independent minds confirming an answer. So manufacture the missing tension: run one more pass that builds the strongest honest case for the *opposite* conclusion — the steelman the council talked itself out of. Feed this dissent into the chairman alongside everything else. (One evolved version of this skill calls easy agreement "theatrical consensus" and tells the chairman to weight it as a single opinion. Same idea.)

### Step 5 — chairman synthesis

One agent receives: the framed question, all five advisor responses **de-anonymized**, the key mapping the review letters back to advisors (so "Response C has a blind spot" can be traced to the actual advisor), all five peer reviews, and the forced dissent from Step 4 if there was one.

The chairman does three things before writing the verdict, in this order:
1. **Score independent attributes first.** Before forming an overall view, rate the decision on three or four separate dimensions (e.g., upside if it works, cost of being wrong, feasibility, confidence in the framing). Scoring parts before the whole reduces the halo effect where one strong impression colors everything.
2. **Apply the outside view.** Ask what the base rate says for decisions like this, independent of how compelling the specific arguments sound.
3. **Resist the crowd.** Do not side with the majority just because it's the majority, and do not reward the longest or most confident response. If the lone dissenter has the strongest reasoning, side with the dissenter and explain why.
4. **Don't invent the inputs.** If the verdict turns on a number or fact the user never gave (runway, conversion rate, team size, budget), say so plainly and route it into "What the council couldn't resolve" — do not paper over the gap with a confident-sounding figure you made up. A recommendation that names its missing inputs is more useful than one that fakes certainty.

Then produce the verdict in this structure (this is what the user sees):

```
## Council Verdict: [short topic]

### Where the council converged
[Points multiple advisors reached independently — and a one-line note on how much to trust
the agreement, given they share a model. Genuine cross-method agreement is a strong signal;
easy unanimity that the dissent pass had to break is a weak one.]

### Where the council clashed
[The real disagreements. Don't smooth them over. Present both sides and why reasonable
advisors land differently.]

### Blind spots surfaced in review
[What only emerged in the peer-review round — what individual advisors missed that others caught.]

### The recommendation
[A clear, direct call. Not "it depends." End it with one sentence on what the user gives up
by following it — every real recommendation has a cost.]

### What the council couldn't resolve
[Open questions the council genuinely can't settle, and what information would settle them.
Lead the user toward what's still unknown rather than false confidence.]

### The one thing to do first
[A single concrete next step. Not a list of ten. One.]
```

### Step 6 — present the verdict

Present the verdict directly in chat as markdown, using the structure above. Do **not** generate an HTML file, a report file, or any other artifact — the user reads the verdict in the conversation. Keep it scannable.

### Step 7 — save the transcript (optional)

Only if the user asks, or the decision is significant enough to revisit. If saving, write `council-transcript-[timestamp].md` to the project's working directory.

---

## Example: counciling a product decision

**User:** "Council this: I'm thinking of building a $297 course on Claude Code for beginners. My audience is mostly non-technical solopreneurs. Is this the right move?"

**The Contrarian (pre-mortem):** "Picture this dead in six months. Most likely cause: the market's flooded with Claude courses competing against free YouTube, and non-technical beginners generate heavy support load and refunds. The early sign you'll rationalize away: weak pre-sales that you blame on 'bad timing' instead of bad fit..."

**The First Principles Thinker (strip and rebuild):** "What are you actually after? If it's revenue, a course is one of the slowest paths. If it's authority, a free resource builds it faster. If it's a feeder for higher-ticket work, $297 to non-technical beginners may be the wrong door entirely. Decide the goal before the format..."

**The Expansionist (second-order):** "Beginner solopreneurs are underserved while everyone teaches advanced users. Own the entry point and you own the funnel into everything above it — community, templates, a $997 cohort. $297 might be underpricing the position, not the product..."

**The Outsider (zero context + base rate):** "I don't know what Claude Code is, and neither will half your buyers — the title sells a tool, not an outcome. Base rate: most solo course launches to a cold beginner audience underperform the founder's projection by a wide margin. Sell the result, and assume slower than you hope..."

**The Executor (constraints + first move):** "A real course is 4–8 weeks of production. Don't build it. Run one live $97 workshop to 50 people first. If 50 won't buy the workshop, 500 won't buy the course — and you've got testimonials and raw material either way..."

**Chairman's Verdict (excerpt):** Converged: the beginner-solopreneur angle has real demand, but "Claude Code course" is too tool-specific for the buyer — trust this, it came from different methods independently. Clashed: price (too high given competition vs. too low for the position) — unresolved until scope is fixed. Recommendation: don't build the course yet; validate with a low-commitment offer and reframe to sell the outcome, not the tool — the cost is a slower start and less upfront revenue. First thing: run a $97 live workshop titled around the outcome ("automate your first business task with AI"), no "Claude Code" in the title.

---

## Why this works (and where it doesn't)

Be honest with yourself about what this skill is and isn't:

- **The original got its strength from using different models.** This version uses one model wearing five method-hats, which is weaker. Everything above — distinct *methods* not personalities, the sycophancy guardrail, the forced-dissent pass, treating easy agreement as suspect, and the option to bring in real other models — exists to recover the diversity that a single model loses. The biggest factor in a useful council is genuine divergence among advisors; protect it above all.
- **Agreement among same-model advisors is not strong evidence.** A model can confirm its own blind spots five times over. Weight cross-*method* agreement (the Contrarian and the Executor landing in the same place by different routes) far more than unanimity.
- **This is for judgment, not facts.** On verifiable tasks, a council does not reliably beat one careful pass — so the "when to run" rules matter. The council's edge is surfacing perspectives and blind spots on decisions with no lookup-able answer.
- **Critiques must be grounded — but not faked.** Reviews that point to a specific assumption or missing fact improve the outcome; reviews that emit vibes don't. The catch: told to "be specific," a model will happily manufacture specific-*looking* numbers it has no basis for. That's worse than vibes, because false precision reads as analysis. Ground claims in the user's real inputs; where an input is missing, flag the gap instead of inventing a figure.
- **A council can't audit itself out of being one model.** Anonymization, the trust caveats, even this note are all written by the same model. They reduce the most obvious biases; they do not make a single-model council into five independent minds. Treat the whole thing as a structured way to make one model argue with itself honestly — useful, but not a substitute for a real second opinion or the user's own judgment.

---

## Important notes

- **Independence is the whole game.** Whether via sub-agents or careful role-play, advisors must form first opinions without seeing each other's. Don't let them blend.
- **Always anonymize and rotate order for peer review.** Named or fixed-position responses get judged by who/where, not on merit.
- **The chairman may overrule the majority.** If four advisors say "do it" and the one dissenter has the better argument, side with the dissenter and say why.
- **Don't council trivia.** One right answer, a creation task, or a checkable bug → just handle it directly.
- **Output stays in chat.** No HTML, no report files — present the verdict as markdown in the conversation.

---

## Advanced modes

For genuine multi-model councils, jury mode (replacing the single chairman with several independent synthesizers), domain-specific advisor rosters, and formal-framework variants (pre-mortem, weighted decision matrix, 10/10/10, SWOT, Six Thinking Hats, WRAP), read `references/advanced-modes.md` when the situation calls for them.

---

Adapted from Andrej Karpathy's LLM Council and Ole Lehmann's Claude Code adaptation.
