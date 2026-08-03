<!--
TEMPLATE — manual test plan and record. Copy to <artefact_root>/<slug>/tasks/manual-testing.md, delete this
header. Written at step A12 / B11, and EXECUTED — not just written.

PURPOSE: cover what automation genuinely cannot. Not a duplicate of the test suite.

EVERY ROW MUST JUSTIFY WHY IT IS MANUAL. "Easier to check by hand" is not a justification — that is a test
someone did not write. Legitimate reasons: real device input, actual mail or notification delivery, generated
binary output, live third-party behaviour, rendered visual appearance, real payment flow, screen-reader
behaviour.

A WRITTEN-BUT-UNEXECUTED PLAN IS WORSE THAN NONE, because it reads like coverage. The Result column is filled
by actually doing it.

Sections are not deleted. Write None or Not applicable.
-->

# Manual Testing — <slug>

| | |
|---|---|
| Requirement | `../final_requirement.md` v<n> |
| Executed by | |
| Date | |
| Environment | <build, version, and how it was started> |
| **Result** | **<n> passed / <n> failed / <n> blocked** |

## 1. Why these are manual

| Reason | Cases |
|--------|-------|
| Real device or input hardware | |
| Actual delivery (mail, notification, message) | |
| Rendered visual appearance | |
| Generated binary output | |
| Live third-party behaviour | |
| Real payment or money movement | |
| Assistive-technology behaviour | |
| Environment-specific behaviour | |

**Anything not attributable to a row above should be automated instead.** Say so if it should and was not.

## 2. Environment

| | |
|---|---|
| How the app was started | `<command>` |
| Build type | development / production-like |
| Data state | <seeded, empty, realistic volume> |
| Actor / credentials used | <role, never real secrets> |
| Devices / clients | |
| Locales exercised | |

<If a local development mode is unusable for a known reason, say what was used instead and note the extra layer
that puts between the test and reality.>

## 3. Test cases

### M1 — <what is being checked>

| | |
|---|---|
| **Requirement** | R<n> criterion <n>.<m> |
| **Why manual** | <a reason from §1> |
| **Actor** | |
| **Precondition** | |

**Steps**

1. <step>
2. <step>

**Expected**

<Observable. "Works correctly" is not an expectation.>

**Result**

| | |
|---|---|
| Outcome | ✅ pass / ❌ fail / ⏸ blocked |
| Actual | |
| Evidence | <screenshot path, recording, pasted output> |
| Defect raised | <hotfix slug, or none> |

---

### M2 — <what is being checked>

<Same structure. One block per case.>

---

## 4. Cross-cutting sweeps

Run once across the whole feature rather than per case.

### Responsive and layout

Only if there is UI.

| Width / device | Horizontal overflow | Interactive targets | Overlays stay on screen | Result |
|----------------|--------------------|--------------------|------------------------|--------|
| narrowest supported | ☐ none | ☐ ≥ minimum | ☐ | |
| tablet | | | | |
| desktop | | | | |

Overlays near a screen edge are the specific case worth checking: a dropdown positioned only by an offset and a
fixed width overflows at narrow widths and passes every automated test.

### Accessibility

| # | Check | Result |
|---|-------|--------|
| 1 | Keyboard-only: every control reachable and operable | |
| 2 | Focus indicator visible on every interactive element | |
| 3 | Focus order matches visual order | |
| 4 | Labels present and announced | |
| 5 | Error messages associated with their fields | |
| 6 | Contrast meets the project's stated standard | |
| 7 | Reduced-motion preference respected | |

Full conformance needs testing with real assistive technology and expert review. This is a floor check.

### Locale parity

| Locale | Every new string present | Layout holds | Result |
|--------|------------------------|--------------|--------|
| | ☐ | ☐ | |

### States

| Surface | Loading | Empty | Populated | Error | Unauthorised |
|---------|---------|-------|-----------|-------|--------------|
| | | | | | |

The empty and error states are the ones that ship broken, because the happy path is what gets demonstrated.

### Regression spot-check

**Track B.** A sample of `do-not-break.md` rows, checked by hand.

| Register row | Contract | Result |
|--------------|----------|--------|
| | | |

## 5. Failures found

| # | Case | Symptom | Severity | Action |
|---|------|---------|----------|--------|
| 1 | M<n> | | 🔴 / 🟡 / 🟢 | fixed now / hotfix `<slug>` raised / accepted |

**A 🔴 failure blocks close-out.** It becomes a Track C fix, not a residual risk.

## 6. Not tested

Honest list. Anything skipped, and why.

| What | Why not | Risk |
|------|---------|------|
| | | |

## 7. Sign-off

| # | Condition | Met |
|---|-----------|-----|
| 1 | Every case executed, with a real result | ☐ |
| 2 | Every manual case justified as genuinely manual | ☐ |
| 3 | Cross-cutting sweeps run | ☐ |
| 4 | 🔴 failures resolved | ☐ |
| 5 | Untested items recorded with their risk | ☐ |

| | |
|---|---|
| Verdict | ready to close / blocked |
| Blocking failures | |
