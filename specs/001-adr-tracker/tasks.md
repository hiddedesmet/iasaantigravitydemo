# Tasks: ADR Tracker

**Input**: Design documents from `/specs/001-adr-tracker/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Test tasks are included per Constitution Principle III — all features MUST have Jasmine test coverage.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3, US4)
- Include exact file paths in descriptions

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization, file scaffolding, and seed data

- [ ] T001 Create project directory structure with `src/`, `tests/spec/` folders per plan.md
- [ ] T002 [P] Create `index.html` with application shell, script tags for `src/app.js`, `src/adr-store.js`, `src/adr-renderer.js`, `src/guardrail.js`, and link to `index.css` in index.html [FR-004, FR-011]
- [ ] T003 [P] Create `index.css` with base application styles, CSS custom properties, and layout in index.css
- [ ] T004 [P] Create `data.json` with four pre-populated seed ADRs (2 Accepted, 1 Proposed, 1 Deprecated) per plan.md seed data design in data.json [FR-003, FR-014]
- [ ] T005 [P] Create `guardrail-log.md` as an empty append-only log file in guardrail-log.md [FR-015, FR-016]
- [ ] T006 [P] Create `tests/SpecRunner.html` with Jasmine 5.x CDN links and script includes for all spec files in tests/SpecRunner.html [Constitution Principle III]

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core data layer and guardrail infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T007 Implement `initStore()` in `src/adr-store.js` — load from localStorage key `adr-tracker-data`, or initialize empty `{ adrs: [] }`; if localStorage is empty, attempt `fetch('data.json')` to seed data [FR-014, FR-003]
- [ ] T008 Implement `getAllAdrs()` in `src/adr-store.js` — return all ADRs sorted by `createdAt` descending (newest first) [FR-013]
- [ ] T009 [P] Implement `getAdrById(id)` in `src/adr-store.js` — return single ADR or null [FR-006]
- [ ] T010 [P] Implement `exportData()` and `importData(jsonString)` in `src/adr-store.js` — JSON export/import with schema validation [FR-003]
- [ ] T011 [P] Implement `requestGuardrailConsent()` in `src/guardrail.js` — return Promise<boolean> for consent dialog [FR-009, FR-010]
- [ ] T012 [P] Implement `logGuardrailOutcome()` and `getGuardrailLog()` in `src/guardrail.js` — append-only in-memory log with timestamp, guardrail name, operation, record count, consent outcome [FR-015, FR-016]
- [ ] T013 [P] Implement `showNotification(message, type)` in `src/adr-renderer.js` — toast/notification for success, error, info messages [FR-002, FR-005]

**Checkpoint**: Foundation ready — data layer and guardrail infrastructure functional. User story implementation can now begin.

---

## Phase 3: User Story 1 — Create a New ADR (Priority: P1) 🎯 MVP

**Goal**: Users can create a new ADR with title, status, and description. After submission, the ADR is saved and appears in the list.

**Independent Test**: Open app → click "New ADR" → fill form → submit → verify ADR appears in list with correct title, status, description.

### Tests for User Story 1 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T014 [P] [US1] Write Jasmine specs for `createAdr()` in `tests/spec/AdrStoreSpec.js` — test valid creation, empty title rejection, empty description rejection, invalid status rejection, unique ID generation [FR-001, FR-002, FR-012]

### Implementation for User Story 1

- [ ] T015 [US1] Implement `createAdr({ title, status, description })` in `src/adr-store.js` — validate non-empty title/description, validate status against `VALID_STATUSES`, generate UUID via `crypto.randomUUID()`, set `createdAt` to ISO 8601, persist to localStorage [FR-001, FR-002, FR-012]
- [ ] T016 [US1] Implement `renderCreateForm(container)` in `src/adr-renderer.js` — form with title input, status dropdown (Proposed/Accepted/Deprecated), description textarea, submit button, and validation messages [FR-001, FR-002]
- [ ] T017 [US1] Wire "New ADR" button click and form submission in `src/app.js` — show create form, handle submit, call `createAdr()`, show notification, refresh list [FR-001, FR-002]

**Checkpoint**: User Story 1 complete — users can create ADRs with validation. Run `tests/spec/AdrStoreSpec.js` to verify.

---

## Phase 4: User Story 2 — View ADR List (Priority: P1)

**Goal**: Users see all existing ADRs on the main page, each showing title and current status. Empty state shown when no ADRs exist.

**Independent Test**: Load main page with pre-existing data → verify all entries display with correct title and status. Load with empty data → verify empty-state message.

### Tests for User Story 2 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T018 [P] [US2] Write Jasmine specs for `renderList()` and `renderEmptyState()` in `tests/spec/AdrRendererSpec.js` — test list renders all ADRs with title and status, test empty state message, test reverse chronological order [FR-004, FR-005, FR-013]

### Implementation for User Story 2

- [ ] T019 [US2] Implement `renderList(adrs, container)` in `src/adr-renderer.js` — render each ADR as a clickable list item showing title and status badge, sorted newest first [FR-004, FR-013]
- [ ] T020 [US2] Implement `renderEmptyState(container)` in `src/adr-renderer.js` — display "No ADRs yet. Create your first one!" message [FR-005]
- [ ] T021 [US2] Wire initial page load in `src/app.js` — call `initStore()`, then `getAllAdrs()`, render list or empty state, attach "New ADR" button handler [FR-004, FR-005, FR-014]

**Checkpoint**: User Stories 1 & 2 complete — users can create ADRs and see them in the list. This is the MVP.

---

## Phase 5: User Story 3 — View ADR Details and Change Status (Priority: P2)

**Goal**: Users click an ADR to view full details and can change its status to any of the three allowed values.

**Independent Test**: Pre-load an ADR → click it → verify detail view shows title, status, description → change status → confirm change persists in list and detail view.

### Tests for User Story 3 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T022 [P] [US3] Write Jasmine specs for `updateAdrStatus()` in `tests/spec/AdrStoreSpec.js` — test valid status update, test ADR not found error, test invalid status error, test no-op when same status [FR-007]
- [ ] T023 [P] [US3] Write Jasmine specs for `renderDetail()` in `tests/spec/AdrRendererSpec.js` — test detail view shows full title, status, description; test status change dropdown [FR-006, FR-007]

### Implementation for User Story 3

- [ ] T024 [US3] Implement `updateAdrStatus(id, newStatus)` in `src/adr-store.js` — validate ADR exists and status is valid, skip write if status unchanged, persist to localStorage [FR-007]
- [ ] T025 [US3] Implement `renderDetail(adr, container)` in `src/adr-renderer.js` — show full title, current status, description, status change dropdown with confirm button, back-to-list navigation [FR-006, FR-007]
- [ ] T026 [US3] Wire ADR list item click and status change in `src/app.js` — show detail view on click, handle status change confirm, call `updateAdrStatus()`, show notification, refresh views [FR-006, FR-007]

**Checkpoint**: User Stories 1, 2 & 3 complete — full ADR lifecycle (create, view, update status) is functional.

---

## Phase 6: User Story 4 — Bulk Reset All ADRs to Proposed (Priority: P3)

**Goal**: Users can reset all ADRs to "Proposed" status in one operation. The guardrail halts for explicit consent before writing, and logs the outcome regardless of consent.

**Independent Test**: Pre-load multiple ADRs with mixed statuses → click "Reset All to Proposed" → confirm consent → verify all statuses are "Proposed". Also test refusal: verify no changes and log records refusal.

### Tests for User Story 4 ⚠️

> **NOTE: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T027 [P] [US4] Write Jasmine specs for `bulkResetToProposed()` in `tests/spec/AdrStoreSpec.js` — test all statuses reset to "Proposed", test return count, test empty collection [FR-008]
- [ ] T028 [P] [US4] Write Jasmine specs for guardrail consent and logging in `tests/spec/GuardrailSpec.js` — test consent dialog shows guardrail name and record count, test granted/refused logging, test log entry format [FR-009, FR-010, FR-015, FR-016]

### Implementation for User Story 4

- [ ] T029 [US4] Implement `bulkResetToProposed()` in `src/adr-store.js` — set all ADR statuses to "Proposed", persist to localStorage, return `{ count }` [FR-008]
- [ ] T030 [US4] Implement `renderGuardrailDialog({ guardrailName, operation, recordCount }, container)` in `src/adr-renderer.js` — modal dialog stating Constitution Principle IV, exact record count, yes/no buttons, return Promise<boolean> [FR-009, FR-010]
- [ ] T031 [US4] Wire "Reset All to Proposed" button in `src/app.js` — check if ADRs exist, show guardrail dialog via `requestGuardrailConsent()`, on consent call `bulkResetToProposed()`, log outcome via `logGuardrailOutcome()`, refresh list, show notification [FR-008, FR-009, FR-010, FR-015, FR-016]

**⚠️ CONSTITUTION PRINCIPLE IV**: During implementation of T029/T031, the agent MUST halt and request explicit human consent before writing the bulk reset logic that modifies `data.json`/localStorage. This is a hard guardrail stop — proceeding without approval is a constitution violation.

**Checkpoint**: All four user stories complete — full ADR tracker with guardrailed bulk reset.

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Integration testing, export/import UI, and final refinements

- [ ] T032 [P] Write integration tests in `tests/spec/AppSpec.js` — test full workflows: create ADR → appears in list → view detail → change status → bulk reset with consent [All Stories]
- [ ] T033 [P] Add export/import UI controls in `index.html` and wire in `src/app.js` — "Export" button triggers JSON download, "Import" button opens file picker and loads data [FR-003]
- [ ] T034 Add edge case handling in `src/adr-store.js` — malformed `data.json` error handling, empty localStorage initialization, long text graceful handling [Edge Cases]
- [ ] T035 [P] Polish `index.css` — status badges with color coding (Proposed=blue, Accepted=green, Deprecated=red), responsive layout, form styling, modal dialog styling, notification toast styling
- [ ] T036 Run `tests/SpecRunner.html` — verify all Jasmine specs pass across all spec files [Constitution Principle III]
- [ ] T037 Validate quickstart.md — open app via `python3 -m http.server 8080`, verify seed data loads, test all key workflows documented in quickstart.md

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion — BLOCKS all user stories
- **User Story 1 (Phase 3)**: Depends on Foundational (Phase 2) — No dependencies on other stories
- **User Story 2 (Phase 4)**: Depends on Foundational (Phase 2) — Can run in parallel with US1 but integrates naturally after
- **User Story 3 (Phase 5)**: Depends on Foundational (Phase 2) — Benefits from US2 list rendering but independently testable
- **User Story 4 (Phase 6)**: Depends on Foundational (Phase 2) and guardrail infrastructure — Independently testable
- **Polish (Phase 7)**: Depends on all user stories being complete

### User Story Dependencies

- **US1 (P1)**: Independent — only needs foundational store
- **US2 (P1)**: Independent — only needs foundational store; shares list rendering with US1
- **US3 (P2)**: Soft dependency on US2 (list view provides navigation to detail) but detail view is independently testable
- **US4 (P3)**: Independent — only needs foundational store and guardrail infrastructure

### Within Each User Story

- Tests MUST be written and FAIL before implementation (Constitution Principle III)
- Data layer before rendering
- Rendering before app wiring
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks T002–T006 marked [P] can run in parallel
- Foundational tasks T009–T013 marked [P] can run in parallel
- All test tasks within a story marked [P] can run in parallel
- Once Foundational phase completes, US1 and US2 can start in parallel
- US3 and US4 can start in parallel after US2 completes

---

## Parallel Example: User Story 1

```bash
# Launch tests for User Story 1:
Task: T014 "Write Jasmine specs for createAdr() in tests/spec/AdrStoreSpec.js"

# After tests fail, implement:
Task: T015 "Implement createAdr() in src/adr-store.js"
Task: T016 "Implement renderCreateForm() in src/adr-renderer.js"

# Wire everything together:
Task: T017 "Wire New ADR button and form submission in src/app.js"
```

---

## Parallel Example: User Story 4

```bash
# Launch tests for User Story 4 (both in parallel):
Task: T027 "Write Jasmine specs for bulkResetToProposed() in tests/spec/AdrStoreSpec.js"
Task: T028 "Write Jasmine specs for guardrail in tests/spec/GuardrailSpec.js"

# After tests fail, implement sequentially:
Task: T029 "Implement bulkResetToProposed() in src/adr-store.js"
Task: T030 "Implement renderGuardrailDialog() in src/adr-renderer.js"
Task: T031 "Wire Reset All button in src/app.js"
```

---

## Implementation Strategy

### MVP First (User Stories 1 & 2 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL — blocks all stories)
3. Complete Phase 3: User Story 1 (Create ADR)
4. Complete Phase 4: User Story 2 (View List)
5. **STOP and VALIDATE**: Test US1 + US2 independently — app should allow creating and viewing ADRs
6. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add US1 + US2 → Test independently → Deploy/Demo (**MVP!**)
3. Add US3 → Test independently → Deploy/Demo (detail view + status changes)
4. Add US4 → Test independently → Deploy/Demo (bulk reset with guardrail)
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1 + User Story 3
   - Developer B: User Story 2 + User Story 4
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability (Constitution Principle I)
- Each user story should be independently completable and testable
- Verify tests fail before implementing (Constitution Principle III)
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently
- ⚠️ US4 implementation triggers Constitution Principle IV — agent MUST halt for human consent
