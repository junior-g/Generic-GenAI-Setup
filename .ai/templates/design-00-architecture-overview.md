<!--
TEMPLATE — architecture overview. Copy to <artefact_root>/<slug>/design/00-architecture-overview.md.
Delete this header. Written at step A5 (always) / B5 (only if more than two surfaces interact).

PURPOSE: the cross-cutting view. Per-surface detail goes in design-0N-<topic>.md; this file holds what no
single section owns — the runtime map, the reversibility ledger, and the decisions that bind every section.

THE RUNTIME FEASIBILITY SECTION IS MANDATORY AND IS THE MOST VALUABLE PART. Assumed feasibility — "this layer
can reach that resource" — is the design defect that survives review and surfaces at execution, when it costs
a re-plan rather than a paragraph.

Sections are not deleted. Write None or Not applicable.
-->

# Architecture Overview — <slug>

| | |
|---|---|
| Requirement | `../final_requirement.md` v<n>, approved <date> |
| Sections | <n> design documents |
| Requirements covered | R1–R<n> |
| Status | draft / reviewed round <n> / approved |

## 1. Shape of the change

<Two to four sentences. What is being added, where it sits, what it talks to.>

```mermaid
graph TD
```

## 2. Section map

The design documents and what each owns. One section per surface or coherent concern.

| Doc | Owns | Requirements | Depends on |
|-----|------|--------------|-----------|
| `design-01-<topic>.md` | | R1, R2 | — |
| `design-02-<topic>.md` | | R3 | 01 |

## 3. Runtime feasibility 🔴

**Mandatory.** For every boundary crossing this design assumes, state whether it is actually possible — in
this environment, with this permission, within this latency.

| From | To | Possible? | Evidence | If not, alternative |
|------|----|-----------|----------|--------------------|
| <layer / runtime> | <resource> | ✅ / ❌ | `path:line` or documented limit | |

Crossings worth checking explicitly: an edge or middleware layer reaching a database · a client-side context
reading a server-only secret · a build-time step needing request-time data · a serverless function holding
state between invocations · a background job reaching a request-scoped session · a component in one runtime
importing a module compiled for another.

**Evidence, not confidence.** "It should work" is the wrong entry in this table.

## 4. Data flow

```mermaid
sequenceDiagram
```

| # | Step | Runtime | Reads | Writes | Failure behaviour |
|---|------|---------|-------|--------|-------------------|
| | | | | | |

## 5. Reversibility ledger 🔴

**Every action this design introduces, classified before anything is built.** This is the ledger that routes
work to the cutover step instead of letting a deletion ride inside a feature section (**F6**).

| # | Action | Class | Why | Approval needed | Cutover? |
|---|--------|-------|-----|-----------------|----------|
| 1 | | pure / reversible / compensable / **irreversible** | | ☐ | yes / no |

| Class | Meaning |
|-------|---------|
| Pure | no world effect |
| Reversible | local and undoable |
| Compensable | needs a deliberate, designed undo |
| **Irreversible** | cannot be undone — **needs approval and goes to cutover** |

### Idempotency

| Operation | Idempotent? | Mechanism | Consequence of a blind retry |
|-----------|-------------|-----------|------------------------------|
| | yes / no | key / natural uniqueness / conditional write | |

Any operation that is not idempotent is listed here, because a retry of it is a second real event.

## 6. Cross-cutting decisions

Decisions that bind every section, made once here so they are not re-decided inconsistently.

| # | Decision | Options considered | Chosen | Why | Affects |
|---|----------|--------------------|--------|-----|---------|
| D1 | | | | | all sections |

## 7. Reuse

| Existing thing | Path | Verdict | Why |
|----------------|------|---------|-----|
| | `path:line` | reuse / extend / new | |

Reuse is decided **after** the sections enumerate what they need — deciding it first produces a design shaped
by what exists rather than by what is required.

### New shared components introduced

| Component | Used by | Lives at | Justification |
|-----------|---------|----------|---------------|
| | | | |

## 8. Data model changes

Summary; the authoritative version is `../final_requirement.md` §6.

| Entity | Change | Migration needed | Section that builds it |
|--------|--------|------------------|----------------------|
| | | | S0 |

Every new entity carries the project's audit fields.

## 9. Boundary and trust map

| Boundary | Input from | Validated at | Authorised at | Trust level |
|----------|-----------|--------------|---------------|-------------|
| | | `path` | `path` | untrusted / authenticated / internal |

Every arrow from an untrusted source crosses a validation point. An arrow that does not is a finding.

## 10. Failure and degradation

| Component | If it fails | User sees | Recovery |
|-----------|------------|-----------|----------|
| | | | |

| | |
|---|---|
| Retry policy, and which operations may be retried | |
| Timeout budget | |
| Partial-failure behaviour | |
| What must never be left half-applied | |

## 11. Observability

| Signal | Emitted where | Answers |
|--------|--------------|---------|
| | | |

## 12. Non-functional compliance

| NFR | How this design meets it | Verified by |
|-----|------------------------|-------------|
| N1 | | |

## 13. Security review

From [`../rules/50-security.md`](../rules/50-security.md) — path relative to `.ai/templates/`.

| # | Check | Result | Evidence |
|---|-------|--------|----------|
| 1 | Every new input validated | | |
| 2 | Every new data access authorised, ownership-scoped | | |
| 3 | No secret in source, output, or logs | | |
| 4 | No interpolated query, command, or path | | |
| 5 | New exposed surface has auth, or the gap is stated | | |
| 6 | Cross-origin policy origin-restricted | | |
| 7 | New dependencies pinned and justified | | |
| 8 | Errors leak nothing | | |
| 9 | Personal data minimised and unlogged | | |
| 10 | State changes carry an audit trail | | |
| 11 | Rate limits on anything enumerable or expensive | | |
| 12 | Nothing irreversible without approval | | |

## 14. Out of scope for this design

- <thing>

## 15. Open design questions

| # | Question | Blocking? | Default applied |
|---|----------|-----------|-----------------|
| | | | |

## 16. Deviations from the requirement

Where the design could not do exactly what the requirement said. Each needs an amendment to the requirement,
not a silent divergence.

| Requirement | Requirement said | Design does | Why | Amendment raised |
|-------------|-----------------|-------------|-----|------------------|
| R<n> | | | | ☐ |
