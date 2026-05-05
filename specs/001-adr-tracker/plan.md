# Implementation Plan: ADR Tracker

**Branch**: `001-adr-tracker` | **Date**: 2026-05-05 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `specs/001-adr-tracker/spec.md`

## Summary

Build a browser-based Architecture Decision Record (ADR) tracker using vanilla HTML/CSS/JS with localStorage persistence. Users create, view, and manage ADRs with three statuses (Proposed, Accepted, Deprecated). A bulk reset feature demonstrates Constitution Principle IV by halting for explicit human consent before modifying data, with all guardrail invocations logged to `guardrail-log.md`. Tests use Jasmine via CDN.

## Technical Context

**Language/Version**: JavaScript (ES2022+), HTML5, CSS3  
**Primary Dependencies**: None (vanilla — Constitution Principle II)  
**Storage**: localStorage (browser-native); data.json for import/export  
**Testing**: Jasmine 5.x via CDN (Constitution Principle III)  
**Target Platform**: Modern desktop browsers (Chrome 92+, Firefox 95+, Safari 15.4+, Edge 92+)  
**Project Type**: Static single-page web application  
**Performance Goals**: All operations complete within 2-3 seconds (SC-001 through SC-004)  
**Constraints**: No npm, no build tools, no server-side code, no external dependencies  
**Scale/Scope**: Single user, up to 100 ADRs

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Pre-Phase 0 | Post-Phase 1 | Evidence |
|-----------|-------------|--------------|----------|
| I. Spec Traceability | ✅ PASS | ✅ PASS | All source files reference FR-xxx IDs in comments; quickstart maps files to FRs |
| II. Minimal Dependencies | ✅ PASS | ✅ PASS | Zero runtime dependencies; Jasmine CDN for tests only (pre-approved exception) |
| III. Test Coverage (Jasmine via CDN) | ✅ PASS | ✅ PASS | SpecRunner.html loads Jasmine via CDN; spec files in tests/spec/*.Spec.js |
| IV. Hard Guardrail Stops | ✅ PASS | ✅ PASS | FR-009/010/015/016 implement guardrail; agent must halt during bulk reset implementation |

No violations. No complexity tracking needed.

## Project Structure

### Documentation (this feature)

```text
specs/001-adr-tracker/
├── spec.md              # Feature specification
├── plan.md              # This file
├── research.md          # Phase 0: technical decisions
├── data-model.md        # Phase 1: entity definitions
├── quickstart.md        # Phase 1: setup and run guide
├── contracts/
│   └── ui-contracts.md  # Phase 1: module interface contracts
└── tasks.md             # Phase 2: task breakdown (via /speckit-tasks)
```

### Source Code (repository root)

```text
├── index.html              # Main application page [FR-004, FR-005, FR-006, FR-011]
├── index.css               # Application styles
├── src/
│   ├── app.js              # Entry point, DOM event wiring [FR-001..FR-014]
│   ├── adr-store.js        # Data layer: CRUD, localStorage, bulk reset [FR-001..FR-003, FR-007, FR-008, FR-012..FR-014]
│   ├── adr-renderer.js     # UI rendering: list, detail, forms [FR-004..FR-007]
│   └── guardrail.js        # Guardrail dialog and logging [FR-009, FR-010, FR-015, FR-016]
├── data.json               # Seed/export data file [FR-003, FR-014]
├── guardrail-log.md        # Append-only guardrail log [FR-015, FR-016]
├── tests/
│   ├── SpecRunner.html     # Jasmine CDN test runner
│   └── spec/
│       ├── AdrStoreSpec.js  # Data layer tests [Story 1, 2]
│       ├── AdrRendererSpec.js # UI rendering tests [Story 2, 3]
│       ├── GuardrailSpec.js   # Guardrail behavior tests [Story 4]
│       └── AppSpec.js         # Integration tests [All Stories]
```

**Structure Decision**: Single-project flat structure. No backend — purely client-side application. The `src/` directory holds four focused modules with clear separation of concerns: data (adr-store), rendering (adr-renderer), guardrail logic, and orchestration (app). Test files mirror source modules.

## Design Artifacts

| Artifact | Path | Status |
|----------|------|--------|
| Research | [research.md](research.md) | ✅ Complete |
| Data Model | [data-model.md](data-model.md) | ✅ Complete |
| Contracts | [contracts/ui-contracts.md](contracts/ui-contracts.md) | ✅ Complete |
| Quickstart | [quickstart.md](quickstart.md) | ✅ Complete |
