# Backlog

Deferred items with the reason for deferral. Not a roadmap — see README for
version planning.

## B-001: Confidence assessment as a Product Reviewer capability

**From:** F-011, Phase 1.3 validation.

Both reverse-engineered PRDs independently produced the same system-level
failure condition: output that is coherent, complete, confident, and materially
wrong, with no detection mechanism. Two unrelated AI-driven features, same
highest-cost uncovered failure mode.

**Deferred because** this is not a template gap — the template surfaced it
correctly both times, and R-007 now prompts for it explicitly. Detecting
whether a *PRD itself* is confidently wrong requires semantic judgement about
the domain, which is `product-reviewer`'s responsibility, not the engine's.

**When:** v0.2, with `product-reviewer`. Candidate scope — assess whether stated
confidence matches evidence, flag requirements asserted without a source, and
identify assumptions doing load-bearing work without an owner.

## B-002: Deterministic validation script

**From:** Phase 1.2 note.

Several contract checks are mechanical: R-001 (ID uniqueness, no backfilled
gaps), R-006 (no `Applies to:` on a BR), parts of R-007 (every FC has
`Applies to:` and an expected behavior), R-011 (retired entries carry status,
date, reason). R-006 was verified during Phase 0 with a one-line grep.

**Deferred because** contract validation currently depends on the model
remembering to run the checks. That is acceptable while the engine is being
proven, and it would be a scope expansion to fix now.

**When:** v0.2 or later. If contract validation is meant to be deterministic,
the mechanical subset probably should not rely on attention.
