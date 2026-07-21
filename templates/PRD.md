# <Feature Name>

<!--
Architecture-consumable PRD.
This document is consumed by Architecture Toolkit. Structure is a contract:
- Requirement IDs are stable and never reused.
- Sections marked REQUIRED must be present, even if the content is "None".
- Unknowns belong in Open Questions or Assumptions, never left blank.
-->

<!--
Requirement relationships. Exactly two exist. Do not add more.

  Governed by  — NORMATIVE. This requirement cannot be satisfied while
                 violating the referenced rule. Business rules only.
  Related      — INFORMATIONAL. Navigation aid: goals, stories, context worth
                 reading. Carries no obligation.

The test: if an implementation could satisfy the requirement while ignoring the
reference, it is Related. If doing so would make the implementation wrong, it is
Governed by.

Deliberately excluded: Depends on, Implements, Derived from, Conflicts with,
Overrides. Each sounds reasonable alone; together they turn a PRD into a
requirements management system. Two relationships is the cap.
-->

| Field | Value |
|---|---|
| Status | Draft / Review / Approved / Superseded |
| Mode | New Feature / Existing Feature |
| Discovery | Initial / Confirmed |
| Confidence | Low / Medium / High |
| Owner | |
| Last updated | YYYY-MM-DD |

<!-- Discovery and Confidence exist so an incomplete PRD is explicitly
     incomplete rather than quietly thin.

     Discovery:  Initial  — evidence gathered, not yet confirmed with people
                 Confirmed — the unknowns have been worked through

     Confidence: Low     — most sections rest on unanswered questions
                 Medium  — core requirements established, gaps remain
                 High    — open questions are peripheral

     A Low-confidence PRD full of Open Questions is a valid deliverable, not a
     failure. It states what nobody knows yet. What is never acceptable is a
     High-confidence marking on a document whose gaps were filled by guessing. -->


## Overview

REQUIRED. Two to four sentences. What this feature is, for whom.

<!-- Mode B (Existing Feature) adds a "## Current Behavior" section here.

     Current Behavior: existing implementation only. Describe the system as it
     is today. Do not describe desired behavior here.

     Functional Requirements: required product behavior. Do not copy
     implementation details here.

     The two sections overlap in Mode B and that is expected — they answer
     different questions ("what exists" vs "what the product must guarantee").
     Restating a fact in both is correct; blurring the two is not. -->

## Problem Statement

REQUIRED. The problem in user or business terms. No solution language.

## Goals

REQUIRED. What success means. Outcomes, not features.

- G-001: ...

## Non Goals

REQUIRED. Explicitly excluded scope. Prevents architecture over-reach.

- ...

## Users / Actors

REQUIRED. Every actor that interacts with the system, human or machine.
Machine actors become external systems in arc42 Context.

| Actor | Type | Description |
|---|---|---|
| | Human / System | |

## User Stories

REQUIRED.

- US-001: As a <actor>, I want <capability>, so that <value>.

## Functional Requirements

REQUIRED. Primary input to Architecture Toolkit. Implementation-independent.

<!-- Requirement lifecycle. Applies to FR, NFR, BR, and FC alike.

     Status: Active (default — omit the field) | Retired

     A retired requirement is never deleted from the document. Its ID stays
     permanently reserved and is never reassigned, because ADRs and other
     external artifacts cite it. Deleting the entry destroys the traceability
     the ID namespace exists to provide.

     A retired entry keeps its original text and adds:
       - **Status:** Retired
       - **Retired:** YYYY-MM-DD
       - **Reason:** why it left scope
       - **Replaced by:** FR-0XX  (omit if nothing replaces it)
-->

### FR-001: <short title>

- **Description:** What the system must do.
- **Actor:** Who triggers it.
- **Priority:** Must / Should / Could
- **Acceptance Criteria:**
  - Given <context>, when <action>, then <observable outcome>.
- **Governed by:** BR-001 — normative; omit if none apply.
- **Related:** US-001, G-001

## Failure Conditions

REQUIRED. How the product behaves when a requirement cannot be fulfilled, or
when the system cannot produce a reliable result. Success criteria alone do not
define a system: "produced nothing" and "produced a confident wrong answer" are
different outcomes and must be distinguishable.

A failure condition MAY reference one or more requirements. A failure condition
with no references represents a system-level failure mode and states
`(system-level)` explicitly — an omitted field means the question was never
asked, which is not the same thing.

### FC-001: <short title>

- **Applies to:** FR-001, FR-003 — or `(system-level)`
- **Condition:** What goes wrong.
- **Expected behavior:** What the product does in response. Degrade, refuse, or
  report — never fail silently.

## Non Functional Requirements

REQUIRED. Must be measurable — these become architecture quality scenarios.
A requirement without a number is an Open Question, not an NFR.

<!-- NFR vs Constraint — decide with this rule, do not guess:

     If removing it would require changing the environment  → Constraint
     If removing it would make the product worse            → NFR

     Worked example, one fact producing both:
       "Must operate within Claude context limits"          → Constraint
       "Should summarize efficiently to minimize tokens"     → NFR

     One fact may legitimately generate an entry in both sections. That is
     correct when deliberate, and drift when accidental. -->

### NFR-001: <short title>

- **Category:** Performance / Security / Availability / Scalability / Compliance / Observability
- **Requirement:** Measurable statement with a threshold.
- **Measurement:** How it is verified.
- **Priority:** Must / Should / Could

## Business Rules

REQUIRED. Invariants that hold regardless of implementation.

<!-- Business Rule vs Acceptance Criterion — decide with this rule:

     Would the rule still exist if this feature disappeared?
       Yes → Business Rule (domain truth, feature references it)
       No  → Acceptance Criterion (verifies this feature's implementation)

     Example:
       "An ADR has exactly one accepted decision"  → BR (true regardless)
       "Generated ADR contains a decision section" → AC on the generating FR

     Do not write the same statement as both. If it is a BR, the FR references
     its ID; it is not restated as an acceptance criterion. -->

### BR-001: <short title>

- **Rule:** ...
- **Rationale:** ...

<!-- A business rule does not list the requirements it governs. The FR declares
     `Governed by:` and that direction is authoritative. Storing the link on
     both sides means they can disagree, and nothing would detect it. To find
     what a BR governs, search for its ID. -->


## Data Requirements

REQUIRED. Entities, ownership, lifecycle, sensitivity. No schema design.

| Entity | Owned by | Retention | Sensitivity |
|---|---|---|---|
| | this feature / external | | public / internal / PII / regulated |

## Integration Requirements

REQUIRED. Systems this feature depends on or is depended upon by.
Each entry becomes a neighbour in arc42 Context.

| System | Direction | Purpose | Criticality |
|---|---|---|---|
| | inbound / outbound / bidirectional | | blocking / degraded / optional |

## Constraints

REQUIRED. Fixed conditions the solution cannot change — regulatory, contractual,
organisational, or pre-existing platform commitments.

- C-001: ...

## Assumptions

REQUIRED. Things believed true but unverified. Each must be falsifiable and owned.

| ID | Assumption | Impact if wrong | Owner |
|---|---|---|---|
| A-001 | | | |

## Open Questions

REQUIRED. Known unknowns blocking or shaping the design.

| ID | Question | Blocks | Owner | Status |
|---|---|---|---|---|
| Q-001 | | FR-001 | | open / answered |

## Success Metrics

REQUIRED. How the outcome is observed after release.

| Metric | Baseline | Target | Source |
|---|---|---|---|
| | | | |

## Architecture Impact

OPTIONAL. Filled by Product Toolkit only as a hint; Architecture Toolkit owns
the decision. Never contains a chosen technology.

- Requirements likely to drive decisions: FR-001, NFR-002
- Suspected new components or boundaries: ...
- Known architectural risk: ...