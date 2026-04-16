# ROUTING_DECISIONS.md
## Jean-Claude Decision Log

This log records finalization decisions so recurring patterns can graduate into deterministic rules.
It is the required output sink for the `logging` stage in `router/resolver.runtime.json`.

---

## Entry Template

Copy this block for each finalization decision.

```yaml
decision_id: RD-YYYYMMDD-XXXX
timestamp: 2026-04-16T00:00:00Z
sap_id: SAP-YYYYMMDD-XXXX
severity: normal
user_tier: internal
signal_type: bug
first_occurrence: false

routing_result:
  responsibility_candidates: [BugTriage, FaultFinder]
  match_quality: multiple

joan_clode:
  llm_confidence: ambiguous
  llm_reasoning: "Short advisory rationale."

final_assignment:
  responsibility: BugTriage
  vibe: Cleed
  target_agent: Cleed-BugTriage

decision:
  accepted_deterministic: false
  override_type: responsibility
  rationale: "Why this override is safer or more appropriate."

follow_up:
  action: route_update
  file: router/ROUTING.md
  note: "Add deterministic rule for internal normal bug with first_occurrence=false."
```

---

## Decision Types

- `ACCEPTED`: deterministic candidate and roster default accepted
- `OVERRIDE_RESPONSIBILITY`: deterministic responsibility changed
- `OVERRIDE_VIBE`: responsibility accepted, vibe changed
- `OVERRIDE_BOTH`: both responsibility and vibe changed
- `ROSTER_GAP`: responsibility had no roster entry
- `CONTEXT_GAP`: required input context was missing

---

## Follow-up Actions

- `route_update`: change belongs in ROUTING.md
- `roster_update`: change belongs in ROSTER.md
- `vibe_guide_update`: change belongs in VIBE_GUIDE.md
- `none`: no pattern change needed

---

## Entries

_No entries yet._
