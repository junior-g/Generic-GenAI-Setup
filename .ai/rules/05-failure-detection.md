# Failure Detection

**Load mode: ALWAYS.** A runtime self-check, not a reference document. It is short on purpose, because a
table you have to go and fetch is a table you do not consult.

If a signal below matches what you just saw or what you are about to do, apply the first move
**immediately**. `F<n>` codes are stable across every project using this framework. Detail:
[`../knowledge/failure-taxonomy.md`](../knowledge/failure-taxonomy.md). Remediation:
[`84-failure-playbooks.md`](84-failure-playbooks.md).

---

## Signal → code → first move

| Signal | Code | First move |
|--------|------|-----------|
| Stating that a file, symbol, or behaviour exists — or doesn't — without having read it this session | **F5** | Read it. A negative needs two checks by different methods |
| A tool result contradicts something you already claimed or wrote | **F1** | Correct it at the source, then re-walk everything derived from it |
| The same edit or command failed twice the same way | **F3** | Stop patching. State what the failures rule out, then change approach |
| Reading directories wide, or opening the same file a third time | **F4 F12** | Targeted retrieval instead; pin the file in the feature steering file |
| A long stretch of planning or reviewing with no file written and no gate run | **F7** | Commit to the smallest concrete next action |
| Wanting one more round after a cap | **F7** | Caps are hard. Close the stage; carry items as residual risks |
| Building without re-reading the objective and its requirement block this session | **F2** | Re-pin the feature steering file and the requirement before continuing |
| About to report done with follow-ups open or a gate unrun | **F9** | Not done. Burn down follow-ups, run all four gates |
| Accepting a stated assumption you have not verified | **F8** | Verify it. A review round emits findings or names the files read |
| Retrying anything that already had an external effect | **F6** | Confirm it is idempotent first |
| An instruction given early is slipping out of view in a long session | **F10** | Re-state it at the end of the working context |
| Guessing a type, schema, or response shape | **F13** | Read the declaration |
| Two rules or documents instructing differently | **F11** | Stop. Report the conflict and ask which wins |
| Session running well past its declared budget | **F12** | Report progress and remaining budget; propose phasing |
| Output truncated mid-structure, or early instructions no longer influencing behaviour | **F4** | Stop adding context. Prune, re-pin, re-state the constraint |
| Agreeing with the user's framing because it is theirs | **F8** | Separate the claim from who made it, then check the claim |
| A document you are relying on is missing, incomplete, or contradicted by the code | — | Pause, fix the doc, update `INDEX.md`, resume. [`65-auto-healing-docs.md`](65-auto-healing-docs.md) |

## Hard stops — ask, never proceed

Dropping or renaming live data · deleting a live route, endpoint, component, field, or permission ·
changing roles or authentication · sending mail or notifications · anything touching money or a billable
third party · any removal during feature work · rewriting shared git history — all **F6**.

A reported defect that will not reproduce — **F5 F8**.
A 🔴 finding still open at a round cap — **F7**.
Two documents instructing differently — **F11**.

## Reporting

When a signal fires and changes what you do, say so in one line: **the code, the signal, the move.**

> `F5` — I was about to state that no rate limiter exists based on one scoped search. Read the middleware
> chain and the dependency manifest instead; there is one, at `src/http/limiter.ts:34`.

Log it in the section's execution report under Discoveries, or in the fix report's attempt log, so the
pattern becomes visible instead of being absorbed. The pattern of what goes wrong is worth more than any
individual clean report.

## Telling look-alikes apart

Symptoms overlap. The discriminating checks are in
[`84-failure-playbooks.md`](84-failure-playbooks.md#which-one-is-it--look-alike-pairs). The pairs that
matter most:

| What you see | Could be | Quick discriminator |
|--------------|----------|---------------------|
| Output doesn't match the expected shape | **F13** or **F4** | Cut off mid-structure → F4. Complete but wrong → F13 |
| No progress for a long stretch | **F3** or **F7** | Same call, same error → F3. Many different read-only calls → F7 |
| A claim about the code turns out false | **F5** or **F1** | Yours, never verified → F5. Inherited from an earlier wrong claim → F1 |
| Declared complete but it isn't | **F9** or **F8** | A verification step was never run → F9. Jumped to agreement → F8 |
| Budget gone, little shipped | **F12** or **F7** | Spent on reading → F12. Spent on planning → F7 |
