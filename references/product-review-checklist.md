# Product Review Checklist

The bar `product-reviewer` judges a PRD against. Semantic quality only —
contract conformance is `prd-engine`'s job (R-001…R-012) and is assumed to have
passed before review begins.

The division, restated because it is the thing most likely to blur:

| | Question | Nature |
|---|---|---|
| `prd-engine` | Does this document obey the contract? | Deterministic |
| `product-reviewer` | Are these good product decisions? | Judgement |

"NFR-003 has no threshold" is a contract finding and must not appear here.
"NFR-003's threshold of 50ms is unachievable for a cross-region call" is a
review finding. If a check can be answered without domain knowledge, it does
not belong in this file.

## Dimension 1 — Problem quality

- Does the Problem Statement describe a real problem experienced by a real
  actor, or does it restate the solution as a problem? ("Users cannot export to
  Markdown" is a missing feature, not a problem.)
- Is the problem stated in user or business terms rather than implementation
  terms?
- Would solving it plausibly matter to someone named in Users / Actors?
- Do the Goals follow from the problem, or do they describe activity instead of
  outcome?

## Dimension 2 — Requirement quality

- Is each Functional Requirement testable? Could two engineers disagree about
  whether it is met?
- Do acceptance criteria state observable outcomes rather than internal
  behavior?
- Are requirements independent, or does one silently presuppose another?
- Do any two requirements contradict each other? This is the check the engine
  explicitly cannot make.
- Are NFR thresholds achievable given the Constraints? A threshold that
  conforms to the contract can still be impossible.

## Dimension 3 — Scope

- Are Goals and Non Goals actually distinguishing? A Non Goals section that
  excludes only the obviously irrelevant does no work.
- Does anything in Functional Requirements fall outside the stated Goals?
- Does any Goal have no implementing requirement? (After an update, an orphaned
  goal is reported by R-012 — assess whether the coverage loss matters.)
- Is the feature coherent, or is it several features that should be separate
  PRDs?

## Dimension 4 — Risk

- Which Assumptions are load-bearing? An assumption whose failure invalidates
  the feature is a different thing from one that costs a rework.
- Is any Assumption stated so vaguely it could not be proven wrong?
- Does any Assumption have no owner, meaning nobody will ever test it?
- Do the Failure Conditions cover how the feature fails *confidently* —
  producing output that is coherent, complete, and wrong? For AI-driven
  features this is usually the highest-cost, least-covered failure mode.
- Are there risks visible in the requirements that no Failure Condition or Open
  Question names?

## Dimension 5 — Completeness of inquiry

Not "are all sections filled" — the engine guarantees that. The question is
whether the *right* unknowns were identified.

- Is anything asserted confidently that nobody could actually know yet?
- Are there obvious questions a domain expert would ask that appear nowhere?
- Do the Open Questions name what they block, and would answering them
  actually unblock it?
- Are Success Metrics measurable in practice, and would they detect failure —
  or only confirm success?

## Dimension 6 — Confidence honesty

- Does the stated `Confidence` match the evidence? A High-confidence PRD resting
  on unanswered questions is the failure this toolkit exists to prevent.
- Is any statement asserted without a source that would need one?
- In Mode B: is inference marked as inference, or has it hardened into stated
  fact?

This dimension exists because fabrication is fluent and reads as knowledge.
Push hardest here.

## Verdict scale

One verdict for the whole PRD:

- **Accept** — sound. Minor recommendations may still be listed.
- **Accept with changes** — the product reasoning holds; specific defects need
  fixing before it drives architecture work.
- **Needs rework** — a dimension fails materially. Building on it would waste
  the downstream effort.
- **Cannot assess** — the PRD does not provide enough to evaluate. Say what is
  missing rather than forcing a verdict. A Low-confidence PRD full of honest
  Open Questions is often correctly *Cannot assess* — that is not a criticism
  of the document.

## What a finding must contain

Every Defect, Possible defect, and Gap states three things. A finding missing
any of them is not reportable:

- **Evidence** — the specific requirement IDs or statements it rests on.
- **Impact** — what goes wrong if it is not addressed.
- **Recommendation** — what the author can actually do about it.

```
Weak:    Scope is unclear.

Usable:  Evidence — FR-005 requires a comprehensive report and FR-006 requires
                    per-inference evidence; C-004 caps both at one session.
         Impact   — For any repository large enough to need the feature, the
                    requirements cannot all be met. Which degrades first is
                    unspecified.
         Recommendation — Decide the degradation order and record it as an
                    acceptance criterion rather than an Open Question.
```

This exists to prevent volume-driven review. A finding without impact is an
observation; a finding without a recommendation is a complaint. Both inflate a
report while making it less useful, and both are easy to generate in quantity.

Recommendations and Info findings are exempt — they carry no impact claim by
definition. If a Recommendation would need an impact statement to be
convincing, it is a Gap that was misclassified.

Prefer three findings that meet this bar to ten that do not. Report count is not
a measure of review quality.

## Finding taxonomy

Classify each finding by kind, and Defect/Gap findings by severity.

**Kinds**

- **Defect** — a confirmed problem in the product reasoning.
- **Possible defect** — something looks wrong, but a legitimate explanation you
  cannot rule out may exist. Report it as this, not as a defect.
- **Gap** — a question, risk, or requirement that should be present and is not.
- **Recommendation** — no defect; a concrete improvement.
- **Info** — worth noting, no action.

**Severity** (Defect and Gap only)

- **Critical** — the feature as specified would not solve the stated problem,
  or a load-bearing assumption is untested and unowned.
- **High** — requirements contradict, or a goal has no coverage.
- **Medium** — a real gap that does not invalidate the work.
- **Low** — cosmetic or stylistic.

**Validation principle.** Before calling something a Defect, rule out: a
constraint stated elsewhere you have not seen, a deliberate scope decision, an
Open Question that already acknowledges it, and domain context the author has
and you do not. When unsure, report a Possible defect and say what is unclear.

A PRD that honestly records what it does not know is not defective for not
knowing it. Absence of an answer is a Gap only when the *question* is missing.
