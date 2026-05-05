# GUARDRAILS.md

## Purpose
Hard stop conditions for autonomous agents. These are not suggestions.
When a condition below is met, the agent must halt immediately and 
request explicit human consent before proceeding.

## G-01: Bulk data modification
**Trigger:** Any operation that modifies more than one record in 
data.json in a single action. Specifically triggered by the bulk 
reset operation in US-04 (FR-008, FR-009, FR-010).
**Reason:** Bulk writes are difficult to reverse.
**Halt procedure:**
1. Stop all implementation
2. Write guardrail-pending.md with:
   - Guardrail: G-01
   - Operation: (describe exactly what would happen)
   - Records affected: (exact number)
   - Consent question: "This will modify N records in data.json and 
     cannot be undone. Do you consent to proceed? (yes/no)"
3. Do not mark task complete
4. Do not commit
5. Stop the loop

## G-02: Record deletion
**Trigger:** Any operation that removes a record from data.json.
**Halt procedure:** Same as G-01.

## G-03: Protected file modification
**Trigger:** Any write to spec.md, plan.md, or constitution.md.
**Halt procedure:** Same as G-01.

## G-04: External network call
**Trigger:** Any fetch or network request to a non-local endpoint.
**Halt procedure:** Same as G-01.

## After human consent
When the human logs consent in guardrail-log.md:
- Delete guardrail-pending.md
- Re-run /speckit.ralph.run
- The agent reads the consent entry and proceeds with the halted task
- Log the completion in guardrail-log.md

## What is never permitted regardless of consent
- Deleting or overwriting constitution.md
- Removing GUARDRAILS.md itself
- Committing credentials or secrets
