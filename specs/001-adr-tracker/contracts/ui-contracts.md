# UI Contracts: ADR Tracker

**Branch**: `001-adr-tracker` | **Date**: 2026-05-05

This application is a browser-based SPA with no external API. The "contracts" are the public JavaScript module interfaces that components use to communicate.

## adr-store.js — Data Layer Contract

```javascript
/**
 * ADR Store — Public Interface
 * Manages ADR data in localStorage.
 * All methods are synchronous.
 */

// Initialize store: load from localStorage or create empty collection
// Returns: { adrs: ADR[] }
function initStore()

// Get all ADRs sorted by createdAt descending (newest first)
// Returns: ADR[]
function getAllAdrs()

// Get a single ADR by ID
// Returns: ADR | null
function getAdrById(id)

// Create a new ADR
// Params: { title: string, status: string, description: string }
// Returns: ADR (with generated id and createdAt)
// Throws: Error if title or description is empty, or status is invalid
function createAdr({ title, status, description })

// Update an ADR's status
// Params: id: string, newStatus: string
// Returns: ADR (updated)
// Throws: Error if ADR not found or status is invalid
function updateAdrStatus(id, newStatus)

// Bulk reset all ADRs to "Proposed"
// Returns: { count: number } — number of ADRs affected
// GUARDRAIL: Caller must obtain consent before invoking
function bulkResetToProposed()

// Export current data as JSON string
// Returns: string (JSON)
function exportData()

// Import data from JSON string
// Params: jsonString: string
// Throws: Error if JSON is malformed or schema invalid
function importData(jsonString)
```

### ADR Schema

```javascript
{
  id: string,          // UUID v4 via crypto.randomUUID()
  title: string,       // Non-empty, trimmed
  status: string,      // "Proposed" | "Accepted" | "Deprecated"
  description: string, // Non-empty, trimmed
  createdAt: string    // ISO 8601
}
```

### Valid Status Values

```javascript
const VALID_STATUSES = ["Proposed", "Accepted", "Deprecated"];
```

## guardrail.js — Guardrail Contract

```javascript
/**
 * Guardrail — Public Interface
 * Handles guardrail confirmation dialogs and logging.
 */

// Show guardrail confirmation dialog
// Params: { guardrailName: string, operation: string, recordCount: number }
// Returns: Promise<boolean> — true if user consents, false if refused
function requestGuardrailConsent({ guardrailName, operation, recordCount })

// Log a guardrail invocation outcome
// Params: { guardrailName: string, operation: string, recordCount: number, consent: "granted" | "refused" }
// Side effect: Appends entry to guardrail log (in-memory for display)
function logGuardrailOutcome({ guardrailName, operation, recordCount, consent })

// Get all guardrail log entries
// Returns: GuardrailLogEntry[]
function getGuardrailLog()
```

## adr-renderer.js — UI Rendering Contract

```javascript
/**
 * ADR Renderer — Public Interface
 * Handles all DOM manipulation and view switching.
 */

// Render the ADR list view
// Params: adrs: ADR[], container: HTMLElement
function renderList(adrs, container)

// Render the ADR detail view
// Params: adr: ADR, container: HTMLElement
function renderDetail(adr, container)

// Render the ADR creation form
// Params: container: HTMLElement
function renderCreateForm(container)

// Render the empty state message
// Params: container: HTMLElement
function renderEmptyState(container)

// Render the guardrail confirmation dialog
// Params: { guardrailName: string, operation: string, recordCount: number }, container: HTMLElement
// Returns: Promise<boolean>
function renderGuardrailDialog({ guardrailName, operation, recordCount }, container)

// Show a toast/notification message
// Params: message: string, type: "success" | "error" | "info"
function showNotification(message, type)
```
