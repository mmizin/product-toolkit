# Product Toolkit

## Purpose

Help teams create, maintain and evolve product documentation as **structured,
machine-consumable artifacts** — not prose.

Product Toolkit produces the canonical description of *what* a system must do.
It does not decide *how* the system is built.

## Scope

In scope:

- Create PRD for new functionality
- Reverse engineer PRD from existing systems
- Validate requirements (completeness, consistency, testability)
- Maintain product knowledge over time

Out of scope (v0.1):

- Roadmap management
- Personas and user research
- Analytics and metrics collection
- Backlog / ticket management

Out of scope (permanently):

- Architecture decisions
- Technology selection
- Implementation guidance

## Boundaries with Architecture Toolkit

Product Toolkit and [Architecture Toolkit](https://github.com/mmizin/architecture-toolkit)
are separate tools connected by one shared artifact.

**The dependency is one-directional and optional.** Product Toolkit is complete
on its own: a PRD is a useful artifact whether or not anything downstream reads
it. Architecture Toolkit is a consumer, not a requirement — nothing in this
repository imports it, checks for it, or degrades without it. Designing for a
consumer is not the same as depending on one.

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

| Concern | Owner |
|---|---|
| Problem, users, requirements | Product Toolkit |
| Context, containers, decisions | Architecture Toolkit |
| Acceptance criteria | Product Toolkit |
| Quality scenarios | Architecture Toolkit (derived from NFRs) |

The handoff is one-directional. Architecture Toolkit **reads** the PRD; it never
writes to it. If architecture work reveals a requirements gap, the fix belongs
in the PRD, authored by Product Toolkit.

## The contract

`templates/PRD.md` is the contract between the two toolkits. It is not a
document template — it is an interface definition.

Consequences:

- Every requirement carries a stable ID (`FR-001`, `NFR-001`, `BR-001`).
  IDs are referenced from ADRs and must never be reused or renumbered.
- Unknowns are explicit. An absent answer is recorded as an Open Question or an
  Assumption, never silently omitted.
- Functional Requirements are the input to arc42 Context. They must be
  expressible without reference to implementation.
- Non Functional Requirements are the input to architecture quality scenarios.
  They must be measurable.

Changing the template is a breaking change for Architecture Toolkit.

The template defines the shape; `references/PRD-CONTRACT-RULES.md` defines the
rules the engine must enforce. A PRD with every section filled is not
conformant — classification and relationship rules are the part a generator
violates while producing a document that looks complete.

## Guiding principle

**Honest incompleteness is better than fabricated completeness.**

A thin PRD carrying fifteen genuine Open Questions is a success: it has told you
exactly what nobody knows yet. A polished PRD where an unmeasurable NFR received
a plausible invented number is the failure mode — and it is dangerous precisely
because it reads as success.

This matters more for AI-generated documentation than for hand-written, because
fabrication is cheap, fluent, and indistinguishable from knowledge at a glance.
Every rule in `PRD-CONTRACT-RULES.md` exists to make the honest output the
easier one to produce.

## Repository vs Output Boundary

Two different repositories are involved, and confusing them corrupts the model
of what this tool is.

The **Product Toolkit repository** contains the tool:

```
product-toolkit/
├── templates/PRD.md               ← output shape        (runtime)
├── references/PRD-CONTRACT-RULES.md ← governance        (runtime)
├── skills/prd-engine/             ← the implementation
├── docs/product/                  ← human-facing only
└── docs/examples/<name>/PRD.md    ← test fixtures, not features
```

`templates/` and `references/` are loaded by the skill on every operation.
`docs/` is never read at execution time. A runtime dependency living under
`docs/` would break silently when documentation is pruned.

The **consuming project's repository** contains the output:

```
consumer-project/
└── docs/features/<feature>/PRD.md
```

Rules:

- Generated PRD artifacts belong to the consuming project, never to this one.
- Product Toolkit MUST NOT treat its own examples as product features. Anything
  under `docs/examples/` is a validation fixture.
- `docs/features/<feature>/PRD.md` is the contract path **in the consuming
  project**. Inside this repository that path has no meaning and must not exist.

Product Toolkit knows the *schema* `docs/features/<feature>/PRD.md`. It does not
know, and must not encode, any specific feature. The contract defines the shape;
the consumer supplies the content.

## Main capabilities

```
Product Toolkit

├── PRD Management
│
├── Product Discovery
│
├── Requirements Management
│
└── Product Review
```

## v0.1 — Product Requirement Engine

The first capability is not a generator. It is an engine over one artifact:

- **Create** — PRD for functionality that does not exist yet
- **Recover** — PRD reverse engineered from an existing system
- **Update** — revise an existing PRD while preserving ID stability
- **Validate** — conformance against the contract rules

Generation is one of four operations, which is why the capability is named
Product Requirement Engine rather than PRD Generator.

## Build order

```
Phase 0 — Contract         VISION.md, templates/PRD.md,
                           references/PRD-CONTRACT-RULES.md      ✅
Phase 1 — Engine           .claude-plugin/, skills/prd-engine/   ✅
Phase 2 — Review           agents/product-reviewer.md            ✅
```

The reviewer comes last by design: a reviewer can only check against a standard
that already exists. It owns semantic judgement only — contract validation stays
in the engine.

## Output layout

```
docs/
    features/
        payments/
            PRD.md
        authentication/
            PRD.md
```

One feature, one directory, one PRD. The path is part of the contract —
Architecture Toolkit resolves features by directory name.