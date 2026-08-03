<!--
FORMAT — infrastructure / DevOps task. Copy the checklist blocks into the task's block in task_details.md.

Applies to CI pipelines, infrastructure-as-code, environments, secrets management, releases, monitoring,
containers, and anything that changes how the software is built, shipped, or run.

THIS IS THE FORMAT WHERE F6 (IRREVERSIBLE ACTION) IS THE DEFAULT CASE, NOT THE EXCEPTION. Most other
disciplines produce reversible edits; here a single applied change can destroy a resource that holds the only
copy of something.

THE THREE THINGS INFRASTRUCTURE TASKS FAIL ON MOST:
  1. Apply without reading the plan. The plan said "replace", which for a stateful resource means destroy.
  2. A change tested in one environment and assumed identical in another. Environments drift.
  3. A rollback path that was never tested, discovered to be theoretical at the worst moment.

NEVER RUN AN APPLY, DEPLOY, OR DESTROY WITHOUT EXPLICIT APPROVAL FOR THAT SPECIFIC ACTION. Plan and preview
are read-only and always allowed.
-->

# Infrastructure / DevOps Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| CI, pipelines, infrastructure-as-code, environments, secrets wiring, releases, monitoring, containers, runtime configuration | Application logic (backend format), schema migrations (database format) |

## 1. Change definition

| | |
|---|---|
| What changes | |
| Layer | CI / IaC / container / runtime config / monitoring / release process |
| Environments affected | local / dev / staging / **production** |
| **Reversibility** | reversible / compensable / **irreversible** |
| Blast radius if it goes wrong | |
| Requirement | R<n> |
| **Approval required for apply** | ☐ |
| Change window / freeze in effect | |

Any row where "environments affected" includes production is a hard stop until approved. Production is not a
place where a change is tried out.

## 2. Plan before apply 🔴

| # | Check | Result |
|---|-------|--------|
| 1 | `plan` / `preview` / `diff` run, and its **full output read** | |
| 2 | Every resource marked for **destroy** or **replace** identified by name | |
| 3 | For each: does it hold state that cannot be recreated? | |
| 4 | Resource count matches expectation — no surprise additions or removals | |
| 5 | No unrelated drift being silently corrected in the same apply | |
| 6 | The plan output pasted into the task or the execution report | |

### Resources destroyed or replaced

| Resource | Action | Holds unrecoverable state? | Backup taken | Approved |
|----------|--------|---------------------------|--------------|----------|
| | destroy / replace | | ☐ | ☐ |

**"Replace" means destroy then create.** For anything stateful — a volume, a database, a queue with unread
messages, a certificate — that is data loss. This table existing and being read is the control.

Check 5 matters because an apply corrects *all* drift, not just your change. Unrelated drift in the plan is
someone else's change, and applying it is not yours to decide.

## 3. Idempotency and repeatability

| # | Check | Result |
|---|-------|--------|
| 1 | Running the change twice produces the same result | |
| 2 | State is stored where the team expects, not locally | |
| 3 | State locking in place so two concurrent applies cannot corrupt it | |
| 4 | No manual step required that is not documented | |
| 5 | The change is expressed in code, not clicked in a console | |

A console change that is not in code will be reverted by the next apply, silently. That is drift with a
guaranteed future outage.

## 4. Secrets and configuration

| # | Check | Result |
|---|-------|--------|
| 1 | **No secret value in any file, template, log, or output** | |
| 2 | Secrets referenced from the project's secret store, by name | |
| 3 | New required keys added to the example/template file | |
| 4 | Secrets not printed by the pipeline, including in verbose or debug mode | |
| 5 | Rotation possible without a code change | |
| 6 | Least privilege on every credential this change creates or uses | |

| New key | Where stored | Consumed by | In the example file |
|---------|-------------|-------------|--------------------|
| | | | ☐ |

A pipeline that echoes its environment on failure will leak every secret it holds. Check 4 means reading the
pipeline's failure path, not just its success path.

## 5. Pipeline changes

| # | Check | Result |
|---|-------|--------|
| 1 | **All four gates still run**, and still fail the build when they fail | |
| 2 | No gate weakened, skipped, or made non-blocking to get the pipeline green | |
| 3 | Caching does not mask a real failure or serve stale artefacts | |
| 4 | Runtime and dependency versions pinned, not floating | |
| 5 | Pipeline runs on the events intended, and not on others | |
| 6 | Concurrency handled — two runs cannot deploy over each other | |
| 7 | Third-party actions or plugins pinned to a version or digest, not a moving tag | |
| 8 | Duration acceptable, and slow steps parallelised or cached deliberately | |

Check 2 is the important one. `continue-on-error` on a gate converts a caught failure into an uncaught one —
the same defect as widening a type to pass a static check.

Check 7 is a supply-chain control: a moving tag on a third-party action means someone else can change what
runs in your pipeline, with your credentials.

## 6. Environments

| Environment | Changed | Verified | Notes |
|-------------|---------|----------|-------|
| local | | | |
| dev | | | |
| staging | | | |
| **production** | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Change applied to a lower environment first | |
| 2 | **Differences between that environment and production identified**, not assumed absent | |
| 3 | What the lower environment cannot prove, stated | |
| 4 | Configuration difference between environments is data, not divergent code | |

### Environment differences that matter here

| Difference | Consequence for this change |
|------------|----------------------------|
| | |

"It worked in staging" is evidence only to the extent staging resembles production. Naming the gap is what makes
the evidence honest.

## 7. Rollback 🔴

| | |
|---|---|
| **Rollback procedure, written out step by step** | |
| Time to roll back | |
| **Tested?** | ☐ |
| What cannot be rolled back | |
| Data implications of rolling back | |
| Who can execute it | |

An untested rollback is a hypothesis. If it cannot be tested, say so — that is a residual risk worth stating
before the apply, not after.

## 8. Observability

| # | Check | Result |
|---|-------|--------|
| 1 | The change is visible in monitoring — you can tell it took effect | |
| 2 | Alerts exist for the failure mode this change introduces | |
| 3 | Logs sufficient to diagnose a failure of this change | |
| 4 | Health check reflects real health, not just process liveness | |
| 5 | No new alert that will fire routinely and be ignored | |

Check 5: an alert that cries wolf trains everyone to dismiss the channel it arrives on.

## 9. Cost and capacity

| # | Check | Result |
|---|-------|--------|
| 1 | Resources created, and their ongoing cost | |
| 2 | Autoscaling bounds set — a floor and a **ceiling** | |
| 3 | Retention and lifecycle policies set on anything that accumulates | |
| 4 | Nothing left running that was only needed temporarily | |

## 10. Security

| # | Check | Result |
|---|-------|--------|
| 1 | Nothing exposed publicly that should not be — **and if it is, stated explicitly** | |
| 2 | Network rules least-privilege, not permissive defaults | |
| 3 | Encryption in transit and at rest | |
| 4 | Roles and policies scoped to what is needed | |
| 5 | Container images from a trusted source, pinned by digest, scanned | |
| 6 | No credential in an image layer or build argument | |
| 7 | Audit logging enabled on anything that changes state | |

## 11. Verification 🔴

| # | Check | Result |
|---|-------|--------|
| 1 | Validate / lint gate on the IaC or pipeline definition | |
| 2 | Plan output read in full and pasted | |
| 3 | Applied to a lower environment and verified working | |
| 4 | Applied twice — second run is a no-op | |
| 5 | Rollback executed in a lower environment | |
| 6 | Monitoring confirms the intended effect | |
| 7 | Application gates still pass after the change | |
| 8 | Approval obtained before any production apply | ☐ |

## 12. Ripple effects

| Affected | Where | How | Handled by |
|----------|-------|-----|-----------|
| | | | |

Check specifically: other pipelines sharing this configuration · services depending on a changed resource
name, endpoint or port · anything reading a renamed environment variable · downstream deploys triggered by
this one · shared modules or templates used elsewhere · documentation describing the old process · developer
setup instructions.

## 13. Done when

| # | Criterion |
|---|-----------|
| 1 | Plan read in full; every destroy and replace identified and approved |
| 2 | Change is in code, idempotent, and safe to run twice |
| 3 | No secret in any file, log, or output |
| 4 | All four gates still run and still block on failure |
| 5 | Verified in a lower environment, with the differences from production named |
| 6 | Rollback written and tested, or its untestability stated |
| 7 | Monitoring confirms the effect; no noisy new alert |
| 8 | Ripple effects handled, including documentation |
| 9 | **Explicit approval obtained for every production action** |
| 10 | No follow-up open |
