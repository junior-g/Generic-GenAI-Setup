<!--
FORMAT — integration task. Copy the checklist blocks into the task's block in task_details.md.

Applies to anything crossing a process boundary you do not control: third-party APIs, webhooks, message
queues, event streams, scheduled jobs calling out, file transfer, payment providers, mail and notification
services.

THE DEFINING PROPERTY: the other side can fail, be slow, be duplicated, arrive out of order, or change its
contract without telling you. Every check here follows from that.

THE THREE THINGS INTEGRATION TASKS FAIL ON MOST:
  1. Retrying something that already had an effect. The first call succeeded and the response was lost.
  2. An unverified webhook, which is an unauthenticated endpoint that mutates state.
  3. No timeout, so one slow dependency exhausts the connection pool and takes down everything else.

ANYTHING THAT SENDS MAIL, MOVES MONEY, OR TRIGGERS A BILLABLE THIRD-PARTY ACTION IS A HARD STOP (F6).
-->

# Integration Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| Third-party APIs, webhooks, queues, event streams, outbound jobs, file transfer, providers | Internal handlers (backend format), schema (database format) |

## 1. Integration definition

| | |
|---|---|
| Party / service | |
| Direction | outbound call / inbound webhook / queue publish / queue consume / file exchange |
| Contract documentation | <url or path — **read, not assumed**> |
| Authentication | |
| Sandbox available | |
| **Has external effects?** | <mail / money / notification / provisioning — **or none**> |
| **Approval required** | ☐ |
| Requirement | R<n> |
| Rate limits imposed by the other side | |
| SLA or expected latency | |

If "has external effects" is anything other than none, this task is a hard stop until approved, and testing
happens against a sandbox — never against the live service.

## 2. Contract

| # | Check | Result |
|---|-------|--------|
| 1 | Their documentation **read**, and the version noted | |
| 2 | Request and response shapes recorded, field by field | |
| 3 | Every documented error code enumerated with our handling | |
| 4 | Their pagination, filtering and sorting semantics understood | |
| 5 | Their idempotency support established — key, natural, or none | |
| 6 | Their rate limits and what they return when exceeded | |
| 7 | Versioning and deprecation policy known | |
| 8 | Fields we depend on, marked — a change to any is a breaking change for us | |

| Their error | Means | Our response | Retry? |
|-------------|-------|-------------|--------|
| | | | yes / no |

**Their contract is a claim until verified against a real call.** Documentation lags implementation on every
service; a sandbox call is what establishes the real shape (**F13**).

## 3. Outbound calls

| # | Check | Result |
|---|-------|--------|
| 1 | **Timeout set** — connect and read, both | |
| 2 | Retry policy: which errors, how many attempts, what backoff | |
| 3 | **Backoff with jitter**, not a fixed interval | |
| 4 | Retries bounded — a ceiling on attempts and total elapsed time | |
| 5 | Non-retryable errors identified and **not retried** | |
| 6 | Circuit breaker or equivalent, if failure would cascade | |
| 7 | Response validated before use, not trusted by shape | |
| 8 | Their outage degrades us gracefully, rather than failing everything | |
| 9 | Credentials from the secret store, never inline | |
| 10 | Called from a layer that is allowed to make outbound calls | |

Check 1 has no exception. A call without a timeout waits forever, holds a connection, and one slow dependency
becomes a full outage. Check 3 prevents a synchronised retry storm from every client at once.

## 4. Idempotency 🔴

**The section that prevents duplicate real-world effects.**

| | |
|---|---|
| Is this operation idempotent on their side? | yes / no / unknown |
| How established | <their docs, plus a sandbox test> |
| Our idempotency key | <how it is derived — must be deterministic> |
| Key stored where, for how long | |
| **Consequence of an accidental duplicate** | |
| Are we the source of truth for "already done"? | |

| # | Check | Result |
|---|-------|--------|
| 1 | Every retryable call carries a deterministic idempotency key | |
| 2 | The key is stable across retries — not regenerated per attempt | |
| 3 | A duplicate is detected and short-circuited before the call | |
| 4 | Non-idempotent operations are **not** in the automatic retry path | |
| 5 | The failure-after-effect case handled: their side succeeded, we never got the response | |

Check 5 is the trap. From our side, "succeeded but the response was lost" and "never happened" look identical.
The only resolution is a query before the retry, or an idempotency key their side honours.

## 5. Inbound webhooks

| # | Check | Result |
|---|-------|--------|
| 1 | **Signature or token verified before any processing** | |
| 2 | Verification uses a constant-time comparison | |
| 3 | Replay prevented — timestamp window, or a seen-event store | |
| 4 | Payload validated like any other untrusted input | |
| 5 | Duplicate delivery handled — they will retry, by design | |
| 6 | Out-of-order delivery handled, or ordering not depended on | |
| 7 | Responds fast; heavy work queued rather than done inline | |
| 8 | Correct status returned so they retry when we want them to, and not when we don't | |
| 9 | Unknown event types ignored safely, not treated as errors | |
| 10 | Endpoint documented as public and its verification named | |

**An unverified webhook is an unauthenticated endpoint that changes your data.** Check 1 is not optional, and
it happens before parsing, not after.

## 6. Queues and event streams

| # | Check | Result |
|---|-------|--------|
| 1 | Delivery semantics known: at-least-once, at-most-once, exactly-once | |
| 2 | Consumer idempotent — at-least-once means duplicates will arrive | |
| 3 | Ordering guarantees known, and not assumed stronger than they are | |
| 4 | Dead-letter destination configured, and **someone looks at it** | |
| 5 | Poison message cannot block the queue indefinitely | |
| 6 | Visibility timeout longer than the real processing time | |
| 7 | Backlog observable and alerted | |
| 8 | Consumer failure does not lose the message | |
| 9 | Message schema versioned, so old and new coexist during rollout | |

Check 4's second half matters: a dead-letter queue nobody monitors is a silent data-loss mechanism with extra
steps.

## 7. Scheduled and background work

| # | Check | Result |
|---|-------|--------|
| 1 | Overlapping runs prevented, or safe | |
| 2 | A missed run is recoverable — catch-up behaviour defined | |
| 3 | Failure is visible, not silent | |
| 4 | Bounded runtime; a stuck run is detected | |
| 5 | Timezone and daylight-saving behaviour explicit | |
| 6 | Safe to run manually, out of schedule | |

## 8. Data mapping

| Our field | Their field | Transform | Missing-value behaviour |
|-----------|-------------|-----------|------------------------|
| | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Every mapped field's type and format confirmed against a real response | |
| 2 | Their optional fields handled as genuinely absent | |
| 3 | Their enumerated values mapped, with an unknown-value fallback | |
| 4 | Units, currency, precision and timezone reconciled | |
| 5 | Their identifiers stored so a record can be reconciled later | |
| 6 | No personal data sent beyond what the integration requires | |

Check 3: their new enum value will arrive before your code knows about it. Unknown-value handling decides
whether that is a log line or an exception.

## 9. Security

| # | Check | Result |
|---|-------|--------|
| 1 | Credentials in the secret store, referenced by name | |
| 2 | Transport encrypted; certificate validation not disabled | |
| 3 | Their response treated as untrusted input | |
| 4 | We send the minimum data required | |
| 5 | Nothing logged that contains their payload or our credentials | |
| 6 | Their identifiers not exposed to our users unless intended | |
| 7 | Outbound URL not caller-controlled — no request forgery path | |

## 10. Failure behaviour

| Failure | Detection | Our behaviour | User sees |
|---------|-----------|--------------|-----------|
| Timeout | | | |
| Their 5xx | | | |
| Their 4xx | | | |
| Rate limited | | | |
| Auth expired or revoked | | | |
| Malformed response | | | |
| Contract changed unexpectedly | | | |
| Total outage | | | |

## 11. Verification 🔴

| # | Check | Result |
|---|-------|--------|
| 1 | Real call made against sandbox; actual response shape recorded | |
| 2 | Response shape asserted in a test, field by field | |
| 3 | Each failure mode in §10 simulated and handled | |
| 4 | Timeout behaviour verified with a deliberately slow or unreachable endpoint | |
| 5 | Retry behaviour verified — count, backoff, and the ceiling | |
| 6 | **Duplicate delivery or duplicate call verified to have no double effect** | |
| 7 | Webhook verification tested with a **bad signature**, and rejected | |
| 8 | Tests do not call the live third party | |
| 9 | Nothing with an external effect executed without approval | ☐ |

Check 8: a test suite that calls a real third party is flaky, costs money, and eventually sends something real.
Record fixtures from a sandbox call and assert against those.

## 12. Ripple effects

| Affected | Where | How | Handled by |
|----------|-------|-----|-----------|
| | `path:line` | | |

Check specifically: other code paths calling the same service · shared client or credential configuration ·
anything depending on the timing of this integration · monitoring and alerts referencing it · queue consumers
of events this now publishes · retry budgets shared across integrations.

## 13. Done when

| # | Criterion |
|---|-----------|
| 1 | Their contract read and verified against a real sandbox response |
| 2 | Timeout on every outbound call, bounded retries with jittered backoff |
| 3 | Idempotency handled, including the failure-after-effect case |
| 4 | Webhooks verified before processing; bad signature rejected in a test |
| 5 | Duplicate and out-of-order delivery handled |
| 6 | Every failure mode in §10 has a defined, tested behaviour |
| 7 | No credential or payload in logs; nothing exposed that should not be |
| 8 | Tests never touch the live third party |
| 9 | Ripple effects handled |
| 10 | Nothing with an external effect executed without approval; all four gates green |
