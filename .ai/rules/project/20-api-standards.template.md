<!--
TEMPLATE — copy to 20-api-standards.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: FILE-MATCH on config.yml → paths.file_match.api.

Generate this if the project exposes ANY callable surface: HTTP endpoints, RPC methods, GraphQL resolvers,
CLI commands, queue consumers, webhook receivers, or scheduled jobs. "API" here means any boundary that
accepts input from outside the process.

Evidence rule: the handler shape below is copied from a real handler that already exists, cited by path. A
shape you designed rather than observed will conflict with every existing handler and fail review.

Keep it under ~140 lines.
-->

# API Standards — <project name>

Applies to every boundary that accepts input from outside the process. Security rules that apply
everywhere are in [`../50-security.md`](../50-security.md); this file is the project's concrete shape.

## Surfaces

| Surface | Protocol | Where handlers live | Auth model |
|---------|----------|--------------------|-----------|
| | | `path` | |

## House shape

Every handler follows this. Copied from `path:line`.

```
<the real skeleton: method/verb check, input parse, validation, authorisation,
 the operation, response construction, error handling — in the project's own
 language and idioms>
```

Rules that hold for every handler:

1. **Validate before use.** Never index into an unvalidated payload.
2. **Authorise at the point of data access**, not once at entry.
3. **One responsibility.** Business logic lives in a service or module that is testable without the
   transport layer.
4. **No raw environment reads.** Go through the validated accessor.
5. **Structured errors** with the correct status, never a leaked internal.
6. **Deny by default.** A new surface is unreachable until a rule permits it.

## Request validation

| | |
|---|---|
| Validator | <library and where schemas live> |
| Where validation happens | <at the boundary, before anything else> |
| Shared validators | `path` |
| On failure | <the exact response shape and status> |

| Check | Rule |
|-------|------|
| Shape | reject before indexing into the payload |
| Type / format | explicit parser or pattern, never a truthiness check |
| Size and range | length caps, numeric bounds, collection-size caps, payload-size cap |
| Unknown fields | <rejected or stripped — state which> |
| Domain rules | valid for this actor, in this state |

## Response contract

| | |
|---|---|
| Success envelope | <the exact shape> |
| Error envelope | <the exact shape> |
| Pagination | <the convention: cursor or offset, parameter names, metadata returned> |
| Empty collections | <an empty collection, never null> |
| Timestamps | <format and timezone> |
| Identifiers | <format, and whether they are exposed> |
| Money / decimals | <representation — never a float for currency> |

## Status codes

| Situation | Code |
|-----------|------|
| Success, resource returned | |
| Success, resource created | |
| Success, no body | |
| Validation failure | |
| Unauthenticated | |
| Authenticated but not permitted | |
| Not found | |
| Method / verb not allowed | |
| Conflict | |
| Payload too large | |
| Rate limited | |
| Unhandled server error | |

**Do not conflate "not found" with "not permitted"** where the distinction leaks existence. State which the
project returns and why.

## Authentication and authorisation

| | |
|---|---|
| Mechanism | `path:line` |
| Where the check happens | |
| How the actor is resolved | |
| Ownership scoping | <how a query is bounded to the caller's own data> |
| Permission map | `path` |
| Public surfaces | <the explicit list — anything not on it requires auth> |

**Changing a permission is an irreversible action.** Stop and ask (**F6**).

## Idempotency and retries

| | |
|---|---|
| Which operations must be idempotent | |
| How idempotency is achieved | <key, natural uniqueness, conditional write> |
| Retry-safe vs retry-unsafe | <the list — anything sending mail, charging, or incrementing a sequence is unsafe> |
| Client retry guidance | |

A retry of a non-idempotent operation is a second real event. Confirm what the first call did before
repeating it.

## Rate limiting and abuse

| Surface class | Limit | Enforced at |
|---------------|-------|-------------|
| Authentication | | |
| Search / list | | |
| Export / bulk read | | |
| Write endpoints | | |

Anything enumerable or expensive is limited. An unbounded list parameter is a denial-of-service vector.

## Cross-origin and headers

| | |
|---|---|
| Allowed origins | <explicit list, from configuration> |
| Credentialed requests | <policy> |
| Required security headers | |

A wildcard origin on anything carrying credentials is a defect, not a configuration choice.

## Versioning and deprecation

| | |
|---|---|
| Versioning scheme | <or "unversioned", stated deliberately> |
| Breaking change policy | |
| Deprecation window | |
| How consumers are notified | |

**Renaming a response field is a breaking change.** Add the new field, migrate consumers, then remove the
old one in a separately approved cutover — never a rename in place.

## Observability

| | |
|---|---|
| What every handler logs | <identifiers and outcome — never payloads or secrets> |
| Correlation / request ID | |
| Metrics emitted | |
| Error reporting | |

## Testing a boundary

| Layer | Covers |
|-------|--------|
| Unit | the extracted logic, without transport or store |
| Integration | the real handler: validation, auth, response shape, error paths |
| Contract | the response shape consumers depend on |

Every new endpoint gets, at minimum: the happy path, one validation failure, one unauthenticated call, and
one authenticated-but-forbidden call. An endpoint tested only on its happy path is untested where it
matters.

## Don't

| Don't | Do instead |
|-------|-----------|
| | |
