# Changelog

## This version

A revised take on the LLM Council skill, with fixes and upgrades grounded in current
multi-agent and "LLM-as-judge" research, plus ideas from the wider council ecosystem.
The goal throughout: recover the genuine diversity that a single-model council gives up,
and be honest about what the skill can and can't do.

### Fixed
- **Output contradiction.** The previous version said both "never make a file" and "make the
  HTML report clean" — leftover from an older design. Output is now unambiguously chat-only markdown.
- **Broken peer-review traceability.** The chairman now gets a key mapping the anonymized review
  letters back to advisors, so "Response C has a blind spot" can be traced to the right advisor.
- **Silent breakage on chat surfaces.** The old version assumed sub-agents existed. It now has an
  explicit single-context fallback, so it works on plain claude.ai chat, not just Claude Code/Cowork.
- **Tool-specific instructions** (hard-coded Claude Code tool names) replaced with neutral language.
- **Bloated formatting.** Removed double-spacing and fixed the frontmatter (roughly halved the file size).

### Added
- **Reasoning *methods*, not personalities.** Advisors are now distinct methods (pre-mortem/inversion,
  first-principles, second-order effects, base-rate/outside-view, constraints/execution) — divergence
  is the single biggest driver of whether a council helps.
- **Consensus stress-test.** If advisors agree too easily, one is forced to argue the strongest opposite
  case — the configuration most reliably shown to produce real disagreement instead of an echo chamber.
- **Agreement treated as suspect.** Same-model unanimity is flagged as a weak signal, with a trust note
  in the verdict; cross-method agreement is weighted far higher.
- **Judge-bias mitigations.** Per-reviewer order rotation (position bias) and equal-length advisor
  responses (verbosity bias) in the anonymous review.
- **Grounded critique + a fabricated-precision guard.** Advisors must ground claims in the user's real
  numbers and flag missing inputs instead of inventing precise-looking figures.
- **A debiased chairman.** Scores attributes independently before the holistic call, applies an
  outside-view/base-rate check, may overrule the majority, and routes unknown inputs to "open questions."
- **A more useful verdict shape.** Now includes "what you give up" with the recommendation and an
  explicit "what the council couldn't resolve" section.
- **Honest scoping + a lean mode.** Clearly not for verifiable/factual tasks; and a note that the
  critique + forced-dissent steps carry most of the value, with a lighter flow for low-compute runs.
- **Advanced modes reference.** Genuine multi-model councils, jury mode, domain-specific rosters, and
  formal decision frameworks (pre-mortem, weighted matrix, 10/10/10, SWOT, Six Thinking Hats, WRAP).

## Origins

- Concept: Andrej Karpathy's LLM Council — multiple models answer, peer-review anonymously, a chairman synthesizes.
- Claude Code adaptation: Ole Lehmann.
