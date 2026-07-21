# PRD Contract v0.1 — Enforcement Rules

Rules the PRD engine MUST enforce when producing or validating a PRD. Derived
from three validation passes against a real system
(`docs/examples/architecture-discovery/`).

`templates/PRD.md` defines the *shape*. This document defines the *rules* — the
part a generator will otherwise fill plausibly and wrongly. Filling every
section is not conformance.

Each rule is stated as a check, so it can serve directly as the Product
Reviewer's checklist in Phase 3.

## R-001: Stable IDs

- Every requirement carries an ID from its namespace: `FR-`, `NFR-`, `BR-`,
  `FC-`, `US-`, `G-`, `C-`, `A-`, `Q-`.
- IDs are never reused and never renumbered. ADRs cite them; renumbering
  silently breaks external references.
- Deleting a requirement retires its ID. It does not free it.

**Check:** every requirement has an ID; no ID appears twice; no gap has been
backfilled with a new requirement.

## R-002: NFR vs Constraint classification

Decide by rule, never by feel:

- Removing it would require changing the environment → **Constraint**
- Removing it would make the product worse → **NFR**

One fact may legitimately produce both entries when the split is deliberate.

**Check:** no Constraint states a quality target the product is accountable
for; no NFR states an externally imposed condition that cannot be traded off.

## R-003: NFR measurability

- An NFR states a threshold and a measurement method.
- An NFR without a number is not an NFR. It becomes an Open Question.

This rule is not relaxed in Mode B. Reverse engineering a system with no
telemetry will legitimately produce an NFR section that is entirely Open
Questions — that output is correct, not incomplete, and the engine must present
it as the interview it generated rather than apologize for it.

**Check:** every NFR has a threshold and a measurement; every unmeasurable
candidate appears in Open Questions instead.

## R-004: BR vs Acceptance Criterion classification

Decide by rule:

- Would the rule still exist if this feature disappeared?
  - Yes → **Business Rule**, and the FR references it via `Governed by:`
  - No → **Acceptance Criterion** on that FR

- The same statement is never written as both. A BR restated as an acceptance
  criterion is duplication, and the two copies will diverge.

**Check:** no acceptance criterion restates a BR; every BR survives the
deletion test.

## R-005: Relationship rules

Exactly two relationships exist:

| Relationship | Kind | Meaning |
|---|---|---|
| `Governed by` | Normative | Cannot be satisfied while violating the reference |
| `Related` | Informational | Navigation only, no obligation |

Discriminating test: if an implementation could satisfy the requirement while
ignoring the reference, it is `Related`; if doing so would make the
implementation wrong, it is `Governed by`.

**No new relationship types may be introduced.** `Depends on`, `Implements`,
`Derived from`, `Conflicts with`, and `Overrides` are deliberately excluded.
Each is individually defensible, which is exactly how a PRD becomes a
requirements management system. Adding one is a contract change requiring the
same validation cycle that produced this document.

**Check:** only the two relationship fields appear; no BR is referenced from a
`Related:` field.

**Origin:** F-006 in `docs/examples/architecture-discovery/FINDINGS.md` — the
`Governed by:` field was invented mid-fill and undefined; formalizing it is what
capped the set at two.

## R-006: No inverse relationship duplication

- A link is stored on exactly one side. The FR declares `Governed by:` and that
  direction is authoritative.
- A business rule does not list the requirements it governs. To find them,
  search the ID.

Storing a link on both sides creates two copies of one fact with no mechanism
to detect divergence.

**Check:** no BR carries an `Applies to:` field.

**Origin:** F-007 in `docs/examples/architecture-discovery/FINDINGS.md` — found
while applying F-006, when BRs were seen carrying the inverse of the same link.

## R-007: Failure Condition attachment

- An FC references one or more requirements, or states `(system-level)`
  explicitly.
- An omitted `Applies to:` is not the same as system-level. It means the
  question was never asked, and is a defect.
- Every FC states an expected behavior. Degrade, refuse, or report — never fail
  silently.

An FC's `Applies to:` is its own scope declaration, not an inverse link, so
R-006 does not apply to it.

Every Must-level FR must have **either** at least one Failure Condition, **or**
an explicit justification recorded for why none exists. Not every requirement
needs failure analysis — "display the product logo" reasonably has none, while
"analyze repository architecture" without one is dangerous. What the rule
forbids is the question going unasked.

**Check:** every FC has `Applies to:` and an expected behavior; every Must-level
FR either references an FC or records why it needs none.

## R-008: Unknowns are recorded, never omitted

- Absent information becomes an Open Question or an Assumption. A section is
  never silently left thin.
- Every Assumption states its impact if wrong.
- Every Open Question names what it blocks.

Required sections are filled even when the honest content is "unknown" — a
Success Metrics table of four unknowns *is* the finding.

**Check:** no required section is empty; no assumption lacks an impact; no open
question lacks a blocked item.

## R-009: Mode B section separation

- `Current Behavior` describes only what exists today.
- `Functional Requirements` state what the product must guarantee.
- Overlap between them is expected and correct. Blurring them is not: no
  desired behavior in Current Behavior, no implementation detail in FRs.

**Check:** Current Behavior contains no requirement language ("shall", "must");
FRs contain no implementation specifics.

## R-010: Boundary discipline

- The PRD states *what* and *why*. It never decides *how*.
- `Architecture Impact` may flag risk and name requirements likely to drive
  decisions. It never names a chosen technology.

**Check:** no section prescribes a technology, framework, or design.

## R-011: Requirement lifecycle and retirement

Applies to FR, NFR, BR, and FC.

- A requirement is `Active` (default, field omitted) or `Retired`.
- A retired requirement is **never deleted from the document.** It keeps its
  original text and gains `Status: Retired`, a retirement date, and a reason.
- Its ID stays permanently reserved. Never reassigned, never recycled to close
  the gap it leaves.
- Surviving requirements are never renumbered to tidy the sequence.
- `Replaced by:` names a successor when one exists.

Deleting a retired entry destroys the traceability the ID namespace exists to
provide: an ADR citing FR-003 must still be able to find out what FR-003 was
and why it went away.

**Check:** no ID gap corresponds to a deleted entry; every retired requirement
carries a status, date, and reason; no ID appears with two different meanings
across versions.

## R-012: Retirement reports its coverage effects

Retiring a requirement can orphan the goals, user stories, and failure
conditions that depended on it.

- Report every affected reference. **Never delete an orphaned artifact
  automatically.**
- A user story left with no implementing requirement is reported as orphaned.
  Deleting it would hide that a goal lost coverage — which is the fact worth
  knowing.
- A failure condition whose only referenced requirement retires is retired with
  it, and this is reported rather than silently applied.

The engine reports; the human decides. An orphan is a finding, not a defect to
clean up.

**Check:** after any retirement, every orphaned goal, story, and FC is listed in
the update report.

## Conformance

A PRD conforms when all twelve checks pass. Filling every section is necessary
but not sufficient — R-002, R-004, R-005 and R-006 are the rules a generator
will otherwise violate while producing a document that looks complete.

Conformance says nothing about whether the product decisions are good. That is
semantic review, and it belongs to `product-reviewer`, not here. A PRD can be
fully conformant and describe a bad product.
