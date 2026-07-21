# Handoff Validation — Experimental Protocol

**Status: protocol defined, experiment not yet run.**

Success criteria below are fixed *before* the experiment. Anything decided
afterward is a result, not a criterion.

## Goal

Verify the core product claim: that Architecture Toolkit can consume a PRD
produced by Product Toolkit **without human translation**.

This claim is asserted in VISION.md, README.md, and the PRD template, and has
never been tested. Every other link in the chain is validated:

```
Product intent → PRD contract → prd-engine → product-reviewer → ???
                    ✅              ✅              ✅          untested
```

If the handoff fails, the failure is in the **contract**, not in either
implementation. That is the point of testing it before building more surface.

## Input

`docs/examples/architecture-discovery/PRD.md` — conformant against R-001…R-012,
reviewed, with known weaknesses documented in REVIEWER-VALIDATION.md.

Using a PRD with known defects is deliberate. A handoff that only works on a
perfect document is not a handoff.

## Consumer

Architecture Toolkit v0.1, specifically `references/arc42-template.md` and
`references/adr-template.md`, plus the `arc42-expert` and `adr-expert` skills.

## Expected mappings

The specific claims under test:

| PRD element | Should become | arc42 |
|---|---|---|
| Users / Actors (human) | Stakeholders | §1 |
| Users / Actors (machine) | External systems | §3 Context |
| Integration Requirements | Context neighbours | §3 Context |
| Constraints | Architecture constraints | §2 |
| Goals | Quality goals / requirements overview | §1 |
| Functional Requirements | Requirements overview; input to decomposition | §1, §5 |
| Non Functional Requirements | Quality scenarios | §10 |
| Failure Conditions | Risks | §11 |
| Business Rules | Cross-cutting concepts or constraints | §2, §8 |
| Requirement IDs | Citable from ADR Context | ADR |

## Success criteria

Fixed in advance. The handoff **passes** when all five hold:

- **S-1 — No invention.** No arc42 section requires information absent from the
  PRD and not obtainable by asking. Gaps surface as questions, not as
  plausible filler.
- **S-2 — No manual translation.** Each mapping above is mechanical: the
  consumer restructures PRD content, it does not reinterpret it.
- **S-3 — Traceability holds.** An ADR can cite `FR-005`, `BR-002`, `NFR-001`
  and the citation resolves unambiguously to one requirement.
- **S-4 — Unmeasurable NFRs stay unmeasurable.** An NFR that is an Open
  Question in the PRD becomes an open quality scenario in §10 — it does not
  acquire a number in transit. This is the sharpest test: the PRD's NFR section
  is entirely open questions, so §10 must be too.
- **S-5 — Failure Conditions survive.** FCs land in §11 Risks with their
  expected behavior intact, rather than being dropped as "not architectural."

## Failure criteria

The handoff **fails** if any of these occur:

- **F-1** — An arc42 section can only be filled by inventing content.
- **F-2** — A mapping requires a human to decide what a PRD element means.
- **F-3** — A requirement ID becomes ambiguous or unresolvable.
- **F-4** — An open NFR silently acquires a threshold.
- **F-5** — PRD content with no arc42 home, or an arc42 section with no PRD
  source and no way to ask for one.

## What this experiment does not do

Out of scope, deliberately:

- Adding PRD sections, relationship types, or contract rules
- Building an integration framework, automation, or MCP surface
- Cross-PRD validation
- Producing a complete arc42 document — only enough to test each mapping
- Changing Architecture Toolkit

If a mapping fails, record it. Do not fix it inside this experiment: a contract
change needs its own validation cycle.

## Method

1. Map each PRD section to its arc42 target, recording what transfers cleanly,
   what needs interpretation, and what has no home.
2. Draft the arc42 sections most dependent on product input — §1, §2, §3, §10,
   §11 — from the PRD alone.
3. Draft one ADR citing PRD requirement IDs, to test S-3.
4. Record every point where the consumer would need to ask, invent, or guess.
5. Judge against S-1…S-5 and F-1…F-5 as written above.

## Result

**Run 2026-07-21. Verdict: passes with two contract defects.**

Drafted arc42 §1, §2, §3, §10, §11 and one ADR from the PRD alone. Artifacts in
the session scratchpad.

### Against the success criteria

| | Criterion | Result |
|---|---|---|
| S-1 | No invention | **Pass** — every unfillable point surfaced as a question |
| S-2 | No manual translation | **Fail** — two mappings need a human, see D-1 and D-2 |
| S-3 | Traceability holds | **Pass** — every cited ID resolved unambiguously |
| S-4 | Unmeasurable NFRs stay unmeasurable | **Pass** — all three arrived open |
| S-5 | Failure Conditions survive | **Pass** — including FC-004's "no mitigation" |

S-4 was the sharpest test and it held cleanly. The PRD's three NFRs became three
arc42 quality scenarios that each carry their source NFR and blocking question
forward, and none acquired a number in transit. §10 is a table of three open
scenarios — which is the honest state of that system's quality requirements.

S-3 held under real load. ADR-0001 cites fourteen requirement IDs across seven
namespaces (FR, BR, NFR, C, FC, A, Q, G) and every one resolved to exactly one
requirement. Stable IDs paid off precisely where they were designed to.

S-5 is worth noting: FC-004 arrived in §11 with its "**None.** Nothing detects
the failure" mitigation intact rather than being softened into something
actionable. The failure condition survived contact with a template that expects
mitigations.

### D-1 — `PRD Goals → arc42 quality goals` does not hold (F-2)

The protocol's own mapping table was wrong.

arc42 §1 asks for the top 3–5 **quality goals** — quality attributes with
priority. The PRD's Goals are product outcomes ("surface decisions that were
never recorded"). These are different things, and one does not convert into the
other.

The quality goals would have to come from NFRs — which in this PRD are all open
questions, so §1's quality-goals table is three open items with no priorities.
Defensible for this system, but the mapping is broken in general: a PRD with
measurable NFRs would fill §1 from NFRs, not from Goals.

**This is a contract-level finding.** It was written into the protocol as an
expected mapping and only failed on contact. Not fixed here — a mapping change
needs its own cycle.

### D-2 — Business Rules have two plausible arc42 homes (F-2)

BR-001 ("never invent architecture") is a cross-cutting concept, §8. BR-002 and
BR-003 constrain the analysis method and read as constraints, §2.

Neither the PRD nor arc42 distinguishes, so a human decides — and two people
will decide differently. This is the same class of defect as F-002 and F-003 in
Phase 0: an ambiguity that does not announce itself, resolved silently and
inconsistently.

### Content with no arc42 home

Not failures, but losses worth recording:

| PRD content | Status |
|---|---|
| User Stories | No home. Content survives via FRs; the stories do not transfer. |
| Success Metrics | No home — correct, these are product concerns |
| Non Goals | **No home, and this is a real loss.** Non Goals prevent architecture over-reach, which is precisely the risk in §4 Solution Strategy. |
| Data Requirements | Partial — ownership and sensitivity fit §8; retention has no home |
| Integration criticality | Preserved only if the drafter chooses to keep it; §3 has no field |

Non Goals is the one that matters. A PRD explicitly stating "mobile support is
out of scope" hands architecture a boundary, and arc42 has nowhere to put it.

### Verdict

The core claim holds: **Architecture Toolkit can consume a Product Toolkit PRD,
and the contract's most load-bearing properties survive the crossing.** Stable
IDs make ADR traceability work. Unmeasurable NFRs stay unmeasurable. Failure
conditions arrive intact.

Two mappings need a human, and one of them was wrong in this document before the
experiment ran. That is the value of fixing criteria in advance — D-1 would
otherwise have been quietly reinterpreted as a success.

### Open items — none belong to Product Toolkit

All three findings are **interpretation** problems on the consuming side, not
defects in the PRD contract. None is fixed here, and none should be.

| | Finding | Owner |
|---|---|---|
| D-1 | `Goals → §1 quality goals` is wrong; quality goals derive from NFRs | Architecture Toolkit — mapping rule |
| D-2 | A Business Rule may be a domain invariant or an architectural constraint | Architecture Toolkit — interpretation rule |
| D-3 | Non Goals has no arc42 destination | Architecture Toolkit — scope handling |

The reasoning is the same in each case: the PRD states a product fact correctly.
Deciding which arc42 section that fact belongs in is architectural
interpretation, and Product Toolkit has no standing to make it.

D-2 is worth stating precisely, because it looks like the Phase 0 BR-vs-AC
ambiguity and is not. That one was a real contract defect: the PRD itself could
not decide where a statement belonged. Here the PRD is unambiguous — BR-001 is a
business rule, correctly classified. What is ambiguous is whether the *consumer*
should treat it as §2 or §8. Adding a "kind" field to Business Rules would push
arc42's taxonomy back into the PRD, which is the wrong direction.

### The finding underneath all three

This experiment's mapping table encodes arc42 section numbers into a Product
Toolkit document. That was necessary to run the test, and it is not sustainable:
it means Product Toolkit knows the consumer's format, and any arc42 change makes
a Product Toolkit file wrong.

The correct shape is neither toolkit knowing the other:

```
Product Toolkit          Handoff contract          Architecture Toolkit
  knows PRD        →   knows both, owns the   →      knows arc42
  knows nothing         mapping rules                knows nothing
  about arc42                                        about PRD authoring
```

D-1, D-2, and D-3 are all instances of the same absence: **there is no handoff
contract, so mapping knowledge has nowhere to live and leaks into whichever
document is being written.**

Deciding whether that contract should exist, and where it lives, is the real
next step — not expanding either toolkit. Recorded as B-003.
