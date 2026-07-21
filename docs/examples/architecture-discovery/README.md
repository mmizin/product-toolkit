# PRD Template Validation Example

**Purpose:** validate that `templates/PRD.md` can describe a real system without
forcing invented information.

**This is a test fixture. It is not a Product Toolkit feature.**

`architecture-discovery` is a skill belonging to a *different* product —
[Architecture Toolkit](https://github.com/mmizin/architecture-toolkit). It was
reverse engineered here (template Mode B) because it is the consumer on the
other side of the PRD contract, so a PRD of it exercises the handoff from both
ends.

## What this exercise tests

The question is not "is this a good PRD?" but "does the template hold up against
a real system?" Four signals, per `docs/product/VISION.md`:

| Signal | Meaning |
|---|---|
| "we don't know" | Template working correctly — that's an Open Question |
| "nowhere to put this" | Template gap |
| "this is architecture, not product" | Boundary between toolkits is wrong |
| "could go in two places" | Ambiguity — worse than a gap, it doesn't announce itself |

Findings from this exercise are recorded in [FINDINGS.md](./FINDINGS.md), not
applied to the template directly. The template is not modified unless a real gap
is confirmed.

## Do not

- Treat this directory as product scope.
- Create `docs/features/` in this repository — that path belongs to consuming
  projects only.
- Update this PRD to track changes in Architecture Toolkit. It is a snapshot
  taken at v0.1 for a one-time validation, not maintained documentation. The
  Architecture Toolkit repository is authoritative; this is a frozen reading of
  it. A later validation adds a new example rather than mutating this one.
