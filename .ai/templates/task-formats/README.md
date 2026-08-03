# Task Formats

The requirement asked for standardised formats for executing different **types** of task. This is that set.

A task's discipline changes what can go wrong with it. A UI task fails on states, styling load and
accessibility. A database task fails on migration ordering and records that predate the change. An
integration task fails on retries and webhook verification. One generic checklist cannot catch all of them,
and a checklist that tries becomes long enough to be skipped.

So each format adds **discipline-specific checks** on top of the common task blocks in
[`../task-details.md`](../task-details.md). They do not replace it.

---

## Picking a format

| Format | Use when the task touches |
|--------|--------------------------|
| [`ui-task.md`](ui-task.md) | screens, components, styling, layout, accessibility, client-side interaction |
| [`backend-api-task.md`](backend-api-task.md) | endpoints, handlers, resolvers, commands, service logic, authorisation |
| [`database-task.md`](database-task.md) | schema, migrations, indexes, backfills, queries, data integrity |
| [`infra-devops-task.md`](infra-devops-task.md) | CI, pipelines, infrastructure-as-code, environments, releases, monitoring |
| [`data-ml-task.md`](data-ml-task.md) | pipelines, transformations, feature engineering, model training, evaluation |
| [`integration-task.md`](integration-task.md) | third-party APIs, webhooks, message queues, scheduled jobs, file transfer |

**A task spanning two disciplines** uses both formats' discipline-specific blocks. That is usually a signal
the task should be split — one task, one discipline, one reviewable unit.

**No format fits?** Use the common blocks alone, and say in the task that no discipline format applied. Then
consider whether a new format is worth adding — see the bottom of this file.

## What every format shares

Present in all six, because they close failure modes that are not discipline-specific.

| Block | Why |
|-------|-----|
| Requirement and design reference | traceability both ways; untraceable work is scope creep (**F2**) |
| Files touched, with read-before-modify | modifying a file you have not read is **F5** |
| **Ripple effects** | what else this breaks — catches cascading breakage before it happens (**F1**) |
| Reversibility class | routes irreversible work to cutover instead of letting it ride inline (**F6**) |
| Test plan tied to acceptance criteria | a test that asserts nothing in the requirement proves nothing about it |
| Gate evidence | real output, never a claim |
| Done-when list | removes the freedom to redefine done (**F9**) |
| Follow-ups | they block section closure |

## What the formats deliberately do not do

- **They do not name tools or libraries.** A UI task format that assumes a specific framework stops being
  portable. Concrete choices live in the project rule layer, generated at activation.
- **They do not restate the universal rules.** Grounding, gates, and reversibility apply everywhere; repeating
  them here would create the duplication that becomes **F11**.
- **They are not a substitute for the design.** A checklist verifies; it does not decide. If a check has no
  answer, that is a design gap, not a checklist gap.

## Using one

1. Plan the task in [`../task-details.md`](../task-details.md) as normal.
2. Copy this format's **discipline checklist** into that task's checklist block.
3. Fill every row with a real result. A row you cannot answer is a finding — either the design is incomplete
   or the task is not ready.
4. `Not applicable` is a valid result **with a reason**. Bare `n/a` on a row that clearly applies is how a
   check gets skipped while looking complete.

## Adding a format

Worth it when a discipline has failure modes the existing six do not cover — mobile-native, embedded,
game engine, blockchain, hardware-in-the-loop.

To add one:

1. List the ways tasks in that discipline **actually fail**. Not theory: things that have gone wrong.
2. Turn each into a check with an observable pass condition.
3. Keep the tool-specific parts out; they belong in the project rule layer.
4. Add the verification section — how a task in this discipline is *proved* done, which is usually the part
   that differs most between disciplines.
5. Add a row to the table above and to [`../../INDEX.md`](../../INDEX.md).
