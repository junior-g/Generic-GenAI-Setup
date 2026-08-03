<!--
FORMAT — backend / API task. Copy the checklist blocks into the task's block in task_details.md.

"API" here means any boundary that accepts input from outside the process: HTTP endpoints, RPC methods,
GraphQL resolvers, CLI commands, queue consumers, webhook receivers, scheduled jobs.

Project specifics — which validator, which auth middleware, the exact handler skeleton — come from
rules/project/20-api-standards.md.

THE THREE THINGS BACKEND TASKS FAIL ON MOST:
  1. Authorisation checked once at entry rather than at the point of data access, so a valid actor reads
     another actor's records.
  2. Only the happy path tested. An endpoint tested on success alone is untested where it matters.
  3. A response field renamed in place, breaking a consumer nobody enumerated.
-->

# Backend / API Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| Endpoints, handlers, resolvers, commands, consumers, jobs, service logic, authorisation | Rendering (UI format), schema changes (database format), third-party calls (integration format) |

## 1. Surface definition

| | |
|---|---|
| Operation | |
| Kind | endpoint / RPC / resolver / command / consumer / job |
| Path or name | |
| Verb / trigger | |
| Actors permitted | |
| Requirement | R<n> |
| Public or authenticated | |
| Idempotent | yes / no |
| Rate limited | |

## 2. Handler shape

| # | Check | Result |
|---|-------|--------|
| 1 | Follows the project's house handler shape | |
| 2 | Method / verb / trigger validated before anything else | |
| 3 | Input parsed and validated **before** any field is used | |
| 4 | Authorisation checked **at the point of data access**, not only at entry | |
| 5 | Business logic in a service or module testable without the transport layer | |
| 6 | No raw environment read — the validated accessor is used | |
| 7 | Response built from an explicit shape, not a leaked internal object | |
| 8 | Errors caught, mapped, and never swallowed into a hiding default | |

Check 7 matters more than it looks: returning an internal object leaks whatever field someone adds to it later,
including fields that should never be public.

## 3. Input validation

| Input | Type | Required | Bounds | Validated where | On failure |
|-------|------|----------|--------|-----------------|-----------|
| | | | | `path` | |

| # | Check | Result |
|---|-------|--------|
| 1 | Shape validated before indexing into the payload | |
| 2 | Types checked with a parser or pattern, not a truthiness test | |
| 3 | **Bounds on every string length, number range, and collection size** | |
| 4 | Payload size capped | |
| 5 | Unknown fields handled per the project's policy — rejected or stripped, deliberately | |
| 6 | Allowlist, not denylist | |
| 7 | Domain rules checked — valid for this actor, in this state | |
| 8 | Nested and array elements validated, not just the top level | |

Check 3 is a denial-of-service control, not a tidiness one. An unbounded list parameter is an outage waiting for
a bored caller.

## 4. Authorisation

| Action | Actor allowed | Check location | Ownership scoping |
|--------|--------------|----------------|-------------------|
| | | `path:line` | |

| # | Check | Result |
|---|-------|--------|
| 1 | Deny by default — unreachable until a rule permits it | |
| 2 | Actor resolved from a trusted source, never from request-supplied identity | |
| 3 | **Every query scoped to the actor's own boundary** | |
| 4 | Ownership verified, not just role | |
| 5 | Not relying on an identifier being unguessable | |
| 6 | Not-found vs not-permitted chosen deliberately, per project policy | |
| 7 | Permission changes — **stopped and asked** (**F6**) | |

## 5. Response contract

| Outcome | Status / result | Body shape | Notes |
|---------|-----------------|-----------|-------|
| Success | | | |
| Created | | | |
| Validation failure | | | |
| Unauthenticated | | | |
| Forbidden | | | |
| Not found | | | |
| Conflict | | | |
| Rate limited | | | |
| Server error | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Envelope matches the project's convention | |
| 2 | Empty collections return an empty collection, never null | |
| 3 | Pagination follows the project's convention, with a page-size cap | |
| 4 | Timestamps, decimals and identifiers formatted per convention | |
| 5 | Money never a binary float | |
| 6 | **No field renamed in place** — added, migrated, then removed at cutover | |
| 7 | Errors leak nothing: no stack trace, no query text, no internal identifier, no user enumeration | |

## 6. Idempotency and retries

| # | Check | Result |
|---|-------|--------|
| 1 | Is this operation idempotent? | yes / no |
| 2 | If not — the consequence of a retry, stated | |
| 3 | Mechanism where required: key, natural uniqueness, or conditional write | |
| 4 | Concurrent duplicate submissions handled | |
| 5 | Partial failure leaves no half-applied state | |
| 6 | Anything with an external effect is not blindly retried (**F6**) | |

## 7. Data access

| # | Check | Result |
|---|-------|--------|
| 1 | Access via the project's declared accessor, not a new connection | |
| 2 | **Queries parameterised — no string interpolation** | |
| 3 | Every new query pattern has an index, or a stated reason it does not need one | |
| 4 | No N+1 pattern | |
| 5 | Transaction boundaries correct for the invariants involved | |
| 6 | Audit fields written on create and update | |
| 7 | Soft-delete respected on every read path, if the project uses it | |

## 8. Observability

| # | Check | Result |
|---|-------|--------|
| 1 | Outcome logged with identifiers | |
| 2 | **No payload, secret, token, or personal data logged** | |
| 3 | Correlation identifier propagated | |
| 4 | Errors reported to the project's channel with enough context to act on | |
| 5 | Security-relevant events audited: auth outcome, permission use, export, deletion | |

## 9. Performance

| # | Check | Result |
|---|-------|--------|
| 1 | Query count per request known and bounded | |
| 2 | Response size bounded | |
| 3 | Timeout set on every outbound call | |
| 4 | Nothing unbounded: no unpaginated list, no unbounded fan-out | |
| 5 | Rate limit appropriate to the cost of the operation | |

## 10. Testing 🔴

**Minimum four cases per boundary.** An endpoint tested only on its happy path is untested where it matters.

| # | Case | Level | Result |
|---|------|-------|--------|
| 1 | Happy path | integration | |
| 2 | Validation failure | integration | |
| 3 | Unauthenticated | integration | |
| 4 | Authenticated but forbidden | integration | |
| 5 | Not found | integration | |
| 6 | Duplicate / concurrent submission | integration | |
| 7 | Dependency unavailable | unit or integration | |
| 8 | Extracted logic, per branch | unit | |

| | |
|---|---|
| Pure logic extracted to | `path` |
| Response shape asserted, field by field | ☐ |
| Authorisation asserted with a **wrong-owner** case, not only a wrong-role case | ☐ |

## 11. Ripple effects

| Affected | Path | How | Handled by |
|----------|------|-----|-----------|
| | `path:line` | | |

Check specifically: every client reading a changed response field · generated clients or typed contracts ·
existing tests asserting the old shape · anything constructing this request · documentation and schema files ·
other handlers sharing the changed service function · fixtures.

## 12. Done when

| # | Criterion |
|---|-----------|
| 1 | Handler follows the house shape |
| 2 | Every input validated, with bounds |
| 3 | Authorisation at the point of data access, ownership-scoped |
| 4 | Every outcome in §5 returns the right status and shape |
| 5 | Idempotency handled or its absence stated |
| 6 | No secret or payload logged |
| 7 | All four minimum test cases pass, plus applicable extras |
| 8 | Ripple effects handled |
| 9 | All four gates green |
| 10 | No follow-up open |
