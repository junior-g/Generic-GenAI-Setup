<!--
TEMPLATE — cutover. Copy to <artefact_root>/<slug>/tasks/cutover-report.md, delete this header, fill it in.
Written at step A11 / B10. AFTER every feature section is green.

THIS STEP IS REMOVALS AND RETIREMENTS ONLY. Nothing is built here.

IT CARRIES ITS OWN APPROVAL, PER ITEM. Not one approval for the cutover — one per row. The user ticks each box
individually, and an unticked box means that item does not happen.

WHY IT IS A SEPARATE STEP: feature momentum is exactly the wrong state of mind for a deletion. The two most
expensive wrong claims in this framework's history were both "removing this is impact-free", made during
feature work, both from a scoped search that returned nothing.

CONSUMERS ARE FOUND *AND READ*, WITH AN UNSCOPED SEARCH. A scoped search finds the consumers that use the name
you searched for. It does not find the one that reaches the thing another way.

Sections are not deleted. Write None or Not applicable.
-->

# Cutover Report — <slug>

| | |
|---|---|
| Track | A / B |
| Prerequisite | every feature section green — confirmed in `README.md` status table ☐ |
| Items | <n> |
| Items approved | <n> |
| Items executed | <n> |
| Status | awaiting approval / in progress / complete |

## 1. Why these items can go

<Two or three sentences. What replaced them, and where the new path was verified. If nothing replaced an item
— it is simply dead — say that explicitly and say how that was established.>

## 2. Prerequisite check

Cutover does not start until all of these are true.

| # | Condition | Met |
|---|-----------|-----|
| 1 | Every feature section complete, zero open follow-ups | ☐ |
| 2 | All four gates green on the current state | ☐ |
| 3 | The replacement path is live and verified | ☐ |
| 4 | Every `do-not-break.md` row verified green *(Track B)* | ☐ |
| 5 | Each item below has its own approval | ☐ |

---

## 3. Items

One block per item. **No item is executed without its own ticked approval box.**

### C1 — <what is being removed>

| | |
|---|---|
| **Item** | `path` — <thing> |
| **Kind** | file / component / endpoint / field / table / permission / config key / dependency / document |
| **Reversibility** | reversible / compensable / **irreversible** |
| **Replaced by** | `path` — verified in `execution-report-S<n>.md` |
| **Discovered at** | S<n> / planned from the start |

#### Pre-flight

| # | Check | Result |
|---|-------|--------|
| 1 | The replacement is live and verified | ☐ |
| 2 | **Unscoped** search run for every reference | ☐ — command: `<the command>` |
| 3 | Every consumer found was **read**, not just listed | ☐ |
| 4 | No consumer still depends on it | ☐ |
| 5 | No test asserts its existence | ☐ |
| 6 | No document references it — or the document is updated in this step | ☐ |
| 7 | No configuration, route table, or registry references it | ☐ |
| 8 | Not referenced dynamically by string, reflection, or convention | ☐ |
| 9 | Live data does not depend on it | ☐ |
| 10 | Reversal path exists and is written below | ☐ |

Check 8 is the one that catches what a search cannot: a name assembled at runtime, resolved by convention, or
read from configuration will not appear in any search for the literal name.

#### Consumers found and read

| Consumer | Path | Read? | Still depends? | Handled by |
|----------|------|-------|----------------|-----------|
| | `path:line` | ✅ | no | |

**If this table is empty**, say how absence was established — two independent checks by different methods, both
named. An empty table with no method stated is precisely the claim that has been wrong twice.

| | |
|---|---|
| Method 1 | <and result> |
| Method 2 (different) | <and result> |

#### Reversal path

<Exactly how to put this back if removing it turns out to be wrong. "Revert the commit" is acceptable **only**
when the item is purely reversible — for a dropped table or a changed permission it is not, and the real
reversal path must be written out.>

#### Approval

| | |
|---|---|
| **Approved by user** | ☐ |
| Date | |
| Conditions | |

#### Execution

| | |
|---|---|
| Executed | <date> |
| Gates after this item | lint ☐ static ☐ build ☐ test ☐ |
| Gate output | <pasted, or a pointer to where it is> |
| Anything unexpected | |

---

### C2 — <what is being removed>

<Same structure. One block per item.>

---

## 4. Items not removed

Things that looked removable and are not. Recording them stops the next session re-litigating it.

| Item | Why it stays | Evidence |
|------|-------------|----------|
| | | `path:line` |

## 5. Order of execution

Removals have dependencies too — remove the consumer before the thing it consumes.

| # | Item | Must follow | Why |
|---|------|-------------|-----|
| 1 | C<n> | — | |

## 6. Gates after the full cutover

```
$ <lint>
$ <static>
$ <build>
$ <test>
```

| Gate | Result |
|------|--------|
| lint | ✅ / ❌ |
| static | |
| build | |
| test | |

## 7. Documents updated

| Document | Change | `INDEX.md` updated |
|----------|--------|-------------------|
| | | ☐ |

## 8. Do-not-regress rows added

If a removed thing must not come back, fence it.

| Don't | Do instead | Added |
|-------|-----------|-------|
| | | <date> |

## 9. Sign-off

| # | Condition | Met |
|---|-----------|-----|
| 1 | Every item has an individual ticked approval | ☐ |
| 2 | Every item's pre-flight complete, consumers **read** | ☐ |
| 3 | Every item has a written reversal path | ☐ |
| 4 | Gates green after each item, and after all of them | ☐ |
| 5 | Documents and index updated | ☐ |
| 6 | Items not removed recorded with evidence | ☐ |

| | |
|---|---|
| Items executed | <n> of <n> |
| Items deferred | <n> — and why |
| Unexpected findings | |
