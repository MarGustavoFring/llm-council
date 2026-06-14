# LLM Council

> Ask one AI, get one answer. Run it past a council, and find out what that one answer missed.

**LLM Council** is a skill for Claude that takes a hard decision and runs it through five AI advisors who each reason from a *different method*, critique each other anonymously, stress-test the answer they converge on, and hand you one clear verdict — including where they disagreed, what they all missed, and the single thing to do first.

Based on [Andrej Karpathy's LLM Council](https://github.com/karpathy/llm-council). Original Claude Code adaptation by [Ole Lehmann](https://x.com/itsolelehmann). See [CHANGELOG.md](./CHANGELOG.md) for what this version adds.

---

## The problem it solves

Claude is agreeable by default. Ask *"should I do this?"* and it tends to find reasons you should. Ask *"is this a mistake?"* about the same idea and it finds reasons it is. That's fine for drafting an email. It's risky when you're making a real decision and only hearing one voice that quietly bends toward whatever you seem to want.

The council is a structured way to get genuine pushback instead of a yes-man.

---

## How it works

```
        Your question / decision
                  |
                  v
   5 advisors answer independently
   (each uses a different reasoning method)
                  |
                  v
   Anonymous peer review
   (they critique each other blind, so no playing favorites)
                  |
                  v
   Consensus stress-test
   (if they agree too easily, one is forced to argue the opposite)
                  |
                  v
   Chairman synthesis
                  |
                  v
   A single verdict you can act on
```

The five advisors are reasoning *methods*, not personalities:

1. **The Contrarian** — runs a pre-mortem: assumes it failed, works backward to why.
2. **The First Principles Thinker** — strips the question to fundamentals and asks if you're even solving the right problem.
3. **The Expansionist** — hunts for the upside and second-order effects everyone else is discounting.
4. **The Outsider** — has zero context, reacts to what's literally there, and checks it against the base rate.
5. **The Executor** — only cares whether it can actually be done, and what you do Monday morning.

---

## What you get

The verdict comes back in a fixed, scannable shape:

```
## Council Verdict: [your topic]

### Where the council converged   (+ how much to trust the agreement)
### Where the council clashed       (the real disagreements, not smoothed over)
### Blind spots surfaced in review  (what only came out when they critiqued each other)
### The recommendation              (a clear call — and what you give up by taking it)
### What the council couldn't resolve (open questions + what info would settle them)
### The one thing to do first       (a single concrete next step)
```

---

## When to use it

**Good council questions** — judgment calls with real stakes and no single correct answer:
- "Should I launch a $97 workshop or a $497 course?"
- "Which of these three positioning angles is strongest?"
- "I'm thinking of pivoting from X to Y. Am I crazy?"
- "Rewrite this service in Go or keep patching the Python one?"
- "Here's my plan to go solo in 6 months. Poke holes in it."

**Bad council questions** — just ask Claude normally:
- "What's the capital of France?" (one right answer)
- "Write me a tweet" (a creation task)
- "Summarize this article" (a processing task)
- "What's the bug in this function?" (verifiable — one careful pass beats five opinions)

The council earns its cost when the hard part is that *reasonable people would disagree* and you want that disagreement surfaced. On tasks with a checkable answer, it won't pretend to beat a single careful pass.

---

## Where it runs

| Surface | How it runs |
|---|---|
| **Claude Code** | Full power — advisors run as separate parallel sub-agents (real independence). |
| **Claude Cowork** | Same as above — uses sub-agents. |
| **claude.ai chat (web/app)** | Works too — Claude runs the council itself in one conversation, with built-in safeguards to keep the advisors' first opinions independent. Slightly lighter, but no setup needed. |
| **Multiple models (advanced)** | Strongest version: route advisors across *different* models (e.g. via OpenRouter or a connected provider) for real diversity, like Karpathy's original. See [`references/advanced-modes.md`](./references/advanced-modes.md). |

---

## Install

You don't need to touch a terminal.

**Option 1 — let Claude install it for you.** Open a chat and paste:

> Please install this Claude skill for me. The repo is here: https://github.com/YOUR-USERNAME/llm-council — grab `llm-council.skill` and set it up so I can start using it. Walk me through anything you need.

*(Replace `YOUR-USERNAME/llm-council` with your repo address once you've uploaded it.)*

**Option 2 — download and install.**
1. Download **`llm-council.skill`** from this repo.
2. Add it in Claude's skill/capabilities settings (or just send Claude the file and ask it to install it). Claude will walk you through it.

---

## How to use it

Once installed, in any conversation just say one of:

- `council this`
- `run the council on [your question]`
- `pressure-test this` · `stress-test this` · `war room this`
- `poke holes in this`

Claude spins up the advisors, runs the review and the stress-test, and delivers the verdict.

---

## What's in this repo

| File | What it is |
|---|---|
| `llm-council.skill` | The installable package — **this is the file you load into Claude.** |
| `SKILL.md` | The skill's actual instructions (what Claude reads to run a council). |
| `references/advanced-modes.md` | Optional upgrades: multi-model councils, jury mode, domain-specific advisor rosters, formal decision frameworks. |
| `README.md` | This page. |
| `CHANGELOG.md` | What this version changed and why. |
| `LICENSE` | Usage terms. |

---

## New here? Two quick explainers

**What's a "skill"?** A small instruction file you give your AI — like a job description for one specific task. Once it's installed, a trigger phrase tells Claude exactly how to handle that kind of request.

**What's GitHub?** Think Google Drive, but for code and files like this one. You're looking at a GitHub page right now; the buttons near each file let you view or download it.

---

## Honest limitations

This version is upfront about what it is (the full detail is in `SKILL.md`):

- When every advisor is the *same* model, five "independent" voices are still one model's instincts five times over — so the skill treats easy agreement as a weak signal, not a strong one, and leans on the forced-dissent step to create real tension.
- It's built for **judgment calls, not facts.** On verifiable questions, more advisors don't reliably beat one careful answer.
- The biggest single upgrade is genuine **multiple models** (see advanced modes). Everything else exists to recover the diversity a single model gives up.

---

## Credits & License

Methodology by [Andrej Karpathy](https://github.com/karpathy/llm-council). Original Claude Code adaptation by [Ole Lehmann](https://x.com/itsolelehmann). Released under the [MIT License](./LICENSE).
