<!--
TEMPLATE — copy to product.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: ALWAYS.

Purpose: so the agent knows what it is building and for whom, and uses the project's own vocabulary
instead of inventing synonyms. This is the file that stops a "customer" becoming a "user" becoming a
"client" across three artefacts.

Evidence rule: every fact here comes from the codebase, the project's README, or the user. Nothing is
inferred from the project name. Inventing product facts is F5 and it propagates into every requirement
written afterwards.

Keep it under ~120 lines. Detail belongs in project documentation, not in an ALWAYS-loaded rule.
-->

# Product — <name>

**<one-line description: what it does, for whom>**

| | |
|---|---|
| Stage | <prototype / pre-launch / live / maintenance> |
| Primary users | <who> |
| Scale of concern | <what volume/latency actually matters, or "not yet a constraint"> |
| Source of these facts | <README / user / code — cite paths> |

## What it does

<Two to four sentences. The capability, not the implementation. Someone who has never seen the code should
be able to say what the product is for after reading this.>

## Users and actors

Every actor the system distinguishes. This list drives authorisation review, so an actor missing here is an
authorisation gap nobody checks.

| Actor | Who they are | What they can do | Where they enter |
|-------|--------------|------------------|------------------|
| | | | |

## Core capabilities

The things the product does, as the product's own users would name them.

| Capability | Status | Primary surface |
|------------|--------|-----------------|
| | live / partial / planned | |

## Primary user journeys

Numbered, because requirements reference them. Each is the happy path in one line.

1. <journey>
2. <journey>

## Domain vocabulary

**The most valuable section in this file.** One term per row, with the name the code actually uses. An agent
that calls the same thing three names produces artefacts that cannot be cross-checked.

| Term | Means | Code name / type | Do not call it |
|------|-------|------------------|----------------|
| | | `path:line` | |

## Constants — import, never hardcode

Everything below lives in the project's constants module (`config.yml` → `paths.constants_module`) and is
imported. Hardcoding any of it into feature code is a defect.

| Constant | Holds | Where |
|----------|-------|-------|
| | | `path:line` |

<If the project has no single constants module, say so here and state that establishing one is
documentation/technical debt rather than pretending a convention exists.>

## Locales and regions

| | |
|---|---|
| Supported locales | <list, or "single locale"> |
| Default | |
| Parity requirement | <which surfaces must have all locales, and where the catalogues live> |
| Regional constraints | <currency, date format, legal, data residency — or none> |

## Boundaries — what this product is not

Stating the non-goals is what keeps requirements from drifting into them.

- <non-goal>
- <non-goal>

## Compliance and sensitivity

| Concern | Applies? | Constraint it imposes |
|---------|----------|----------------------|
| Personal data | | |
| Payments | | |
| Health / financial / legal regulation | | |
| Accessibility standard | | |
| Data residency | | |
| Audit / retention | | |

<A "no" here is a real answer and worth recording. An empty row means nobody has checked.>

## Where to read more

| Topic | Document |
|-------|----------|
| | |
