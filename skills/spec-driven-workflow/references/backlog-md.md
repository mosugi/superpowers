# Backlog.md CLI Mapping for spec-driven-workflow

How this workflow's concepts map onto the `backlog` CLI. Run `backlog instructions overview` for the tool's own workflow guide; this file covers only the conventions specific to this skill.

**Ground rules**

- Never edit files under `backlog/` by hand — always go through the CLI so IDs and metadata stay consistent.
- Always pass `--plain` on list/view commands when you (an agent) read the output.
- Multi-line text: repeat `--append-notes "line"` per line, or pass real newlines inside quotes. `\n` is NOT converted. Single-quote any text containing backticks.
- If no backlog exists yet: `backlog init "<project>"` (add `--no-git` for non-code folders).

## Standing documents (once per project, all optional)

```bash
backlog doc create "North Star"          # mission/vision — rarely touched
backlog doc create "Goals: 2026-H2"      # period goals — revised via lite mode at period boundaries
backlog doc create "Principles"          # constraints binding all work
backlog doc create "Spec: expense-approval" -p specs   # living spec per capability/area (full mode)
```

Revising a standing document is itself a change: brief → approval → `backlog doc update`. Never edit one as a side effect of other work.

## Setup per change

```bash
# Planning docs (content per templates/*.md)
backlog doc create "Brief: q3-tax-filing" -p changes/q3-tax-filing        # lite mode
backlog doc create "Proposal: new-onboarding" -p changes/new-onboarding  # full mode
backlog doc create "Spec: new-onboarding" -p changes/new-onboarding
backlog doc update doc-12 --content "…full markdown…"

# Milestone = parent task (one per deliverable slice, P1 first)
backlog task create "Q3 filing submitted and confirmed" \
  -l milestone --priority high \
  -d "Hard deadline: 2026-07-15 (statutory). Brief: doc-12" \
  --ac "WHEN finance opens the shared folder on the 15th THEN the signed filing PDF is there" \
  --ac "WHEN the portal is checked THEN submission status shows Accepted"

# Child tasks = session-sized work under the milestone
backlog task create -p 42 "Request P&L export from accounting (due: 2026-07-04)" \
  -a @me --priority high \
  -d "Email keiri@example.com, ask for Jan–Jun P&L as xlsx. Template: doc-13" \
  --ac "Request sent and acknowledged"
backlog task create -p 42 "Draft filing form using P&L numbers" --dep task-43 \
  --ac "All form sections filled, no placeholder cells"
```

Sequencing: `--dep task-N` chains tasks; tasks without deps are parallel/delegable. Front-load tasks that trigger external waits.

## Reading state (session start)

```bash
backlog task list --labels milestone --plain      # active milestones
backlog task <id> --plain                          # milestone detail: ACs + journal notes
backlog task list --parent <id> --plain            # its child tasks
backlog task list --labels waiting --plain         # outstanding external waits
backlog task list -s "In Progress" --plain         # where work stopped mid-flight
backlog search "<topic>" --plain                   # find related tasks/docs/decisions
```

## Executing

```bash
backlog task edit 43 -s "In Progress" -a @me
backlog task edit 43 --append-notes "2026-07-02: sent request, cc'd manager"
backlog task edit 43 --check-ac 1                  # only with fresh evidence in hand
backlog task edit 43 -s Done --final-summary "P&L received, saved to shared drive /finance/q3/"
backlog task edit 43 --comment "Is the June correction included?" # questions for humans
```

## Waits

```bash
backlog task edit 43 -l waiting \
  --append-notes "waiting on accounting P&L export; follow up 2026-07-07"
# when it arrives: remove label via edit (-l without 'waiting'), unblock dependents
```

## Session-end journal (on the MILESTONE task)

```bash
backlog task edit 42 --append-notes "2026-07-02: task-43 done (P&L received). Decided to use xlsx not CSV (portal requirement)."
backlog task edit 42 --append-notes "NEXT: task-44 — draft form sections 1-3 using numbers in /finance/q3/pl.xlsx"
```

The `NEXT:` line must be cold-startable: task ID + concrete first step.

## Decisions and close-out

```bash
backlog decision create "Use tax portal e-filing instead of paper submission"
backlog task edit 42 -s Done --final-summary "Filed 2026-07-14, portal receipt #12345, PDF in shared folder"
backlog task archive 43 && backlog task archive 42   # or: backlog cleanup
backlog board export status.md                        # shareable status report
```

## Useful extras

- `backlog board` / `backlog browser` — visual check for your human partner.
- `backlog draft create "Spike: …"` → `backlog draft promote <id>` — ideas not yet approved through the gate live as drafts.
- Definition of Done defaults (`backlog config`) can carry standing per-task requirements from the Principles doc (e.g. "filed under the right folder", "stakeholder notified").
