# Backlog

Deferred items with the reason for deferral. Not a roadmap — see README for
version planning.

## B-001: Confidence assessment as a Product Reviewer capability

**Status:** Delivered in v0.2. All three candidate-scope items are implemented
in `agents/product-reviewer.md` — confidence against evidence, requirements
asserted without a source, and unowned load-bearing assumptions.

**From:** F-011, Phase 1.3 validation.

Both reverse-engineered PRDs independently produced the same system-level
failure condition: output that is coherent, complete, confident, and materially
wrong, with no detection mechanism. Two unrelated AI-driven features, same
highest-cost uncovered failure mode.

**Deferred because** this is not a template gap — the template surfaced it
correctly both times, and R-007 now prompts for it explicitly. Detecting
whether a *PRD itself* is confidently wrong requires semantic judgement about
the domain, which is `product-reviewer`'s responsibility, not the engine's.

**Was planned for:** v0.2, with `product-reviewer`. Candidate scope — assess
whether stated confidence matches evidence, flag requirements asserted without a source, and
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

## B-003: Does a shared handoff contract need to exist?

**From:** D-1, D-2, D-3, Phase 2 handoff validation.

The handoff works — traceability, open NFRs, and failure conditions all survive
the crossing. But all three defects found are the same absence: mapping
knowledge has no home, so it leaks into whichever document is being written.
`HANDOFF-VALIDATION.md` currently encodes arc42 section numbers into a Product
Toolkit file, which makes a Product Toolkit document wrong whenever arc42
changes.

The shape that would fix it:

```
Product Toolkit    →    Handoff contract    →    Architecture Toolkit
knows PRD only          owns the mapping         knows arc42 only
```

Open questions, none answered:

- Does a third artifact justify its own existence, or is this over-engineering
  for a two-consumer problem?
- If it exists, where does it live — a third repository, or owned by the
  consumer since interpretation is architectural?
- Does it need to be machine-readable, or is a documented rule set enough?

**Deferred because** this is a cross-toolkit decision, not a Product Toolkit
feature, and it cannot be made unilaterally. Doing it wrong couples the two
plugins more tightly than they are now, which is worse than the current
duplication.

**When:** before either toolkit grows another consumer or producer. The problem
is currently cheap because there is exactly one of each.
