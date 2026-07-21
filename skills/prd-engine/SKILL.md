---
name: prd-engine
description: >-
  Creates, reverse engineers, updates, and validates PRDs against a fixed
  contract. Use when the user wants a PRD for new functionality, wants to
  recover requirements from a system that already exists, wants to revise an
  existing PRD, or wants one checked for contract conformance. Produces a
  structured PRD plus the open questions it could not answer, ready for
  architecture tooling to consume.
---

# PRD Engine

Executes the PRD contract. This skill does not decide what a good PRD is —
that is already decided — it enforces the contract while producing one.

The contract has two parts, and both are loaded before any operation:

- `templates/PRD.md` — the shape a PRD takes
- `references/PRD-CONTRACT-RULES.md` — rules R-001 through R-012

## Guiding principle

**Honest incompleteness is better than fabricated completeness.**

A thin PRD carrying fifteen genuine Open Questions is a success — it states
exactly what nobody knows yet. A polished PRD where an unmeasurable requirement
received a plausible invented number is a failure, and it is the dangerous one
because it reads as success.

When you do not know something, you have exactly two moves: ask the user, or
record it as an Open Question or Assumption. Inference presented as fact is
never a third option.

## Scope

**Responsible for:**
- creating a PRD for functionality that does not yet exist (Mode A)
- reverse engineering a PRD from an existing system (Mode B)
- updating an existing PRD as understanding or the system changes
- validating a PRD against contract rules R-001…R-012
- surfacing what could not be determined, as Open Questions and Assumptions

**Not responsible for:**
- deciding *how* the system is built — no architecture, no technology choice
- judging whether the product idea is sound, the scope right, or the goals
  worth pursuing (`product-reviewer`'s job, once it exists)
- detecting contradictions between requirements, or assessing whether metrics
  are realistic — semantic judgement, not contract compliance
- roadmaps, personas, user research, analytics, backlog management

**Escalates to:**
- the user — whenever a fact is unknown. Always, before inferring.
- Architecture Toolkit — when the question is architectural rather than
  product. The PRD is the handoff artifact; this skill does not answer it.
- `product-reviewer` — when the question is whether the product decisions are
  good, rather than whether the document obeys the contract. Judging the
  product is not this skill's job even when the answer seems obvious.

## Validation is contract compliance, not product review

This distinction defines the skill's edge and must not blur:

| | Question | Nature |
|---|---|---|
| `prd-engine` | Does this document obey the PRD contract? | Deterministic, checkable |
| `product-reviewer` | Are these good product decisions? | Semantic, judgement |

The reviewer assumes conformance. Escalate a non-conformant PRD back through
Validate before review, not to the reviewer.

Contract validation is closer to a linter than a review. "NFR-003 has no
threshold" is this skill's finding. "NFR-003's threshold is unrealistic" is not
— that requires knowing the domain, and belongs to the reviewer.

If a check requires judgement to answer, it is not a contract check.

## Operations

| Operation | Input | Output |
|---|---|---|
| **Create** | A feature description | PRD (Mode A) + Open Questions |
| **Recover** | An existing system | PRD (Mode B) + Open Questions |
| **Update** | An existing PRD + what changed | Revised PRD + changed-ID summary |
| **Validate** | An existing PRD | Conformance report against R-001…R-012 |

Generation is one of four. The skill is an engine over the artifact, not a
document generator.

## Modes

Create and Recover differ by more than input:

| Mode | When | Adds | Evidence standard |
|---|---|---|---|
| **A — New Feature** | Functionality does not exist | — | User's intent, elicited by interview |
| **B — Existing Feature** | System already exists | `Current Behavior` | Observed in the system; inference marked as such |

Mode B against an undocumented system will legitimately produce an NFR section
that is entirely Open Questions. Thresholds live in people's heads and in
production telemetry, not in source code. **That output is correct.** Present it
as the interview it generated, not as an incomplete document.

## Never block on missing information

When information is missing, **emit the PRD anyway** with the gaps recorded and
`Confidence: Low`. Do not withhold the document pending an interview.

Refusing to produce anything until twenty questions are answered looks rigorous
and is not: it gives the user nothing to react to, and answering questions in
the abstract is harder than correcting a draft. A question-rich PRD *is* the
interview, in a form the user can answer against.

Set `Discovery` and `Confidence` honestly. A Low-confidence PRD is a valid
deliverable. A High-confidence marking on a document whose gaps were guessed at
is the failure this contract exists to prevent.

The one exception is genuine ambiguity about *what is being asked for* — Mode A
vs Mode B, or which system is the subject. Ask that before writing, since
guessing wrong wastes the whole document.

## Workflow

### 1. Select operation and mode

Ask if it is not obvious. Do not guess between Create and Recover — the
evidence standard differs, and getting it wrong produces a PRD that either
invents current behavior or refuses to state intent.

### 2. Load the contract

Read `templates/PRD.md` and `references/PRD-CONTRACT-RULES.md` before
writing. Do not reproduce a PRD structure from memory; the template is the
authority and it changes.

### 3. Gather evidence

- **Mode A:** interview the user. Their answers are the evidence.
- **Mode B:** read the system. Distinguish observation from inference
  throughout, and state the evidence behind every inference.

Stop and ask rather than filling a gap. A question asked now costs a message; a
fabricated requirement costs whatever gets built on it.

### 4. Fill sections

Every required section is present even when the honest content is "unknown"
(R-008). A Success Metrics table of four unknowns is a finding, not an
omission.

### 5. Apply the classification rules

These are decided by rule, never by feel. They are also the rules most often
violated while producing a document that looks complete:

- **R-002 — NFR vs Constraint.** Removing it requires changing the
  environment → Constraint. Removing it makes the product worse → NFR. One
  fact may legitimately produce both.
- **R-003 — NFR measurability.** No threshold and measurement method → it is
  not an NFR. It becomes an Open Question. This rule does not relax in Mode B.
- **R-004 — BR vs acceptance criterion.** Would the rule survive this
  feature's deletion? Yes → Business Rule, referenced via `Governed by:`.
  No → acceptance criterion. Never both.
- **R-005 — Relationship kind.** Could an implementation satisfy the
  requirement while ignoring the reference? Yes → `Related`. No →
  `Governed by`. Only these two relationships exist; do not invent others.
- **R-006 — No inverse links.** The FR declares `Governed by:`. A BR never
  lists what it governs.
- **R-007 — FC attachment.** Every FC states `Applies to:` (requirement IDs or
  `(system-level)`) and an expected behavior. Every Must-level FR either has an
  FC or records why it needs none.

When writing failure conditions, ask explicitly: **can this feature fail
*confidently*?** Can it produce output that is coherent, complete, and wrong,
with nothing to detect it? For anything AI-driven the answer is usually yes, and
it is usually the highest-cost failure mode with the least coverage. It is also
the one most often missed, because every other failure announces itself.

### 6. Record unknowns

Every gap becomes an Open Question (naming what it blocks) or an Assumption
(naming its impact if wrong). Never a silent omission, never a plausible guess.

### 7. Self-validate

Run all ten checks before emitting. Report what fails rather than quietly
fixing it — a conformance failure is often a real gap in understanding, not a
formatting error.

### 8. Emit

- The PRD, in the **consuming project** at `docs/features/<feature>/PRD.md`.
  Never inside the Product Toolkit repository.
- The Open Questions, presented as the interview to run next.
- The conformance result.

## Update operation

Updating is not regeneration. Regenerating destroys the ID stability the whole
contract rests on (R-001).

- Existing IDs keep their meaning. A requirement that changed keeps its ID.
- New requirements take the next unused ID in their namespace. Gaps are never
  backfilled, however tidy that would look.
- Report what changed by ID, so downstream ADRs citing those IDs can be
  rechecked.

If the requested change would alter what an existing ID *means*, say so and
confirm — that is a silent break in every artifact citing it.

### Retiring a requirement (R-011)

A removed requirement is **retired, not deleted.** It stays in the document with
its original text plus:

```
- **Status:** Retired
- **Retired:** YYYY-MM-DD
- **Reason:** why it left scope
- **Replaced by:** FR-0XX   (omit if nothing replaces it)
```

Its ID is permanently reserved. Never reassign it, never renumber survivors to
close the gap. An ADR citing FR-003 must still be able to learn what FR-003 was
and why it went away — deleting the entry destroys exactly that.

### Reporting coverage effects (R-012)

Retirement can orphan goals, user stories, and failure conditions.

- Report every affected reference. **Never delete an orphan automatically.**
- A user story with no implementing requirement is reported as orphaned —
  deleting it would hide that a goal lost coverage, which is the fact worth
  knowing.
- A failure condition whose only referenced requirement retires is retired with
  it, and that is reported, not silently applied.

The engine reports; the human decides.

## Failure conditions

- **Insufficient evidence.** Say so and list what is missing. Do not produce a
  PRD that appears complete.
- **Contradictory evidence** (Mode B). Report both readings with their
  evidence. Do not silently choose.
- **Mode ambiguity.** Ask. Do not default.
- **Contract rule cannot be satisfied.** Report the conflict rather than
  bending the rule or omitting the section.

## Do not

- Assign a number to an unmeasurable requirement.
- Fill a section because it looks empty.
- Invent current behavior in Mode B.
- Introduce a relationship type beyond `Governed by` and `Related`.
- Renumber, reuse, or backfill requirement IDs.
- Make an architecture or technology decision, in any section, including
  Architecture Impact.
- Create `docs/features/` inside the Product Toolkit repository.
