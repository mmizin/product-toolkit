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

Not yet run.
