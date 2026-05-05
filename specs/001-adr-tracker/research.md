# Research: ADR Tracker

**Branch**: `001-adr-tracker` | **Date**: 2026-05-05

## Research Questions

### RQ-1: Static Web App Persistence Without a Server

**Decision**: Use `localStorage` as the primary runtime persistence layer, with a manual JSON export/import mechanism for `data.json` portability.

**Rationale**: A purely static HTML/CSS/JS app served from the filesystem or a simple HTTP server cannot write to the filesystem (no server-side component). `localStorage` provides synchronous, same-origin persistence that survives page refreshes. To satisfy the spec's reference to `data.json`, the app will:
1. Use `localStorage` as the live data store (key: `adr-tracker-data`).
2. Provide an "Export to data.json" button that triggers a file download.
3. Provide an "Import data.json" button that reads a file and loads it into `localStorage`.
4. On first load, if `localStorage` is empty, attempt to `fetch('data.json')` to seed initial data (works when served via HTTP server).

**Alternatives considered**:
- Server-side file I/O (Node.js/Express): Rejected — violates Constitution Principle II (minimal dependencies) and adds unnecessary complexity.
- IndexedDB: Rejected — overkill for a simple JSON array; localStorage is simpler and sufficient.
- File System Access API: Rejected — limited browser support and requires user permission prompts.

### RQ-2: Unique ID Generation Without Dependencies

**Decision**: Use `crypto.randomUUID()` (built-in browser API, supported in all modern browsers).

**Rationale**: Available natively in all target browsers (Chrome 92+, Firefox 95+, Safari 15.4+, Edge 92+). No external UUID library needed, aligning with Constitution Principle II.

**Alternatives considered**:
- `Date.now()` + random suffix: Fragile, potential collisions under rapid creation.
- External UUID library: Rejected — violates Principle II.

### RQ-3: Guardrail Implementation at Agent Level

**Decision**: The bulk reset guardrail operates at two levels:
1. **UI level**: A confirmation dialog in the browser stating which guardrail applies and how many records are affected.
2. **Agent level**: During implementation, the agent (AI) must halt before writing the bulk-reset logic to `data.json` and ask for explicit human consent — this is the Constitution Principle IV enforcement.

The guardrail log (`guardrail-log.md`) will be maintained as an append-only markdown file alongside the application, created by the agent during implementation.

**Rationale**: The "agent guardrail" is a development-time safeguard (the AI agent pausing for consent), while the "UI guardrail" is a runtime safeguard (the user confirming in the browser). Both are needed per the spec.

### RQ-4: Jasmine Testing Strategy for Browser-Only App

**Decision**: Use Jasmine 5.x via CDN with a `tests/SpecRunner.html` test runner. Test the data layer (ADR CRUD, bulk reset, guardrail log) as pure JavaScript functions, separate from DOM manipulation.

**Rationale**: Per Constitution Principle III, Jasmine must be loaded via CDN with browser-based test execution. Separating data logic from DOM rendering makes tests fast and reliable.

**Alternatives considered**:
- Testing DOM directly with Jasmine: Partially needed for integration tests, but core logic should be testable without DOM.

### RQ-5: Application Architecture Pattern

**Decision**: Simple module pattern with clear separation:
- `app.js` — Application entry point and DOM event wiring
- `adr-store.js` — Data layer (CRUD operations, localStorage interaction)
- `adr-renderer.js` — UI rendering (DOM manipulation, view switching)
- `guardrail.js` — Guardrail confirmation dialog and logging logic

**Rationale**: Minimal file count, clear responsibilities, no framework needed. Each module maps to testable spec requirements.
