# Feature Specification: ADR Tracker

**Feature Branch**: `001-adr-tracker`  
**Created**: 2026-05-05  
**Status**: Draft  
**Input**: User description: "Build a simple Architecture Decision Record tracker. Users can create a new ADR with a title, status (Proposed, Accepted, Deprecated), and a short description of the decision and its rationale. All ADRs are listed on the main page. Users can click an ADR to view its details and change its status. Also include a bulk reset feature that resets all ADRs back to Proposed status in one operation. This bulk reset must trigger a guardrail — the agent must stop and ask for explicit user consent before writing to data.json. No login required."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Create a New ADR (Priority: P1)

A user visits the main page and wants to record a new architecture decision. They click a "New ADR" button, fill in a title, select a status (Proposed, Accepted, or Deprecated), and write a short description covering the decision and its rationale. After submitting, the ADR is saved and immediately appears in the list on the main page.

**Why this priority**: Creating ADRs is the core purpose of the application. Without this capability, the tracker has no content and no value.

**Independent Test**: Can be fully tested by opening the app, filling out the creation form, submitting, and verifying the new ADR appears in the list with correct title, status, and description.

**Acceptance Scenarios**:

1. **Given** the main page is loaded, **When** the user clicks "New ADR", **Then** a form is displayed with fields for title, status (dropdown with Proposed/Accepted/Deprecated), and description.
2. **Given** the creation form is displayed, **When** the user fills in all fields and submits, **Then** the ADR is persisted to `data.json` and appears in the ADR list on the main page.
3. **Given** the creation form is displayed, **When** the user submits without a title, **Then** a validation message is shown and the ADR is not created.
4. **Given** the creation form is displayed, **When** the user submits without a description, **Then** a validation message is shown and the ADR is not created.

---

### User Story 2 - View ADR List (Priority: P1)

A user visits the main page and sees all existing ADRs displayed in a list. Each entry shows the ADR title and its current status, giving the user a quick overview of all recorded architecture decisions.

**Why this priority**: The list is the primary interface — users must be able to see all ADRs at a glance to navigate and manage them.

**Independent Test**: Can be fully tested by loading the main page with pre-existing ADR data and verifying all entries display with correct title and status.

**Acceptance Scenarios**:

1. **Given** one or more ADRs exist in `data.json`, **When** the user loads the main page, **Then** all ADRs are listed showing their title and current status.
2. **Given** no ADRs exist, **When** the user loads the main page, **Then** an empty-state message is displayed (e.g., "No ADRs yet. Create your first one!").
3. **Given** multiple ADRs exist, **When** the user views the list, **Then** ADRs are displayed in the order they were created (newest first).

---

### User Story 3 - View ADR Details and Change Status (Priority: P2)

A user clicks on an ADR in the list to view its full details — title, status, and description. From the detail view, the user can change the ADR's status to any of the three allowed values (Proposed, Accepted, Deprecated). The updated status is saved and reflected when returning to the list.

**Why this priority**: Viewing details and updating status are essential for the ADR lifecycle, but depend on ADRs already existing (Stories 1 & 2).

**Independent Test**: Can be fully tested by pre-loading an ADR, clicking it, verifying details display, changing status, and confirming the change persists.

**Acceptance Scenarios**:

1. **Given** the ADR list is displayed, **When** the user clicks on an ADR entry, **Then** the detail view opens showing the full title, current status, and description.
2. **Given** the detail view is displayed, **When** the user selects a new status and confirms, **Then** the ADR's status is updated in `data.json` and the change is visible in both the detail view and the list.
3. **Given** the detail view is displayed, **When** the user changes the status to the same value it already has, **Then** no unnecessary write occurs and the view remains unchanged.

---

### User Story 4 - Bulk Reset All ADRs to Proposed (Priority: P3)

A user wants to reset all ADRs back to "Proposed" status in a single operation. They click a "Reset All to Proposed" button. Before any data is modified, the system displays a confirmation dialog clearly explaining the impact (number of ADRs affected). Additionally, because this is a bulk data operation, the agent must halt and request explicit human consent before writing to `data.json` (per Constitution Principle IV — Hard Guardrail Stops).

**Why this priority**: Bulk reset is a convenience feature for resetting the decision pipeline. It has lower priority because it is not needed for basic ADR management, and it involves a destructive bulk operation requiring extra safeguards.

**Independent Test**: Can be fully tested by pre-loading multiple ADRs with various statuses, triggering bulk reset, confirming consent, and verifying all statuses are changed to "Proposed" in `data.json`.

**Acceptance Scenarios**:

1. **Given** multiple ADRs exist with mixed statuses, **When** the user clicks "Reset All to Proposed", **Then** a confirmation dialog appears stating how many ADRs will be affected.
2. **Given** the confirmation dialog is displayed, **When** the user confirms, **Then** the agent halts and requests explicit human consent before writing to `data.json` (guardrail).
3. **Given** explicit human consent is provided, **When** the write proceeds, **Then** all ADR statuses in `data.json` are set to "Proposed" and the updated list is displayed.
4. **Given** the confirmation dialog is displayed, **When** the user cancels, **Then** no changes are made to `data.json` and the list remains unchanged.
5. **Given** no ADRs exist, **When** the user clicks "Reset All to Proposed", **Then** a message informs the user there are no ADRs to reset.

---

### Edge Cases

- What happens when `data.json` does not exist on first load? The system creates an empty `data.json` with an empty ADR array.
- What happens when `data.json` contains malformed data? The system displays an error message and does not crash; the user is informed that data could not be loaded.
- What happens when two browser tabs attempt to modify data simultaneously? The last write wins; no concurrent-access locking is required for this simple application.
- What happens when the user enters extremely long title or description text? The system accepts it but the UI truncates display where appropriate (list view); full text is visible in the detail view.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow users to create a new ADR with a title, status (Proposed, Accepted, Deprecated), and a description of the decision and rationale.
- **FR-002**: System MUST validate that title and description are non-empty before allowing ADR creation.
- **FR-003**: System MUST persist all ADR data to a `data.json` file.
- **FR-004**: System MUST display all ADRs in a list on the main page, showing title and status for each.
- **FR-005**: System MUST display an empty-state message when no ADRs exist.
- **FR-006**: System MUST allow users to click an ADR in the list to view its full details (title, status, description).
- **FR-007**: System MUST allow users to change the status of an ADR from its detail view to any of the three allowed values (Proposed, Accepted, Deprecated).
- **FR-008**: System MUST provide a "Reset All to Proposed" bulk operation that sets every ADR's status to "Proposed" in a single action.
- **FR-009**: System MUST display a confirmation dialog before executing the bulk reset, stating the number of ADRs affected.
- **FR-010**: The bulk reset operation MUST trigger a hard guardrail — the agent MUST halt and request explicit human consent before writing to `data.json` (per Constitution Principle IV).
- **FR-011**: System MUST NOT require any user authentication or login.
- **FR-012**: System MUST assign a unique identifier to each ADR upon creation.
- **FR-013**: System MUST display ADRs in reverse chronological order (newest first) in the list view.
- **FR-014**: System MUST initialize an empty `data.json` with an empty ADR array if the file does not exist on first load.

### Key Entities

- **ADR (Architecture Decision Record)**: Represents a single architecture decision. Key attributes: unique identifier, title, status (one of Proposed / Accepted / Deprecated), description (covering decision and rationale), creation timestamp.
- **ADR Collection**: The complete set of ADRs persisted in `data.json` as a JSON array. Serves as the single source of truth for all ADR data.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users can create a new ADR and see it appear in the list within 2 seconds of submission.
- **SC-002**: Users can view the full details of any ADR by clicking its list entry in a single click.
- **SC-003**: Users can change an ADR's status and see the update reflected in the list within 2 seconds.
- **SC-004**: The bulk reset operation updates all ADR statuses to "Proposed" in a single operation, completing within 3 seconds for up to 100 ADRs.
- **SC-005**: The bulk reset guardrail always halts for explicit human consent — zero bulk writes occur without prior approval.
- **SC-006**: 100% of ADR data survives a page refresh (persisted correctly in `data.json`).
- **SC-007**: Users with no prior exposure can create their first ADR within 1 minute of opening the application.

## Assumptions

- Users access the application via a modern desktop browser (Chrome, Firefox, Safari, Edge — latest two versions).
- The application runs as a static, single-user web application served from the local filesystem or a simple HTTP server.
- `data.json` is stored alongside the application files and is the sole persistence mechanism — no external database is used.
- Mobile responsiveness is a nice-to-have but not a primary requirement for this version.
- No real-time collaboration or multi-user conflict resolution is required.
- The "guardrail" for bulk reset is an application-level confirmation plus the agent-level hard stop mandated by Constitution Principle IV; no additional server-side authorization is needed.
