# Architecture Discovery

<!--
Mode B (Existing Feature) PRD, reverse engineered from
github.com/mmizin/architecture-toolkit @ v0.1, skills/architecture-discovery/SKILL.md.

Evidence base: repository source only. No users, no usage data, no telemetry,
no issue tracker were available. Every statement is either observed in the
repository or recorded as an Assumption / Open Question. Nothing here is
inferred from what a discovery tool "should" do.
-->

| Field | Value |
|---|---|
| Status | Draft |
| Mode | Existing Feature |
| Owner | Unassigned — see Q-008 |
| Last updated | 2026-07-21 |

## Overview

`architecture-discovery` is a skill in the Architecture Toolkit Claude Code
plugin. It reconstructs a project's actual architecture from evidence in its
repository and emits a discovery report: system overview, component inventory,
data flows, candidate ADRs, and documentation gaps.

It is the grounding step of the toolkit. The report is the preferred input for
the authoring skills (`adr-expert`, `c4-expert`, `arc42-expert`), which
otherwise work from whatever the user hands them directly.

## Current Behavior

Observed in `skills/architecture-discovery/SKILL.md` at v0.1.

The skill is a Markdown instruction document loaded into a Claude Code session.
It has no executable code, no configuration, and no state. Its behavior is
whatever the model does when following it, which means every requirement below
is an instruction to a model, not a guarantee enforced by a program (see
A-001).

The defined workflow is five sequential steps:

1. **Inventory** — detect languages, package managers, frameworks, build tools,
   containers, CI, infra config; read manifests and lockfiles first.
2. **Map structure** — identify top-level modules/services, responsibilities,
   dependency direction, data stores, queues, external APIs, auth boundaries.
3. **Identify candidate decisions** — surface architectural choices visible in
   the repo that lack an ADR, marked as candidates requiring confirmation.
4. **Assess doc gaps** — compare existing docs against what the discovered
   architecture warrants; obvious coverage gaps only.
5. **Report** — emit the discovery report.

The skill carries the plugin's three-part boundary block (`Responsible for` /
`Not responsible for` / `Escalates to`) required by
`references/plugin-design-principles.md`, and declares five escalation targets.

Its own status line reads: "Initial implementation — structure and workflow are
defined; deepen during use."

## Problem Statement

A team working on an unfamiliar or under-documented codebase cannot answer what
the system's architecture actually is without reading the whole repository.
Architectural decisions get made and never recorded, so the reasoning behind
them is lost while the consequences persist. Where documentation does exist, it
drifts from the code silently — nothing detects the divergence.

The cost lands on the people least able to absorb it: newcomers onboarding, and
anyone who must change a system whose constraints are undocumented.

## Goals

- G-001: Establish what a system's architecture is, from evidence, without
  requiring prior knowledge of the codebase.
- G-002: Surface architectural decisions that were made but never recorded.
- G-003: Produce output that the authoring skills can consume directly, so
  discovery grounds documentation rather than duplicating it.
- G-004: Keep observation and inference distinguishable, so the report can be
  trusted without re-verifying the entire codebase.

## Non Goals

- Judging whether the discovered architecture is good (`architecture-reviewer`).
- Creating ADRs, C4 diagrams, or arc42 sections (authoring skills).
- Designing a target architecture — the toolkit documents architecture, it does
  not design it.
- Sequencing the documentation work implied by discovered gaps
  (`architecture-bootstrap`).
- Auditing consistency across existing documentation artifacts
  (`architecture-librarian`).

All five are stated explicitly in the source. This section is observed, not
inferred.

## Users / Actors

| Actor | Type | Description |
|---|---|---|
| Developer onboarding | Human | Needs to understand an unfamiliar codebase. |
| Architect / tech lead | Human | Suspects undocumented decisions; wants drift detected. |
| Claude Code runtime | System | Loads the skill, executes the workflow, holds the session. |
| Target repository | System | The subject under analysis. Read-only input. |
| Sibling skills | System | Consume the discovery report — `adr-expert`, `c4-expert`, `arc42-expert`, `architecture-bootstrap`. |
| Reviewer agents | System | `architecture-reviewer`, `architecture-librarian` — escalation targets. |

The two human actors are inferred from the skill's "When to use" section, which
names onboarding, undocumented projects, and suspected unrecorded decisions. No
real user has been observed (Q-001).

## User Stories

- US-001: As a developer onboarding, I want the system's components and their
  responsibilities inventoried, so that I can navigate the codebase without
  reading all of it.
- US-002: As an architect, I want architectural choices that lack an ADR
  surfaced as candidates, so that I can decide which deserve recording.
- US-003: As an architect, I want inference separated from observation with the
  evidence stated, so that I can trust the report without re-verifying it.
- US-004: As a user of the toolkit, I want the report shaped so the authoring
  skills can work from it, so that I do not restate findings by hand.
- US-005: As a returning user, I want a re-scan to expose divergence between the
  documented architecture and the code, so that drift is detected.

## Functional Requirements

### FR-001: Technology inventory

- **Description:** Detect languages, package managers, frameworks, build tools,
  containers, CI, and infrastructure config. Read manifests and lockfiles before
  drawing conclusions from source.
- **Actor:** Developer onboarding, Architect
- **Priority:** Must
- **Acceptance Criteria:**
  - Given a repository with a dependency manifest, when discovery runs, then the
    manifest is read before source-level inference is used.
  - Given detected technologies, when the report is produced, then each is named
    with the file that evidenced it.
- **Related:** US-001, G-001

### FR-002: Structural mapping

- **Description:** Identify top-level modules and services, their
  responsibilities, and the direction of dependencies. Note data stores, queues,
  external APIs, and auth boundaries.
- **Actor:** Developer onboarding, Architect
- **Priority:** Must
- **Acceptance Criteria:**
  - Given a repository, when structure is mapped, then each identified unit has
    a stated responsibility and its dependency direction.
  - Given identified units, when they are reported, then each names the evidence
    that established its boundary.
- **Governed by:** BR-002
- **Related:** US-001, G-001

### FR-003: Candidate decision identification

- **Description:** Surface architectural choices visible in the repository that
  may represent intentional decisions and lack an ADR. Mark each as a candidate
  requiring confirmation.
- **Actor:** Architect
- **Priority:** Must
- **Acceptance Criteria:**
  - Given an architectural choice with no corresponding ADR, when discovery
    runs, then it is reported as a candidate ADR, never as a settled decision.
  - Given multiple candidates, when they are reported, then each carries the
    repository evidence that surfaced it.
- **Governed by:** BR-001, BR-003
- **Related:** US-002, G-002

### FR-004: Documentation gap assessment

- **Description:** Compare existing documentation against what the discovered
  architecture warrants, and report obvious coverage gaps.
- **Actor:** Architect
- **Priority:** Must
- **Acceptance Criteria:**
  - Given discovered architecture and existing docs, when gaps are assessed,
    then missing coverage is reported as an unordered inventory.
  - Given a set of gaps, when they are reported, then each names the discovered
    architecture element that warrants the missing documentation.
- **Governed by:** BR-004
- **Related:** US-005

### FR-005: Discovery report emission

- **Description:** Emit a report containing system overview, component
  inventory, data flows, candidate ADRs ranked by significance, and doc gaps.
- **Actor:** All human actors, sibling skills
- **Priority:** Must
- **Acceptance Criteria:**
  - Given a completed scan, when the report is emitted, then all five sections
    are present.
  - Given observed and inferred data flows, when both exist, then they are
    reported in separate groupings.
  - Given multiple candidate ADRs, when they are listed, then they are ordered
    by significance.
- **Related:** US-001, US-004, G-003

### FR-006: Evidence attribution for inferences

- **Description:** Every inference is marked as an inference and states the
  evidence that produced it. Observation and inference remain distinguishable
  throughout the report.
- **Actor:** All human actors
- **Priority:** Must
- **Acceptance Criteria:**
  - Given any statement not directly observed in the repository, when it appears
    in the report, then it is marked as inferred and cites its evidence.
  - Given an inference, when the report is delivered, then the user is prompted
    to verify it.
- **Governed by:** BR-001
- **Related:** US-003, G-004

### FR-007: Escalation to sibling modules

- **Description:** Hand off to the module that owns work falling outside this
  skill's responsibility: candidate decisions to `adr-expert`, structure to
  `c4-expert`, full narrative to `arc42-expert`, quality judgement to
  `architecture-reviewer`, cross-artifact consistency to
  `architecture-librarian`, zero-to-documented sequencing to
  `architecture-bootstrap`.
- **Actor:** Sibling skills, Reviewer agents
- **Priority:** Must
- **Acceptance Criteria:**
  - Given a need that this skill's Not-responsible-for block names, when it
    arises, then the named owning module is handed off to.
  - Given a need for more context only, when no ownership boundary is crossed,
    then no escalation occurs.
  - Given a handoff, when it is made, then the receiving module is named
    explicitly rather than implied.
- **Governed by:** BR-005
- **Related:** G-003

## Failure Conditions

Reverse engineered from the skill's stated behavior. The source defines these
only implicitly — the skill says what to do, rarely what to do when it cannot.
That asymmetry is itself the finding (see FINDINGS.md, F-001).

### FC-001: No architectural evidence found

- **Applies to:** FR-001, FR-002
- **Condition:** The repository has no manifests, no recognizable structure, or
  is too small to exhibit architecture.
- **Expected behavior:** Report that architecture could not be established and
  name what was searched for. Not defined in the source — see Q-011.

### FC-002: Repository exceeds session capacity

- **Applies to:** (system-level)
- **Condition:** The repository cannot be read within the session's context
  budget.
- **Expected behavior:** Undefined in the source. Partial-scan behavior,
  prioritization, and whether the user is told coverage was incomplete are all
  unspecified — see Q-003, Q-012.

### FC-003: Evidence supports contradictory structures

- **Applies to:** FR-002, FR-003
- **Condition:** Manifests, deployment config, and source layout imply different
  component boundaries.
- **Expected behavior:** Report the contradiction with both readings and their
  evidence rather than silently choosing one. Not defined in the source — see
  Q-011.

### FC-004: Confident wrong output

- **Applies to:** (system-level)
- **Condition:** The report is internally coherent and fully populated but
  materially wrong about the architecture.
- **Expected behavior:** No detection mechanism exists. BR-001 and FR-006
  mitigate by making provenance checkable, but nothing catches this failure —
  the user must. This is the failure mode with the highest cost and the least
  coverage.

## Non Functional Requirements

**No NFR in this section is currently measurable, and that is the single
largest finding of this exercise.** The skill has no runtime, no telemetry, and
no observed usage, so no threshold can be evidenced from the repository. Per the
template's own rule — a number-less NFR is an Open Question, not an NFR — the
candidates below are recorded as questions Q-002 to Q-005 rather than
fabricated with invented numbers.

### NFR-001: Report trustworthiness

- **Category:** Correctness
- **Requirement:** Unverified. The intended property is that no statement in the
  report is asserted without either observation or cited evidence. No threshold
  or measurement method exists.
- **Measurement:** None defined — see Q-002.
- **Priority:** Must

### NFR-002: Scan tractability on large repositories

- **Category:** Performance
- **Requirement:** Unverified. The workflow requires reading manifests and
  mapping structure across an arbitrary repository within one session's context
  budget. No size limit, timeout, or degradation behavior is defined.
- **Measurement:** None defined — see Q-003.
- **Priority:** Should

### NFR-003: Read-only safety on the target repository

- **Category:** Security
- **Requirement:** Unverified. The skill is understood to be read-only against
  the analyzed repository. This is nowhere stated in the source.
- **Measurement:** None defined — see Q-004.
- **Priority:** Must

## Business Rules

### BR-001: Never invent architecture

- **Rule:** What is observed in the code and what is inferred are always
  distinguishable. Inferences state their evidence and are verified with the
  user.
- **Rationale:** A discovery report whose provenance is unclear must be
  re-verified in full, which destroys its value.

### BR-002: Folder structure is not deployment structure

- **Rule:** Runtime and service boundaries are not assumed from directory layout
  alone.
- **Rationale:** `src/payment/` and `src/user/` may be two packages in one
  deployable, not two services.

### BR-003: A dependency is not a decision

- **Rule:** Using a library is not architectural; choosing an architectural
  style is. Not every dependency becomes a candidate ADR.
- **Rationale:** Promoting every import produces a candidate list too noisy to
  act on.

### BR-004: Report evidence, not plans

- **Rule:** Documentation gaps are reported as an unordered inventory. Ordering
  the work belongs to `architecture-bootstrap`.
- **Rationale:** Preserves single responsibility between the two modules.

### BR-005: Escalate at most once per condition

- **Rule:** Escalation happens only when an ownership boundary is crossed, once
  per distinct question. An artifact returned unchanged is never re-escalated;
  the ambiguity goes to the user.
- **Rationale:** Escalation edges in this plugin are intentionally
  bidirectional; the stopping rule, not acyclicity, is what prevents loops.

## Data Requirements

| Entity | Owned by | Retention | Sensitivity |
|---|---|---|---|
| Target repository contents | external | not stored by this feature | inherits the repository's own classification — see Q-006 |
| Discovery report | this feature | undefined — see Q-007 | inherits from analyzed repository |
| Candidate ADR list | this feature | part of the report | internal |
| Existing documentation | external | read-only | internal |

The skill defines no persistence. Whether a discovery report is written to disk,
where it lives, and whether it is committed are all undefined in the source
(Q-007). This matters for the Product Toolkit contract, which by contrast fixes
`docs/features/<name>/PRD.md` as a path.

## Integration Requirements

| System | Direction | Purpose | Criticality |
|---|---|---|---|
| Claude Code runtime | inbound | Loads and executes the skill | blocking |
| Target repository | inbound | Evidence source | blocking |
| `adr-expert` | outbound | Receives candidate decisions | degraded |
| `c4-expert` | outbound | Receives discovered boundaries | degraded |
| `arc42-expert` | outbound | Receives structure for the narrative | degraded |
| `architecture-bootstrap` | bidirectional | Grounds itself in discovery; receives gap inventory | degraded |
| `architecture-reviewer` | outbound | Quality judgement | optional |
| `architecture-librarian` | outbound | Cross-artifact consistency audit | optional |
| `references/terminology.md` | inbound | Shared vocabulary (candidate ADR, drift) | degraded |

Outbound integrations are conceptual. The README states plainly that Claude Code
skills cannot call each other programmatically — the toolkit is independent
modules with a recommended order, not a runtime orchestrator. Every "integration"
here is a documented handoff a human or model performs, not a call.

## Constraints

- C-001: Must run inside the Claude Code plugin environment.
- C-002: Skills cannot invoke each other programmatically. Handoffs are
  recommendations, not calls.
- C-003: The skill is Markdown instructions only. No executable code,
  configuration, or persistent state.
- C-004: Analysis is bounded by the session's context window; the repository
  cannot be assumed to fit.
- C-005: Must carry the three-part boundary block required by
  `references/plugin-design-principles.md`.
- C-006: Naming must follow the `architecture-<verb>` form for cross-cutting
  activities.
- C-007: The toolkit documents architecture; it never designs it.

## Assumptions

| ID | Assumption | Impact if wrong | Owner |
|---|---|---|---|
| A-001 | Instructions in SKILL.md are followed with enough consistency to treat them as requirements. | Every FR here is aspirational, not behavioral; acceptance criteria are unfalsifiable. | Unassigned |
| A-002 | The two human actors are real users of the skill. | Requirements are shaped around users who do not exist. | Unassigned |
| A-003 | Reading a repository is sufficient evidence for architecture; runtime observation is not needed. | Discovery systematically misses runtime-only architecture (dynamic wiring, deployment topology, feature flags). | Unassigned |
| A-004 | The discovery report is useful to the authoring skills in its current unstructured prose form. | G-003 fails silently — handoff requires manual restatement, which is the cost it exists to remove. | Unassigned |
| A-005 | The skill is read-only against the analyzed repository. | A discovery scan could modify the system under analysis. | Unassigned |

## Open Questions

| ID | Question | Blocks | Owner | Status |
|---|---|---|---|---|
| Q-001 | Has anyone outside the author used this skill? | US-001–005, A-002 | | open |
| Q-002 | How is report trustworthiness measured? | NFR-001 | | open |
| Q-003 | What repository size can be scanned within one session, and what happens past it? | NFR-002, C-004 | | open |
| Q-004 | Is read-only access against the target repository stated or enforced anywhere? | NFR-003, A-005 | | open |
| Q-005 | What makes a discovery run a failure rather than a low-quality success? | all NFRs | | open |
| Q-006 | May a discovery report contain secrets or PII read from the target repository? | Data Requirements | | open |
| Q-007 | Is the discovery report persisted, and if so where? | Data Requirements, G-003 | | open |
| Q-008 | Who owns this feature? | Status | | open |
| Q-009 | Does the report need a defined schema for the authoring skills to consume it reliably? | G-003, A-004 | | open |
| Q-010 | How is drift detection triggered — is a re-scan manual? | US-005 | | open |
| Q-011 | What does the skill do when evidence is absent or contradictory? | FC-001, FC-003 | | open |
| Q-012 | Is the user told when repository coverage was incomplete? | FC-002 | | open |

## Success Metrics

| Metric | Baseline | Target | Source |
|---|---|---|---|
| Time to first useful architecture understanding on an unfamiliar repo | unknown | unknown | not instrumented |
| Candidate ADRs confirmed as real decisions vs. rejected as noise | unknown | unknown | not instrumented |
| Discovery reports consumed by an authoring skill without manual restatement | unknown | unknown | not instrumented |
| Inferences later corrected by the user | unknown | unknown | not instrumented |

No metric has a baseline. The plugin has no telemetry and no observed usage.
The second and fourth rows are the ones worth instrumenting first — the
candidate-ADR signal-to-noise ratio directly tests BR-003, and the correction
rate directly tests BR-001.

## Architecture Impact

- Requirements likely to drive decisions: FR-005 and FR-006 (report structure
  and provenance), FR-007 (escalation topology).
- Suspected boundaries: the report is the interface between discovery and every
  authoring module. It has no defined schema, which makes it the toolkit's
  least specified and most load-bearing artifact (Q-009).
- Known architectural risk: C-002 means the toolkit's composition is
  convention-enforced, not runtime-enforced. Every handoff is a place where the
  chain can break without any error surfacing.
