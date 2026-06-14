# Advanced council modes

Optional upgrades to the core council. Reach for these when the stakes clearly justify the extra setup. Everything here is opt-in — the core flow in `SKILL.md` works on its own.

## 1. Genuine multi-model mode (the strongest upgrade)

The core skill runs all five advisors on one model, which is its main weakness. If you can reach other models, assign advisors across them so the council gets *real* reasoning diversity instead of one model in five costumes. This is what made Karpathy's original strong.

How, depending on what's available:
- **A connected model provider / API key (OpenRouter, OpenAI, Google, etc.):** route 2–3 advisors to different providers, keep the rest on Claude. Anonymize as usual before review.
- **The Anthropic API inside an artifact:** you can at least spread advisors across different Claude models (Opus, Sonnet, Haiku) for some decorrelation — better than one model, weaker than cross-vendor.
- **Nothing extra available:** stay single-model, but lean hard on the divergence safeguards in the core skill (distinct methods, forced dissent, suspicion of easy agreement).

Don't block a session waiting for this. Offer it for genuinely high-stakes calls; otherwise run single-model and say so.

## 2. Jury mode (multiple chairmen)

A single chairman is itself one biased judge. On close, high-stakes calls, replace the lone chairman with **two or three independent synthesizers**, ideally on different models. Each produces its own verdict from the same advisor responses and reviews, blind to the others. Then run a short reconciliation pass: where the verdicts agree, that's solid; where they diverge, present the divergence to the user rather than papering over it. Use this when one judge's reliability isn't enough to bet on.

## 3. Domain-specific rosters

The five default methods are general. Swap individual advisors when a domain has a better-fitting method — divergence is the rule, the exact roster is not.

- **Technical / architecture decision:** Contrarian → failure-mode / blast-radius analysis; Executor → migration cost and sequencing; add a "Reversibility" advisor (how cheaply can we undo this?). Keep First Principles (what problem are we really solving) and the Outsider (what does the next engineer who inherits this think?).
- **Personal / life decision:** lead with First Principles (what do you actually want, underneath this?) and the Outsider's base rate (what usually happens to people in this situation?). Keep it humane — drop the business register entirely. The Executor becomes "what's the smallest reversible first step?"
- **Research / strategy:** replace the Expansionist with a "Steelman the opposing camp" advisor; keep the Contrarian as a pre-mortem and the Outsider as the base-rate check.
- **Hiring / people decision:** add a "Second-order culture effects" advisor; the Contrarian runs the reference-check skeptic; the Outsider reads the candidate cold.

When you swap, keep the count around five and preserve the core tensions (downside vs. upside, rethink vs. execute, plus fresh eyes).

## 4. Formal-framework variant

Instead of (or alongside) the method-based advisors, run the decision through named decision frameworks, one per advisor. Useful when the user wants rigor or a paper trail:

- **Pre-mortem / inversion** — assume failure, work backward to causes.
- **Weighted decision matrix** — list options as rows, criteria as weighted columns, score and total.
- **10/10/10** — how will you feel about this in 10 minutes, 10 months, 10 years?
- **SWOT** — strengths, weaknesses, opportunities, threats.
- **Six Thinking Hats** — facts, emotions, caution, optimism, creativity, process.
- **WRAP** — Widen options, Reality-test assumptions, Attain distance before deciding, Prepare to be wrong.

Anonymize and peer-review exactly as in the core flow; the chairman synthesizes across frameworks. This pairs well with jury mode for genuinely consequential decisions.

## 5. When to stack modes

- Routine high-stakes call → core flow, single model.
- Important and you have model access → core flow + multi-model.
- Consequential and close → multi-model + jury mode.
- Wants rigor / a record → formal-framework variant + saved transcript.

More machinery is not automatically better. Add a mode only when its specific failure-mode (single-model groupthink, single-judge bias, lack of structure) is the thing actually threatening this decision.
