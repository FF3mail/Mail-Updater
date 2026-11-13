# Architecture Decision Records (ADR)

This directory contains **Architecture Decision Records** for the Mail-Updater project. ADRs capture significant technical and product decisions, the context behind them, and their consequences.

## Relationship to `docs/SPEC.md`

[`docs/SPEC.md`](../SPEC.md) is the **authoritative source of project requirements**. It defines what the system must do, acceptance criteria, and governance for requirement changes.

ADRs complement the specification:

| Document | Role |
|----------|------|
| `docs/SPEC.md` | Requirements, functional behavior, acceptance criteria |
| `docs/adr/` | Recorded decisions that interpret, narrow, or operationalize requirements where the specification leaves room for choice |

An ADR **must not contradict** an approved requirement in `docs/SPEC.md`. If a decision would change requirements, the specification must be updated through its own approval process—not by overriding it in an ADR.

When implementation depends on both a requirement and a decision, cite the relevant SPEC sections and ADR identifiers in plans, compatibility matrices, and runbooks.

## Numbering and naming

- ADRs use sequential numbers: `ADR-001`, `ADR-002`, …
- Numbers are **never reused**. A withdrawn or superseded ADR keeps its number.
- File names: `ADR-<NNN>-<short-kebab-title>.md` (e.g. `ADR-001-database-backend-compatibility.md`).
- The numeric prefix in the filename must match the ADR title inside the document.

Gaps in numbering are acceptable if an ADR was never merged or was renumbered before acceptance. Do not renumber accepted ADRs.

## Status values

Every ADR has exactly one status:

| Status | Meaning |
|--------|---------|
| **Proposed** | Under discussion; not binding for implementation or operations. |
| **Accepted** | Owner-approved; binding until superseded or rejected by a later accepted ADR. |
| **Rejected** | Considered and explicitly not adopted; retained for history. |
| **Superseded** | Replaced by a newer accepted ADR; the superseding ADR must be linked. |

Only **Accepted** ADRs are normative for project work. **Proposed** ADRs may inform design but must not be treated as policy.

## Required sections

Each ADR must include these sections (headings may use Title Case as in the template):

1. **Title** — `ADR-NNN: …` as the document H1.
2. **Status** — One of: Proposed, Accepted, Rejected, Superseded (with link to superseding ADR when applicable).
3. **Date** — Decision date (ISO 8601 calendar date) or last material update for Accepted ADRs.
4. **Context** — Facts, constraints, and problems that motivated the decision.
5. **Decision** — What was chosen; clear and testable where possible.
6. **Consequences** — Positive, negative, and follow-up work (including SPEC or compatibility impacts).
7. **References** — Links to issues, reviews, external docs, and related ADRs.

Optional but encouraged: **Review evidence** (independent reviews, lab notes) clearly labeled as **not** decision authority unless also recorded in **Decision** after owner approval.

## Superseding decisions

To replace an accepted ADR:

1. Create a **new** ADR with the next number (do not edit the old decision in place except for status metadata).
2. In the new ADR, state which ADR(s) it supersedes and why.
3. Update the old ADR’s **Status** to **Superseded** and add a prominent link to the new ADR.
4. Obtain **explicit project owner approval** for the new decision before marking it **Accepted**.

Rejected alternatives may remain documented in the superseded ADR or in the new ADR’s context; do not delete historical files.

## Index

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| [ADR-001](ADR-001-database-backend-compatibility.md) | Database and directory backend compatibility (v1) | Accepted | 2026-11-13 |
