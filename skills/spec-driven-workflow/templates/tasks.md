# Tasks: [Change Name]

**Spec:** ./spec.md | **Design:** ./design.md (if present)

<!--
RULES
- Every task is a checkbox `- [ ] X.Y ...` — this file IS the progress tracker.
- Order by dependency; group by phase, then by priority slice (P1 first).
- [P] = independent of its neighbors; can run in parallel or be delegated.
- (owner: name) on every delegated task.
- Every task ends verifiable: a passing test, an existing artifact, a received
  confirmation. Exact paths, commands, contacts. No "TBD", no "handle
  appropriately", no "similar to task N".
-->

## 1. Setup / Foundations

<!-- Work that blocks everything else: scaffolding, access, bookings, approvals -->

- [ ] 1.1 [Exact action with exact target — e.g. `Create src/theme/context.tsx`, or "Reserve venue X for DATE, confirmation email received"]
- [ ] 1.2 [P] [Independent setup task]

## 2. P1 — [Slice title]  🎯 minimum viable outcome

- [ ] 2.1 [Task with verification — e.g. "Write failing test `tests/x.test.ts`, run `npm test`, expect FAIL"]
- [ ] 2.2 [Implementation/execution task]
- [ ] 2.3 Verify P1 scenarios from spec.md hold — [name the scenarios]

**Checkpoint:** P1 is independently deliverable here. Confirm with human partner before continuing.

## 3. P2 — [Slice title]

- [ ] 3.1 [P] [...] (owner: [name])
- [ ] 3.2 [...]

## 4. Verify & Close

- [ ] 4.1 Walk EVERY spec scenario with fresh evidence (run it / check it / show it)
- [ ] 4.2 Cross-check: proposal scope vs. what was actually done; list gaps honestly
- [ ] 4.3 Merge delta specs into living specs at `specs/<capability>/spec.md`
- [ ] 4.4 Move change folder to `specs/changes/archive/YYYY-MM-DD-<change-name>/`
- [ ] 4.5 Record learnings (update `specs/principles.md` if a new standing rule emerged)
