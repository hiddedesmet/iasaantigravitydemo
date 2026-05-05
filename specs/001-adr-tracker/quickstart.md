# Quickstart: ADR Tracker

**Branch**: `001-adr-tracker` | **Date**: 2026-05-05

## Prerequisites

- A modern web browser (Chrome 92+, Firefox 95+, Safari 15.4+, Edge 92+)
- A simple HTTP server (optional, for `data.json` seeding)

## Running the Application

### Option 1: Direct file open
```bash
open index.html
# or double-click index.html in Finder
```

### Option 2: Simple HTTP server (recommended for data.json import)
```bash
# Using Python (built-in, no install needed)
python3 -m http.server 8080

# Then open http://localhost:8080 in your browser
```

## Running Tests

```bash
# Open the test runner directly
open tests/SpecRunner.html

# Or via HTTP server
python3 -m http.server 8080
# Then navigate to http://localhost:8080/tests/SpecRunner.html
```

## Project Structure

```text
├── index.html              # Main application page
├── index.css               # Application styles
├── src/
│   ├── app.js              # Entry point, DOM event wiring [FR-001..FR-014]
│   ├── adr-store.js        # Data layer: CRUD, localStorage, bulk reset [FR-001..FR-014]
│   ├── adr-renderer.js     # UI rendering: list view, detail view, forms [FR-004..FR-007]
│   └── guardrail.js        # Guardrail dialog and logging [FR-009, FR-010, FR-015, FR-016]
├── data.json               # Seed data with 4 ADRs in mixed statuses [FR-003, FR-014]
├── guardrail-log.md        # Append-only guardrail invocation log [FR-015, FR-016]
├── tests/
│   ├── SpecRunner.html     # Jasmine test runner (CDN-loaded)
│   └── spec/
│       ├── AdrStoreSpec.js  # Tests for data layer [Story 1, 2]
│       ├── AdrRendererSpec.js # Tests for UI rendering [Story 2, 3]
│       ├── GuardrailSpec.js   # Tests for guardrail behavior [Story 4]
│       └── AppSpec.js         # Integration tests [All Stories]
└── specs/
    └── 001-adr-tracker/    # Feature specification and design documents
```

## Key Workflows

1. **Create ADR**: Click "New ADR" → Fill form → Submit → ADR saved to localStorage
2. **View details**: Click any ADR in the list → Detail view opens
3. **Change status**: In detail view → Select new status → Confirm → Status updated
4. **Bulk reset**: Click "Reset All to Proposed" → Confirm in dialog → All statuses reset
5. **Export data**: Click "Export" → `data.json` downloaded
6. **Import data**: Click "Import" → Select `data.json` file → Data loaded
