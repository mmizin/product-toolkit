# Phase 2 — Reviewer Validation

Two runs of `product-reviewer` against PRDs produced in Phase 1. Review
artifacts are disposable and live in the session scratchpad; findings are here.

The question under test is not "does it produce findings" but **does it produce
the right kind** — semantic judgement, not contract checking dressed up as
review.

## Review 1 — architecture-discovery PRD

A dense, conformant Mode B PRD. Verdict: **Accept with changes**, six findings.

| Check | Result |
|---|---|
| Any finding reproducible from the contract rules alone | **No** — verified below |
| Findings cite requirement IDs | Yes |
| Strengths concrete, not courtesy | Yes — three, each naming a specific behavior |
| Severity discriminates | Yes — one Critical, one High, two Medium, one Recommendation, one Possible defect |

**The two findings that justify the agent's existence:**

**FR-005/FR-006 conflict with C-004 (High).** The report must be comprehensive
*and* cite evidence per inference, within one session's context budget. For any
repository large enough to need discovery, these cannot all hold. The PRD
records the pieces separately — FC-002 flags capacity, Q-003 asks about size —
but never states the requirements are in tension. Every contract rule passes;
the feature is still unsatisfiable as specified.

**A-003 is load-bearing and unowned (Critical).** If repository evidence is
insufficient without runtime observation, discovery fails precisely on the
systems where architecture is hardest to understand. The PRD states the impact
correctly. Nobody owns it, so nobody will test it.

R-008 requires assumptions to state impact — satisfied. Nothing in the contract
can say *this particular assumption decides whether the feature works.* That
requires reading the feature and judging.

**Also surfaced:** G-004 may be unachievable (the report is auditable but not
trustworthy, since FC-004 concedes confident wrongness is undetectable), and the
Success Metrics would read as excellent exactly when the tool is most wrong,
because every metric depends on a user noticing an error.

## Review 2 — Mode A export PRD (thin, Confidence: Low)

Thirteen Open Questions, one FR, no NFRs. This tests the opposite failure: does
the reviewer punish honest incompleteness?

| Check | Result |
|---|---|
| Verdict | **Cannot assess** — correct |
| Thinness treated as a defect | No |
| Empty NFR section reported | No — conformant under R-003 |
| Single-row Success Metrics reported | No — conformant under R-008 |
| Unestablished Non Goals reported | No |
| Findings produced anyway | Two, both substantive |

**The one finding worth having:** A-001 assumes "export" means a transformation
rather than a file copy, and notes that if wrong the feature is unnecessary.
The reviewer's contribution was not spotting the assumption — the PRD states it
— but arguing it is **misfiled**. It sits as one of three assumptions alongside
questions about triggering and audience, when it actually determines whether
there is a feature at all. A PRD is already Markdown; if export means writing
the file elsewhere, the document describes `cp`.

That is a judgement about relative weight, which no rule can encode.

## Findings

### F-012: The reviewer's failure mode is producing volume, and it is invisible

Both reviews stayed clear of contract findings, but only because the boundary is
stated four separate times — in the checklist header, the agent's Scope, the
"line you must not cross" table, and the escalation edge. That redundancy looked
excessive while writing and now looks necessary.

Contract findings are attractive to produce: abundant, certain, and they make a
review look thorough. A reviewer drifting into them would appear to be working
harder while delivering strictly less. Unlike most failure modes this one leaves
no trace — a report full of conformance findings reads as diligent.

The worked comparison table (`"NFR-003 has no threshold"` vs `"NFR-003's 50ms
threshold is unachievable given C-002"`) did more to hold the line than the
prose rules. Concrete pairs beat principles here.

**Resolved as a rule, not a capability.** Every Defect, Possible defect, and Gap
must state evidence, impact, and a recommendation. A finding without impact is
an observation; one without a recommendation is a complaint. Both are cheap to
generate in quantity, which is exactly what volume-driven review produces.

Deliberately *not* built: reviewer self-validation. Reviewing the reviewer's
reviews regresses without terminating, and each layer is less grounded than the
one below it. A structural requirement on findings does the same work at the
point of production, where the author still has the evidence in hand.

**Note on the two reviews above:** both predate this rule and collect
recommendations in a separate section rather than attaching one to each finding.
Their findings carry evidence and impact, but a reader must cross-reference to
learn what to do about any given one. Re-running them under the rule would
restructure the output, not change the substance. Recorded rather than
retrofitted — the reviews are evidence of what the agent produced at the time,
and editing them would destroy that.

### F-013: "Cannot assess" needs to be genuinely acceptable, not nominally available

Review 2's correct output is a verdict that declines to judge. The pressure to
manufacture findings on a thin document is real — thirteen Open Questions offer
thirteen chances to say something that sounds like insight while restating what
the PRD already says.

The checklist's line *absence of an answer is a Gap only when the question is
missing* was what made the distinction operable. Without it, "Cannot assess"
would exist as an option nobody picks.

## Status

| Test | Result |
|---|---|
| Dense conformant PRD | Pass — six findings, none contract-derived, two materially important |
| Thin honest PRD | Pass — Cannot assess, thinness not penalized |
| Boundary held | Pass in both |

Contract findings leaked into review output: **none**.

The reviewer found two things in a PRD that passes all twelve contract rules: a
requirement set that cannot be satisfied under its own constraints, and an
unowned assumption that decides whether the feature works. Neither is reachable
by any deterministic check, which is the argument for the agent existing.
