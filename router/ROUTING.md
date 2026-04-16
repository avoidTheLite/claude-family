# ROUTING.md
## Deterministic Routing Table — Jean-Claude Orchestration System

**Version:** 0.1  
**Format:** `signal_type × conditions → responsibility_candidates + match_quality`  
**Resolver:** `resolver.runtime.json` (single authoritative pipeline)  
**Owner:** This file. Encoding human judgment as rules so Jean-Claude doesn't have to re-derive it.  
**Joan-clode:** Reads the deterministic output and adds advisory confidence. Does not modify this table.  
**Jean-Claude:** Reads candidates and match_quality. Finalizes assignment. Overrides require logged rationale.

---

## How to Read This Table

This table is consumed only by the router single-resolver pipeline. Agent folders must not implement local routing variants.

Each row is a routing rule. Rules execute top-to-bottom within each signal_type block.  
The first matching row wins. If no row matches, match_quality is `none` and Jean-Claude finalizes.

**match_quality values:**
- `single` — one responsibility maps cleanly; Jean-Claude confirms and proceeds
- `multiple` — two or more responsibilities are equally valid; Jean-Claude selects
- `none` — no rule matches; Jean-Claude resolves from first principles and logs to FIRST_OCCURRENCES.md

**Condition columns are additive.** All listed conditions must be true for the row to match.

---

## signal_type: question

A user or system is asking for information, clarification, or guidance. No action required beyond answering.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| question | any | any | false | KnownIssueMatcher | single | Known questions route directly to KB lookup. |
| question | any | any | true | KnownIssueMatcher, Researcher | multiple | First-occurrence questions may need research if KB has no match. |

---

## signal_type: bug

A defect or unexpected system behavior has been reported or detected.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| bug | critical | any | any | — | none | Critical bugs bypass this table entirely. Escalation-packager dispatched directly. Jean-Claude notified, not blocking. |
| bug | high | internal | any | FaultFinder, FixWorkaroundResearcher | multiple | Internal high-severity: diagnosis AND workaround needed in parallel. |
| bug | high | external | false | FaultFinder | single | External high-severity: diagnose first, workaround follows. |
| bug | high | external | true | FaultFinder, BugTriage | multiple | First-occurrence external bug: diagnose + triage simultaneously. |
| bug | normal | any | false | BugTriage | single | Standard bug: triage validates before escalating to FaultFinder. |
| bug | normal | any | true | BugTriage, FaultFinder | multiple | First occurrence: triage and fault-find overlap to build the record. |
| bug | low | any | any | BugTriage | single | Low severity: triage only. FaultFinder invoked if triage confirms. |

---

## signal_type: fix

A fix or workaround has been requested. Bug is known; solution is what's needed.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| fix | critical | any | any | — | none | Critical fixes bypass this table. Escalation-packager dispatched directly. |
| fix | high | internal | any | FixWorkaroundResearcher | single | Internal high: fast workaround. Speed over depth. |
| fix | high | external | any | FixWorkaroundResearcher, AnswerComposer | multiple | External high: fix + user-facing response needed. |
| fix | normal | any | false | FixWorkaroundResearcher | single | Standard fix request. |
| fix | normal | any | true | FixWorkaroundResearcher, FaultFinder | multiple | First-occurrence fix: may still need root cause before fix is safe. |
| fix | low | any | any | FixWorkaroundResearcher | single | Low-priority fix. |

---

## signal_type: diagnose

An operator or monitoring system suspects a problem and wants root-cause analysis. Signal is clear; cause is not.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| diagnose | critical | any | any | — | none | Critical: escalation-packager dispatched directly. |
| diagnose | high | any | any | FaultFinder | single | Clear diagnostic request, high urgency. |
| diagnose | normal | any | false | FaultFinder | single | Standard diagnostic. |
| diagnose | normal | any | true | FaultFinder, TelemetryReader | multiple | First-occurrence alarm: may need telemetry context before diagnosing. |
| diagnose | low | any | any | TelemetryReader | single | Low urgency: read and interpret data first, escalate to FaultFinder if needed. |

---

## signal_type: plan

Someone wants a field plan, work sequence, or action list derived from existing data or prior diagnosis.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| plan | critical | any | any | — | none | Critical: escalation-packager handles. |
| plan | high | any | any | FieldPlanner | single | Urgent plan request. |
| plan | normal | any | any | FieldPlanner | single | Standard planning task. |
| plan | low | any | any | FieldPlanner | single | Low-urgency planning. |

---

## signal_type: unknown

Signal could not be classified. signal-intake could not determine intent. Joan-clode confidence will be ambiguous or unknown.

| signal_type | severity | user_tier | first_occurrence | responsibility_candidates | match_quality | Notes |
|-------------|----------|-----------|-----------------|--------------------------|---------------|-------|
| unknown | critical | any | any | — | none | Critical: escalation-packager dispatched directly. |
| unknown | any | any | any | — | none | No deterministic match possible. Jean-Claude resolves from scratch. Logs to FIRST_OCCURRENCES.md. |

---

## Escalation Path (out of band — not routing)

Severity `critical` is not routed through this table. It exits the routing flow immediately:

```
severity: critical
  → escalation-packager dispatched directly
  → Jean-Claude notified but not blocking
  → routing_finalization.finalized is false in ARE (exempted from finalization requirement)
```

---

## Graduation Path

When Jean-Claude repeatedly resolves the same `none` match with the same result, that pattern should graduate here.

1. Jean-Claude logs judgment to `/log/ROUTING_DECISIONS.md`
2. Human reviews the log periodically
3. Confirmed patterns become new rows in this table
4. New rows reduce Jean-Claude's finalization load over time
