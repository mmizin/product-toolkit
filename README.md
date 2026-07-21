# Product Toolkit

A Claude Code **plugin** for product requirements. It creates PRDs for new
functionality, reverse engineers them from systems that already exist, and
validates them against a fixed contract — so the output is a structured artifact
architecture tooling can consume, not prose.

> **Status: v0.1 — contract defined, engine in progress.** The PRD contract and
> its enforcement rules are complete and validated against a real system. The
> `prd-engine` skill is being implemented against them.

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

The PRD is not a document template — it is the **interface** between the two
toolkits. [Architecture Toolkit](https://github.com/mmizin/architecture-toolkit)
reads it; it never writes to it.

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
| `prd-engine` | Create, recover, and validate PRDs against the contract. |

Named an engine rather than a generator because generation is one of four
operations:

| Operation | Input | Output |
|---|---|---|
| Create | A feature description | PRD (Mode A) + Open Questions |
| Recover | An existing system | PRD (Mode B) + Open Questions |
| Update | An existing PRD + what changed | Revised PRD + changed-ID summary |
| Validate | An existing PRD | Conformance report against R-001…R-010 |

**Validation here means contract compliance, not product review.** "NFR-003 has
no threshold" is a contract finding. "NFR-003's threshold is unrealistic"
requires domain judgement and belongs to the future `product-reviewer` agent.
If a check needs judgement to answer, it is not a contract check.

### Contract
| Artifact | Role |
|---|---|
| `templates/PRD.md` | The shape a PRD takes |
| `docs/product/PRD-CONTRACT-RULES.md` | The rules the engine enforces (R-001…R-010) |
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
├── templates/
│   └── PRD.md                          ← the contract
├── docs/
│   ├── product/
│   │   ├── VISION.md
│   │   └── PRD-CONTRACT-RULES.md
│   └── examples/
│       └── architecture-discovery/     ← validation fixture, not a feature
└── README.md
```

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

- **v0.1** — PRD contract, enforcement rules, `prd-engine` (this release).
- **v0.2** — `product-reviewer` agent for semantic review (are the goals
  meaningful, requirements non-contradictory, assumptions safe, metrics
  realistic); deeper Mode B discovery. Contract validation stays in
  `prd-engine`.
- **v0.3+** — requirements validation across multiple PRDs; explicit handoff
  verification with Architecture Toolkit.

Deliberately not in scope: roadmaps, personas, user research, analytics,
backlog management.

## License

MIT
