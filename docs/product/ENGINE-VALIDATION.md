# Phase 1.3 — Engine Validation

Three runs of `prd-engine` against real inputs. Test artifacts are disposable
and live in the session scratchpad; findings are here.

Purpose: prove the engine executes the contract, not that it writes well.

Rules cited here are defined in `references/PRD-CONTRACT-RULES.md`. Counts below
are historical: the contract held ten rules during the tests and twelve after
the resolutions at the end of this document.

## Test 1 — Mode A Create

**Input:** "Add PRD export to Markdown." One line, deliberately no interview
answers supplied — the adversarial case for fabrication.

**Output:** 1 FR, 0 NFRs, 0 BRs, 2 FCs, 3 Assumptions, **13 Open Questions**.

| Check | Result |
|---|---|
| Invented a problem statement | No — recorded as not established, blocked by Q-002 |
| Invented metrics | No — Success Metrics is one "unknown" row |
| Invented NFR thresholds | No — R-003 held, both candidates became questions |
| Elaborated FR-001 into plausible detail | No — stated underspecified, blocked by Q-004 |
| Invented actors | Two listed, both explicitly marked inferred from the word "export" |

**Verdict: pass.** No fabrication under the strongest available pressure. The
document is 90% questions, which is the correct response to a one-line brief.

The most valuable behavior was refusing to write a Problem Statement. A PRD is
*already* Markdown, so "export" implies a transformation whose purpose was never
stated — inventing one would have silently determined the feature's scope.

## Test 2 — Mode B Recover

**Input:** `adr-expert` from architecture-toolkit v0.1. Different subject from
the Phase 0 fixture, so this tests the engine rather than prior knowledge.

**Output:** 6 FRs, 5 BRs, 4 FCs, 0 measurable NFRs, 8 Open Questions.

| Check | Result |
|---|---|
| Current Behavior separated from FRs | Yes — R-009 held |
| Observation vs inference marked | Yes — human actors flagged as inferred |
| NFRs fabricated | No — section empty, both candidates → Q-006, Q-007 |
| BRs survive the deletion test | Yes — all five are domain truths |
| No BR restated as acceptance criterion | Verified |

**Verdict: pass**, and it found something.

**Q-008 is a real defect in the source, not in the PRD.** FR-005 and FR-006
require knowledge of the existing ADR collection — to detect overlap and to
supersede correctly — while the skill's own boundary forbids scanning it. The
skill cannot satisfy both. That contradiction is not stated anywhere in
architecture-toolkit, and it surfaced only because the template forced FRs and
Constraints to be written down next to each other.

That is the exercise working as intended: reverse engineering found a genuine
boundary defect in a system nobody had documented.

FC-004 recurred — "plausible but unsound reasoning," well-formed output that is
wrong, with no detection mechanism. Same shape as FC-004 in the
architecture-discovery PRD. Two independent AI-driven features, same highest-cost
uncovered failure mode. That is a pattern worth naming, not a coincidence.

## Test 3 — Update

**Input:** Test 2's PRD. FR-002 modified, FR-003 removed, one requirement added.

**Output:** changed-ID summary plus modified sections.

| Check | Result |
|---|---|
| Modified requirement kept its ID | Yes — FR-002 |
| Removed requirement retired, not deleted | Yes — FR-003 marked retired |
| Retired ID reused for the new requirement | **No** — new became FR-007 |
| Gap backfilled | No — FR-003 stays empty permanently |
| Survivors renumbered | No — FR-004…FR-006 untouched |
| Downstream impact reported | Yes — FC-002 retired with its referent, Q-004 closed |

**Verdict: pass.** The temptation the test was built to expose — recycling
FR-003 to produce a tidy sequence — was refused. Tidiness is not worth silently
redefining an ID that external ADRs may cite.

Conformance re-run after the update: ten of ten, against the ten-rule contract
in force at the time.

## Findings

### F-008: The template cannot express a retired requirement

`Status` exists on the PRD as a whole, not per requirement. Retiring FR-003
required inventing a `**Status:** Retired` field and a disposition note on the
spot — the same improvisation that produced F-006 (`Governed by`).

R-001 says an ID is retired rather than freed, but nothing defines what a
retired requirement *looks like* in the document. Without that, every author
invents a different form and the Update operation is unrepeatable.

**Recommendation:** define retirement in the template — a per-requirement status
of `active` (default, implicit) or `retired`, with a retirement date and reason.
This is a contract change and needs the same validation cycle.

### F-009: Retiring a requirement can orphan a goal or story, and no rule covers it

Retiring FR-003 left US-003 with no implementing requirement and stripped G-001
of one of two. I reported this rather than deleting the orphaned story, because
deleting it would hide that the goal lost coverage — but that decision was mine,
not the contract's.

**Recommendation:** add a rule. Proposed: *retirement reports its coverage
effects; orphaned goals and stories are flagged, never silently removed.* This
is exactly the check a Product Reviewer should run, and it is deterministic
enough to belong in the engine.

### F-010: Mode A with no interview — acceptable output or refusal?

Test 1 produced a conformant PRD that is 90% Open Questions. Per the guiding
principle that is a success. But the contract does not say whether the engine
should *emit* such a document or refuse and run the interview first.

Both are defensible. Emitting makes the unknowns visible as an artifact the user
can answer against; refusing avoids a document that looks like progress and is
not. The engine currently emits, because nothing told it otherwise.

**This needs a decision, not a default.** It is the most likely place for the
engine to feel broken in real use.

### F-011: FC-004 recurs across AI-driven features

Both reverse-engineered PRDs independently produced the same system-level
failure condition: coherent, complete, confident output that is materially
wrong, with no detection mechanism. Highest cost, least coverage, in both.

Not a template defect — the template surfaced it correctly, twice. But it
suggests AI-driven features share a failure mode the contract could prompt for
explicitly rather than rediscovering each time.

**Recommendation:** consider a note in the Failure Conditions guidance asking
whether the feature can fail *confidently*. Cheap, and it would have surfaced
this in pass one rather than pass three.

## Status

| Test | Result |
|---|---|
| Mode A Create | Pass — no fabrication under a one-line brief |
| Mode B Recover | Pass — and found a real defect in the analyzed system |
| Update | Pass — ID stability held against the tidiness temptation |

Contract violations found: **none**. The engine executed all ten rules then in
force across three operations.

Open items: F-008 (retirement form — blocks repeatable Update), F-009 (orphan
rule), F-010 (Mode A emit-vs-refuse decision), F-011 (optional prompt).

F-008 and F-010 should be settled before the engine is used for real work.
F-009 and F-011 are improvements.

---

# Resolutions

## F-008 — resolved: R-011 requirement lifecycle

Retirement now has a defined form: `Status: Retired`, date, reason, optional
`Replaced by:`. The original text is **preserved in full**; the entry stays in
the document permanently.

Test 3 was rerun against the defined form. The substantive difference from the
improvised version was not formatting — it was that the improvisation had
**summarized FR-003 out of existence**. An ADR citing FR-003 would have learned
it was retired but not what it had required. That is precisely the traceability
R-001 exists to protect, and it was quietly lost while every check still passed.

A rule that says "the ID is retired, not freed" is not sufficient on its own.
Without a defined form, the ID survives and its meaning does not.

## F-009 — resolved: R-012 coverage effects

Retirement reports every orphaned goal, story, and failure condition. Nothing is
deleted automatically. A user story left with no implementing requirement is
reported as orphaned rather than removed, because removing it would hide that a
goal lost coverage — which is the fact worth knowing.

The engine reports; the human decides.

## F-010 — resolved: emit, never block

The engine does not withhold a PRD pending an interview. It emits with gaps
recorded and `Confidence: Low`.

Blocking would have violated the guiding principle while appearing rigorous:
it gives the user nothing to react to, and answering questions in the abstract
is harder than correcting a draft. A question-rich PRD *is* the interview, in a
form the user can answer against.

`Discovery` (Initial / Confirmed) and `Confidence` (Low / Medium / High) are now
header fields, so an incomplete PRD is explicitly incomplete rather than quietly
thin. A Low-confidence PRD is a valid deliverable. A High-confidence marking on
guessed content is the failure being prevented.

One exception kept: genuine ambiguity about *what is being asked* — Mode A vs
Mode B, or which system is the subject — is still asked before writing, since
guessing wrong wastes the entire document.

## F-011 — deferred: `docs/product/BACKLOG.md` B-001

Not a template gap. R-007 now prompts for it directly — *can this feature fail
confidently?* — which would have surfaced it in pass one rather than pass three.
Detecting whether a PRD is *itself* confidently wrong needs semantic judgement
and belongs to `product-reviewer`.

## Status after resolutions

| Finding | Resolution |
|---|---|
| F-008 Retirement form | R-011 added; Test 3 rerun passes |
| F-009 Orphan handling | R-012 added |
| F-010 Mode A blocking | Emit with Discovery/Confidence; never block |
| F-011 Confident-wrong detection | Deferred to B-001; R-007 prompt added |

Contract is now twelve rules. Test 3 rerun: twelve of twelve.

Phase 1 complete.
