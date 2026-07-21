# Template Validation Findings

A closed validation experiment, not a changelog. It records three fill passes
that hardened `templates/PRD.md`, and it stopped when a pass found only the
consequences of its own fix. Future validation runs open a new findings file
rather than appending a pass here.

Result of filling `templates/PRD.md` for `architecture-discovery` (Mode B,
reverse engineered from Architecture Toolkit v0.1, source only — no users, no
telemetry, no tracker).

Verdict: **the template held.** Every section could be filled from evidence or
honestly marked unknown. No section had to be skipped, and nothing about the
system was left homeless. Findings below are ordered by whether they warrant a
template change.

## Gap — one confirmed

### F-001: No section owns "what makes a run a failure"

The template captures what a feature must do (FR) and how well (NFR), but not
what constitutes a failed execution as opposed to a low-quality successful one.
For architecture-discovery this mattered: a scan that reports nothing and a scan
that reports confidently wrong architecture are radically different outcomes,
and nothing in the template distinguishes them. Filed as Q-005 to avoid editing
the template mid-exercise.

Not obviously a new section — it may belong as a required sub-field of NFR, or
as a *Failure Modes* section. Worth deciding before the skill hardcodes the
current shape.

## Ambiguity — two found

These are the more valuable findings, since ambiguity does not announce itself.

### F-002: NFR vs Constraint is not decidable from the template

"Must run inside the Claude Code plugin environment" and "analysis is bounded by
the session context window" both read equally well as Constraints (imposed,
unchangeable) and as NFRs (a capacity threshold the system must meet). I placed
them in Constraints (C-001, C-004) but Q-003 asks an NFR-shaped question about
the same fact.

A human will pick one; the future skill will pick the other; the contract drifts
silently. The template needs a stated tiebreak rule. Proposed: if it is imposed
from outside and cannot be traded off, it is a Constraint; if it is a target the
system is accountable for meeting, it is an NFR. The same fact may generate
both, and that is fine — but the split must be deliberate.

### F-003: Business Rule vs Functional Requirement overlap

BR-002 ("folder structure is not deployment structure") and BR-003 ("a
dependency is not a decision") are constraints *on how a requirement is
satisfied*, not domain invariants in the classic business-rule sense. They could
equally be acceptance criteria on FR-002 and FR-003 — and in fact I wrote them
in both places, as a BR and as an acceptance criterion.

That duplication is a smell the template invited. The tiebreak likely is:
if the rule holds across multiple requirements, it is a BR and the FRs reference
it; if it constrains exactly one, it is an acceptance criterion. Worth stating.

## Mode B friction — not gaps, but real cost

### F-004: Current Behavior and Functional Requirements substantially restate

In Mode B, "what the system does today" and "what the system must do" are
nearly the same content at different altitudes. I wrote the workflow twice — as
prose in Current Behavior and as FR-001 to FR-005. It reads acceptably, but it
took the most time of any part of the exercise and it produced two places where
the same fact can go stale.

Options: have Current Behavior carry only the *narrative and history* while FRs
carry the atomic statements, or explicitly permit Current Behavior to reference
FR IDs rather than restate them. The second is cheaper and preserves the
contract. This should be resolved before the skill automates Mode B, because a
generator will happily produce the duplication at scale.

### F-005: Measurable NFRs are not reachable by reverse engineering

The template requires NFRs to carry a threshold and a measurement method, and
correctly reclassifies number-less ones as Open Questions. Applied to a real
undocumented system, **every NFR became an Open Question** — the section is
three unverified entries pointing at Q-002 to Q-005.

The rule is right and I would not weaken it. But it means Mode B structurally
cannot produce a complete NFR section from source alone, because thresholds live
in people's heads and in production telemetry, not in a repository. The engine
must treat this as an expected interview prompt, not a defect in its output.
Worth stating in the template so the emptiness reads as correct rather than as
an incomplete document.

## Boundary check — clean

No content encountered during the exercise belonged to Architecture Toolkit
rather than Product Toolkit. The one borderline case was *Architecture Impact*,
where the temptation was to name the fix for the report's missing schema (Q-009)
rather than just flag the risk. The section's own instruction — hint only, never
a chosen technology — was sufficient to hold the line. The boundary in VISION.md
did not need adjustment.

## Confirmed working

- **Stable IDs** paid off immediately. FRs reference BRs, BRs reference FRs,
  Open Questions block named requirements, and Assumptions name their impact.
  The cross-references made gaps visible that prose would have hidden.
- **Required-even-if-empty** did real work. Success Metrics is four rows of
  "unknown" — which is itself the finding, and would have been silently omitted
  under an optional-sections rule.
- **Assumptions with impact-if-wrong** surfaced A-004 (the report may not
  actually be consumable by the authoring skills), which turned out to be the
  most consequential open risk in the whole document. A plain assumption list
  would not have produced it.

## Recommendation

Three template decisions to make before Phase 1: the NFR/Constraint tiebreak
(F-002), the BR/acceptance-criterion tiebreak (F-003), and the Mode B
duplication rule (F-004). All three are ambiguities rather than gaps, which
means the template works today for a careful human and will drift once a skill
is generating against it.

F-001 and F-005 are additions worth considering but not blocking.

---

# Validation Rerun

Template revised (FC section + FC- IDs, NFR/Constraint rule, BR/AC rule, Mode B
clarifying comments), example PRD refilled against it.

## Resolved

- **F-002** — The environment/product-worse test decided every ambiguous case on
  first read. C-001 and C-004 stayed Constraints; the token-efficiency framing
  became a candidate NFR rather than competing for the same slot. No item
  required a judgement call.
- **F-003** — The "would it survive the feature's deletion?" test reclassified
  nothing, which is the useful result: BR-002 and BR-003 *are* domain truths, so
  my original classification was right and only the duplication was wrong. Five
  acceptance criteria that restated BRs were removed and replaced with a
  `Governed by:` reference. The FRs got shorter and the BRs got load-bearing.
- **F-004** — Comments were sufficient. No structural change needed; the
  overlap now reads as intentional rather than sloppy.

## F-001 result — the FC section paid for itself immediately

Filling four failure conditions produced **two new Open Questions** (Q-011,
Q-012) that four prior passes over the same source never surfaced. Asking "what
happens when this cannot be done" is a different question from "what must this
do," and the template previously had no place to ask it.

The most valuable output is FC-004, *confident wrong output*: the report is
coherent, fully populated, and materially wrong. It is the highest-cost failure
mode of a discovery tool and it has **no detection mechanism at all** — BR-001
and FR-006 make provenance checkable but nothing catches the failure itself.
That risk was invisible in the first PRD. It is now the single most important
finding about architecture-discovery in this document, and it exists only
because the section forced the question.

Attached-with-optional-system-level was the right call. Two of four FCs are
system-level and would have been distorted by a forced FR reference; the other
two anchor to specific FRs and make coverage checkable.

## New finding

### F-006: `Governed by:` is undefined in the template

Removing the BR/AC duplication required a way for an FR to reference a BR
without restating it. I used `- **Governed by:** BR-001, BR-003` and
distinguished it from `Related:` — governance is normative (the BR constrains
this FR), relation is navigational (a goal or story this FR serves).

The distinction proved useful while writing, but **I invented the field mid-fill
and the template does not define it.** That is exactly the drift the contract
exists to prevent: the next author, or the skill, will not know the field exists
or what separates it from `Related:`. Either formalize it in the template or
remove it and fold BRs back into `Related:`. My recommendation is to formalize —
it makes "which requirements does this BR govern?" answerable by reference
rather than by reading, which is the same property that justified FC references.

## Verdict

The three ambiguities are closed and the one gap is closed. F-006 is a small
open item created by the fix itself.

The template is ready for Phase 1 once `Governed by:` is decided. It has now
been filled twice against a real system, and the second pass found a critical
risk the first pass missed — which is evidence the revisions did work rather
than just adding structure.

---

# F-006 Resolution

`Governed by:` formalized as a first-class relationship. Exactly two
relationships now exist, and the cap is stated in the template:

| Relationship | Kind | Meaning |
|---|---|---|
| `Governed by` | Normative | The requirement cannot be satisfied while violating the rule |
| `Related` | Informational | Navigation only; carries no obligation |

The discriminating test, written into the template: *if an implementation could
satisfy the requirement while ignoring the reference, it is `Related`; if doing
so would make the implementation wrong, it is `Governed by`.*

`Depends on`, `Implements`, `Derived from`, `Conflicts with`, and `Overrides`
are named in the template as deliberately excluded, with the reason — each is
individually defensible, which is precisely how a PRD becomes a requirements
management system.

## F-007: bidirectional links can disagree — found while applying F-006

Formalizing the FR→BR direction exposed that business rules carried
`Applies to: FR-00x`, the inverse of the same link. Two copies of one fact, on
opposite sides, with nothing to detect divergence: add an FR governed by BR-002
and forget to update BR-002's list, and the PRD is quietly wrong.

Resolved by making the FR side authoritative. A BR no longer lists what it
governs; to find that, search the ID. Five `Applies to:` lines were removed
from the example.

Failure conditions keep their `Applies to:` — that is the FC's own declaration
of scope, not an inverse of a link stored elsewhere. Nothing else points at an
FC, so there is no second copy to disagree with.

Verified after the change: all five BRs are referenced exactly once, from the
FR side, and no BR appears in a `Related:` field.

## Status

| Finding | Status |
|---|---|
| F-001 Failure Conditions | resolved — section added, found FC-004 |
| F-002 NFR vs Constraint | resolved — environment test |
| F-003 BR vs Acceptance Criterion | resolved — deletion test |
| F-004 Current Behavior vs FR | resolved — comments, no structural change |
| F-005 NFR discovery in Mode B | accepted as correct behavior, rule unchanged |
| F-006 `Governed by` undefined | resolved — formalized, two-relationship cap |
| F-007 Bidirectional link divergence | resolved — FR side authoritative |

Contract stable. Three fill passes against a real system, each finding
something the previous missed; this pass found only a consequence of its own
fix, which is the signal to stop. Phase 1 can start.