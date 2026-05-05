<!--
  Sync Impact Report
  ==================
  Version change: 0.0.0 → 1.0.0
  Bump rationale: MAJOR — initial constitution ratification with four
                  foundational principles; no prior version existed.

  Added principles:
    - I. Spec Traceability
    - II. Minimal Dependencies
    - III. Test Coverage (Jasmine via CDN)
    - IV. Hard Guardrail Stops (NON-NEGOTIABLE)

  Added sections:
    - Pre-Approved Exceptions
    - Compliance & Review

  Removed sections: (none — first version)

  Templates requiring updates:
    ✅ plan-template.md   — Constitution Check section already generic;
                            principles align without edits.
    ✅ spec-template.md   — Requirements section supports traceability IDs;
                            no edits required.
    ✅ tasks-template.md  — Task phases and test-first notes compatible;
                            no edits required.

  Follow-up TODOs: none
-->

# IASA Antigravity Demo Constitution

## Core Principles

### I. Spec Traceability

Every implementation artifact MUST trace back to a specification entry.

- Every code file, function, or module MUST reference the spec
  requirement (e.g., FR-001, SC-002) it satisfies.
- Every task in `tasks.md` MUST include a `[Story]` label linking it
  to a user story defined in `spec.md`.
- Pull requests and commits MUST cite the spec requirement(s) they
  address.
- If a change cannot be traced to an existing spec entry, the spec
  MUST be amended first — implementation without traceability is
  prohibited.

**Rationale**: Traceability ensures no orphaned code, no scope creep,
and full auditability from requirement to deployed artifact.

### II. Minimal Dependencies

The project MUST use the fewest external dependencies possible.

- Every external dependency MUST be explicitly justified with a
  documented rationale in the implementation plan.
- Native browser APIs and vanilla HTML/CSS/JS MUST be preferred over
  third-party libraries.
- No package manager (npm, yarn, etc.) is required or expected; all
  dependencies MUST be loaded via CDN or vendored inline.
- Adding a new dependency requires a written justification that no
  built-in or simpler alternative exists.
- Pre-approved exceptions are listed in the **Pre-Approved
  Exceptions** section below.

**Rationale**: Fewer dependencies reduce attack surface, build
complexity, and long-term maintenance burden.

### III. Test Coverage (Jasmine via CDN)

All features MUST have automated test coverage using Jasmine loaded
via CDN.

- Jasmine MUST be loaded exclusively via CDN (`<script>` tag) — no
  local installation, no npm, no bundler.
- Every user story MUST have at least one corresponding Jasmine spec
  file before implementation begins (test-first).
- Tests MUST run in the browser by opening an HTML test runner — no
  Node.js test runner is permitted.
- A test runner HTML page (`tests/SpecRunner.html` or equivalent)
  MUST exist and load all spec files.
- Test files MUST follow the naming convention
  `tests/spec/*Spec.js`.
- All tests MUST pass before a task is marked complete.

**Rationale**: CDN-loaded Jasmine keeps the project zero-install while
ensuring every feature is verified by automated tests.

### IV. Hard Guardrail Stops (NON-NEGOTIABLE)

The agent MUST halt and request explicit human consent before
executing any destructive or bulk data operation.

- **Destructive operations** include but are not limited to: file
  deletion, directory removal, database drops, cache purges,
  overwriting existing files without backup, and force-pushes.
- **Bulk data operations** include but are not limited to: mass
  renames, batch file generation exceeding 10 files, bulk database
  inserts/updates/deletes, and recursive directory modifications.
- The agent MUST present a clear summary of what will be affected
  (file names, record counts, scope) and wait for an explicit
  "yes" / approval from the human operator.
- Proceeding without human approval is a **constitution violation**
  and MUST be treated as a blocking error.
- Automated pipelines or scripts MUST NOT bypass this gate; if
  automation is required, the human MUST pre-approve the specific
  operation scope in writing.
- This principle applies to all environments: development, staging,
  and production — no exceptions.

**Rationale**: Irreversible or large-scale operations carry
disproportionate risk. A mandatory human checkpoint prevents
accidental data loss and ensures informed decision-making.

## Pre-Approved Exceptions

The following dependencies and tooling choices are pre-approved and
do not require additional justification when used within their
stated scope:

| Exception | Scope | Justification |
|-----------|-------|---------------|
| Jasmine via CDN | Test tooling only (`tests/` directory) | Required by Principle III for browser-based test coverage; zero-install approach aligns with Principle II. |

Any use of a pre-approved exception outside its stated scope MUST
be treated as a new dependency and justified per Principle II.

## Compliance & Review

- Every pull request MUST include a self-check against all four
  principles before requesting review.
- Reviewers MUST verify spec traceability (Principle I) for every
  changed file.
- Reviewers MUST flag any undocumented dependency (Principle II).
- CI or manual test runs MUST confirm all Jasmine specs pass
  (Principle III).
- Any destructive or bulk operation in a PR diff MUST include
  evidence of human approval (Principle IV).

## Governance

- This constitution supersedes all other development practices and
  ad-hoc decisions within the project.
- Amendments require: (1) a written proposal referencing the
  principle(s) affected, (2) explicit approval from the project
  owner, and (3) a migration plan for any in-flight work impacted.
- Version numbering follows semantic versioning:
  - **MAJOR**: Principle removal or incompatible redefinition.
  - **MINOR**: New principle or materially expanded guidance.
  - **PATCH**: Clarifications, wording, or non-semantic fixes.
- Compliance reviews MUST occur at every spec, plan, and task
  generation step.

**Version**: 1.0.0 | **Ratified**: 2026-05-05 | **Last Amended**: 2026-05-05
