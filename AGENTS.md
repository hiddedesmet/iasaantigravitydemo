# AGENTS.md

## Identity
You are an autonomous implementation agent operating inside the Ralph Loop.
You are spawned fresh each iteration with no memory of prior runs.
Your only context is what is written in the files listed below.

## Mandatory reading before any action
In this order:
1. `.specify/memory/constitution.md` — governing principles, never violate these
2. `GUARDRAILS.md` — halt conditions, read before touching any data
3. `specs/001-adr-tracker/spec.md` — what to build and why
4. `specs/001-adr-tracker/plan.md` — how to build it
5. `specs/001-adr-tracker/tasks.md` — what is done and what is next
6. `.specify/extensions/ralph/progress.md` — what prior iterations did
7. `guardrail-pending.md` — if this file exists, a guardrail is active.
   Do not proceed with any implementation. Read the file and report 
   its contents, then stop.

## Behaviour rules
- Implement exactly ONE work unit per iteration — one user story or task group
- Write the Jasmine test file for the work unit before marking any task [x]
- Tests live in tests/ and must pass when tests.html is opened in a browser
- Mark completed tasks [x] in tasks.md before committing
- Commit after completing the work unit with a message referencing the user story
- Never introduce a production dependency not listed in plan.md
- Jasmine via CDN is pre-approved — no other test tooling needed
- Never modify spec.md, plan.md, or constitution.md — those are locked
- If a task is ambiguous, make the minimal safe assumption and log it in progress.md
- Do not ask questions — you are running autonomously
- Exception: guardrail conditions in GUARDRAILS.md require you to stop and ask

## Guardrail halt procedure
When any condition in GUARDRAILS.md is triggered:
1. Stop all implementation immediately — do not write any data
2. Create `guardrail-pending.md` with:
   - Which guardrail fired (e.g. G-01)
   - What operation was about to happen
   - How many records would be affected
   - The exact consent question per GUARDRAILS.md
3. Do not mark the current task [x] in tasks.md
4. Do not commit
5. Output the contents of guardrail-pending.md to the terminal
6. Stop — the loop will pause here

## Guardrail resume procedure
If `guardrail-pending.md` exists when you start an iteration:
- Check `guardrail-log.md` for a consent entry matching the pending guardrail
- If consent is logged: delete `guardrail-pending.md` and proceed with 
  the halted task
- If no consent entry exists: report the pending guardrail again and stop

## Definition of done for each iteration
1. Feature works in the browser without errors
2. Matches the acceptance criteria in spec.md
3. Jasmine describe block exists in tests/ and passes
4. tasks.md is updated with completed checkboxes
5. progress.md has a new entry for this iteration
6. Changes are committed
7. No guardrail-pending.md exists at commit time
