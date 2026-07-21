# Product Toolkit

A Claude Code **plugin** for product requirements. It creates PRDs for new
functionality, reverse engineers them from systems that already exist, and
validates them against a fixed contract — so the output is a structured artifact
architecture tooling can consume, not prose.

> **Status: v0.3 — contract, engine, reviewer, and verified handoff.** Each
> validated against real systems: the contract across three fill passes, the
> engine across four operations, the reviewer across two PRDs, the handoff
> against criteria fixed before the experiment ran.
>
> Not yet validated by anyone who did not build it — see v0.4.

## The idea

Product Toolkit produces the canonical description of *what* a system must do.
It does not decide *how* the system is built.

```
Product Toolkit
        |
        | docs/features/<feature>/PRD.md
        ↓
Architecture Toolkit
        |
        | arc42 + ADR
        ↓
Implementation
```

The PRD is not a document template — it is a structured artifact with stable
requirement IDs, explicit unknowns, and enforced classification rules.

**Product Toolkit is standalone.** It produces PRDs that stand on their own, and
requires nothing else installed.
[Architecture Toolkit](https://github.com/mmizin/architecture-toolkit) is an
**optional downstream consumer**: if you use it, it reads the PRD and never
writes to it. If you do not, nothing here changes. The diagram above shows where
a PRD *can* go, not a dependency.

## Guiding principle

**Honest incompleteness is better than fabricated completeness.**

A thin PRD carrying fifteen genuine Open Questions is a success — it tells you
exactly what nobody knows yet. A polished PRD where an unmeasurable requirement
received a plausible invented number is the failure mode, and it is dangerous
because it reads as success.

## What's inside

### Skills
| Skill | Purpose |
|-------|---------|
| `prd-engine` | Create, recover, update, and validate PRDs against the contract. |

Named an engine rather than a generator because generation is one of four
operations:

| Operation | Input | Output |
|---|---|---|
| Create | A feature description | PRD (Mode A) + Open Questions |
| Recover | An existing system | PRD (Mode B) + Open Questions |
| Update | An existing PRD + what changed | Revised PRD + changed-ID summary |
| Validate | An existing PRD | Conformance report against R-001…R-012 |

### Agents
| Agent | Purpose |
|-------|---------|
| `product-reviewer` | Independent critique of a PRD's product reasoning. Read-only. |

The engine and the reviewer split along one line:

| | Question | Nature |
|---|---|---|
| `prd-engine` | Does this document obey the contract? | Deterministic |
| `product-reviewer` | Are these good product decisions? | Judgement |

*"NFR-003 has no threshold"* is a contract finding. *"NFR-003's 50ms threshold is
unachievable given C-002"* is a review finding. If a check can be answered
without domain knowledge, the reviewer does not make it.

### Contract
| Artifact | Role |
|---|---|
| `templates/PRD.md` | The shape a PRD takes |
| `references/PRD-CONTRACT-RULES.md` | The rules the engine enforces (R-001…R-012) |
| `references/product-review-checklist.md` | The bar the reviewer judges against |
| `docs/product/VISION.md` | Purpose, scope, boundaries |

Filling every section is **not** conformance. The classification and
relationship rules are the part a generator violates while producing a document
that looks complete.

## Layout

```
product-toolkit/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── prd-engine/SKILL.md
├── agents/
│   └── product-reviewer.md
├── templates/
│   └── PRD.md                          ← output shape
├── references/
│   ├── PRD-CONTRACT-RULES.md           ← runtime governance
│   └── product-review-checklist.md
├── docs/
│   ├── product/                        ← human-facing only
│   │   ├── VISION.md
│   │   ├── ENGINE-VALIDATION.md
│   │   ├── REVIEWER-VALIDATION.md
│   │   ├── HANDOFF-VALIDATION.md
│   │   └── BACKLOG.md
│   ├── examples/
│   │   └── architecture-discovery/     ← validation fixture, not a feature
│   └── history/
└── README.md
```

`templates/` and `references/` are **runtime inputs** — the skill loads both on
every operation. `docs/` is for humans and is never read at execution time.
Keeping that split explicit means pruning documentation cannot silently break
the engine.

Note the boundary: PRDs this plugin *generates* live in the consuming project at
`docs/features/<feature>/PRD.md`. Anything under `docs/examples/` here is a test
fixture. This repository never contains a `docs/features/` directory.

## Two modes

| Mode | When | Adds |
|---|---|---|
| **A — New Feature** | The functionality does not exist yet | — |
| **B — Existing Feature** | Reverse engineering a real system | `Current Behavior` section |

Mode B against an undocumented system will legitimately produce an NFR section
that is entirely Open Questions. That output is correct. The engine presents it
as the interview it just generated, not as an incomplete document.

## How the contract was validated

`templates/PRD.md` was filled three times against a real system
(`architecture-discovery`, from Architecture Toolkit v0.1) before any skill was
written. Findings are recorded in
`docs/examples/architecture-discovery/FINDINGS.md`.

That exercise produced the Failure Conditions section, both classification
rules, the two-relationship cap, and the no-inverse-links rule — none of which
were in the original template. Doing it before writing the engine meant those
rules could be made explicit instead of being silently improvised at generation
time.

## Roadmap

- **v0.1** — PRD contract, enforcement rules, `prd-engine`.
- **v0.2** — `product-reviewer` agent for semantic review.
- **v0.3** — handoff verification against Architecture Toolkit (this release).
- **v0.4** — external user validation. No new features: can someone who did not
  build this produce a usable PRD from the README alone? Everything so far was
  validated by its own author against one subject, which is the largest
  untested assumption in the project.
- **v0.5+** — cross-PRD requirements validation; shared handoff contract if
  B-003 justifies one.

Deliberately not in scope: roadmaps, personas, user research, analytics,
backlog management.

## License

MIT
