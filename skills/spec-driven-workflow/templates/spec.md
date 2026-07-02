# Spec: [Change Name]

**Proposal:** ./proposal.md
**Status:** Draft | Clarified | Approved

<!--
RULES
- Requirements use SHALL/MUST. No "should probably", no aspirations.
- Every requirement has ≥1 scenario in WHEN/THEN form — concrete and observable.
- Prioritize independently deliverable slices: P1 alone must be a viable outcome.
- Mark every guess: [NEEDS CLARIFICATION: specific question]. Never invent answers.
- Modifying an existing living spec? Use the DELTA FORMAT at the bottom instead
  of restating everything.
-->

## Priority Slices

### P1 — [Slice title] (MVP / minimum viable outcome)

[Plain-language description of this slice as a standalone deliverable.]

**Why this priority:** [value]
**Independent verification:** [how this slice alone can be checked and shown]

### P2 — [Slice title]

[...]

## Requirements

### Requirement: [Name]  (P1)

The [system/process/team] SHALL [specific, observable capability].

#### Scenario: [Name]
- **WHEN** [trigger, action, or condition]
- **THEN** [observable outcome]

#### Scenario: [Edge case]
- **WHEN** [boundary condition or failure mode]
- **THEN** [required behavior]

### Requirement: [Name]  (P2)

[...]

- [NEEDS CLARIFICATION: e.g. retention period? approval authority? budget ceiling?]

## Non-Functional / Constraints

- [Deadlines, budget, quality bars, compliance, capacity — with exact values]

---

## DELTA FORMAT (when modifying an existing living spec)

Write only the changes, under these headers:

## ADDED Requirements
[New `### Requirement:` blocks, full content]

## MODIFIED Requirements
[Copy the ENTIRE existing requirement block from `specs/<capability>/spec.md`,
then edit to the new behavior. Header text must match the original. Partial
copies lose detail when merged at archive time.]

## REMOVED Requirements
### Requirement: [Name]
**Reason:** [why]
**Migration:** [what replaces it / what people do instead]
