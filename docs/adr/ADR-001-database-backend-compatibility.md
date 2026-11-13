# ADR-001: Database and directory backend compatibility (v1)

## Status

**Accepted**

Owner approval: 2026-11-13

## Date

2026-11-13

## Context

Mail-Updater must assess whether a source iRedMail environment can be migrated to a selected target (see [`docs/SPEC.md`](../SPEC.md) FR-14 and §3). iRedMail deployments may use different SQL engines and directory services. The specification lists supported SQL and LDAP backends and a source compatibility matrix as open questions (§19).

Without an explicit compatibility policy, implementers and operators could assume cross-backend moves (for example MySQL to MariaDB or SQL to LDAP directory changes) are in scope for version 1. Such moves introduce distinct schema, tooling, and validation risks. The project needs a **pairwise**, evidence-driven policy for v1 that aligns with compatibility assessment and blocks unsafe automation.

An independent review by **Grok** on **2026-11-13** analyzed backend-identification and matrix options. That review informed this record but is **not** the decision authority; the binding policy below is **owner-approved** on the same date.

## Decision

Adopt **Option C: explicit pairwise backend compatibility policy** for Mail-Updater v1.

### Backend identifiers

The following are **separate backend identifiers**. They are not interchangeable for compatibility labeling or matrix keys:

| Identifier | Role |
|------------|------|
| `MySQL` | SQL backend using MySQL |
| `MariaDB` | SQL backend using MariaDB |
| `PostgreSQL` | SQL backend using PostgreSQL |
| `OpenLDAP` | Directory backend using OpenLDAP |

Source and target inventories must record which identifier applies to each relevant component. Compatibility is evaluated **per identifier pair** (source identifier → target identifier).

### v1 scope: same-backend only

For **version 1**:

- Only migration scenarios where the **source and target use the same backend identifier** may be considered for automated support (e.g. `MySQL` → `MySQL`, `OpenLDAP` → `OpenLDAP`).
- **All cross-backend migration scenarios are excluded from v1 without exception** (including but not limited to `MySQL` → `MariaDB`, `MariaDB` → `PostgreSQL`, and any SQL ↔ `OpenLDAP` change).

Cross-backend work remains out of scope until a **separate ADR** is **Accepted** with **explicit owner approval** and any required updates to project requirements through the SPEC governance process.

### Compatibility labels

Each evaluated source→target pair (for a given backend identifier and applicable version context) carries a compatibility label:

| Label | Meaning |
|-------|---------|
| **SUPPORTED** | May proceed under automated migration flows when all other SPEC requirements are met. |
| **NEEDS_REVIEW** | Must not run automatically; requires human review and explicit resolution before execution. |
| **UNSUPPORTED** | Out of scope for the evaluated pair (including all cross-backend pairs in v1). |

#### Evidence required for **SUPPORTED**

A pair may be marked **SUPPORTED** only when documented evidence exists for **all** of the following:

1. **Version evidence** — Supported source and target versions (OS, iRedMail, and backend product versions) are identified and bounded.
2. **Schema evidence** — Schema and data objects relevant to migration are accounted for (including known differences within the same backend identifier).
3. **Procedure evidence** — Migration steps align with approved procedures and official iRedMail guidance for that pair.
4. **Laboratory validation evidence** — Successful exercise of the migration path in a **non-production laboratory** environment, with results recorded (see SPEC acceptance criterion 13).

Absence of any required evidence means the pair **must not** be labeled **SUPPORTED**.

#### **NEEDS_REVIEW** and automation

- **NEEDS_REVIEW** **blocks automatic execution**. The orchestration layer must not proceed unattended while this label applies to a blocking pair.
- **Operator confirmation alone must not** change **NEEDS_REVIEW** to **SUPPORTED**. Escalation to **SUPPORTED** requires the full evidence set above and acceptance through the project’s compatibility governance (typically matrix update plus owner acknowledgment where policy requires it—not a single UI confirmation).

### Pairwise matrix

The project maintains an explicit **pairwise compatibility matrix** keyed by `(source_backend_identifier, target_backend_identifier, version context)`. For v1:

- All diagonal same-identifier pairs are eligible for evidence-based progression from unknown → **NEEDS_REVIEW** → **SUPPORTED**.
- All off-diagonal (cross-backend) pairs are **UNSUPPORTED** by this ADR.

## Consequences

### Positive

- Clear v1 boundary for engineering, testing, and operator expectations.
- Compatibility assessment (FR-14) can fail fast on cross-backend attempts before planning or execution.
- **SUPPORTED** is tied to auditable evidence, consistent with SPEC validation and laboratory acceptance goals.

### Negative / limitations

- Installations that require a backend change during migration are **not** served in v1.
- Same-identifier pairs still require version and lab evidence before **SUPPORTED**; many pairs will remain **NEEDS_REVIEW** or unlisted until validated.

### Follow-up

- Populate the compatibility matrix as inventory and lab work produce evidence.
- Resolve remaining SPEC open questions (§19) in coordination with this policy; do not treat this ADR as a substitute for a future SPEC revision where normative requirement text is needed.
- Any expansion to cross-backend or additional identifiers requires a **new ADR** and owner approval.

## References

- [`docs/SPEC.md`](../SPEC.md) — FR-14 (compatibility assessment), §3 (source backends), §19 (open questions), §20 (requirements governance).
- GitHub Issue documenting this work: [Issue #3](https://github.com/FF3mail/Mail-Updater/issues/3) (ADR infrastructure and ADR-001).
- **Review evidence (not decision authority):** Independent Grok review, 2026-11-13 — analysis of backend identification and matrix design options (Option C selected by owner as above).
