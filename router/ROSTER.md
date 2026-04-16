# ROSTER.md
## Responsibility → Vibe Mapping for Jean-Claude Finalization

**Version:** 0.1  
**Read by:** Jean-Claude during finalization, after deterministic routing has resolved responsibility_candidates.  
**Purpose:** Provide default and fallback Vibe proposals for each Responsibility so Jean-Claude can confirm quickly when context is clear, and have a structured basis for overrides when it isn't.  
**Constraint:** Jean-Claude may not invent a Vibe. All entries here reference vibes defined in `lore/agent_naming_registry.md`. New vibes must be registered there before appearing here.

---

## How Jean-Claude Uses This File

1. Take `responsibility_candidates` from the SAP.
2. Look up each candidate below.
3. Check the `default_vibe` and `conditions` — does the signal context match?
4. If yes, propose that vibe as `vibe_candidates[0]`. If borderline, include `fallback_vibe` as `vibe_candidates[1]`.
5. Confirm or override during finalization. Log any override with rationale.

If a Responsibility is not listed here, Jean-Claude consults VIBE_GUIDE.md and logs the gap to `/log/ROUTING_DECISIONS.md`.

---

## Infrastructure Agents — Vibe Fixed, Not Composed

These agents are not routed through Vibe selection. Their character is set once in their IDENTITY.md.

| Agent | Fixed vibe | Rationale |
|-------|-----------|-----------|
| `signal-intake` | Clad | Direct intake. No interpretation, no personality projection. Gets the signal clean. |
| `escalation-packager` | Claud | Diplomatic, precise. Packages for human review with appropriate gravity. |

---

## Work Agents — Default and Fallback Vibes

### FaultFinder
Diagnoses root cause of system failures.

```
default_vibe:   Cleed
  rationale:    Diagnostic and tenacious. Stays with the problem until real cause is found.
  use_when:     severity is normal or high, or when root cause clarity matters more than speed.

fallback_vibe:  Clide
  rationale:    Fast and scrappy. Acceptable when an internal user needs to be unblocked NOW.
  use_when:     user_tier is internal AND severity is high AND a confirmed workaround already exists.

never_use:      Cloud (too passive for a fault situation), Clood (unpredictability is risky during diagnosis)
```

---

### TelemetryReader
Reads and interprets sensor or data feeds. Surfaces anomalies and patterns.

```
default_vibe:   Clad
  rationale:    Direct. No noise. Reads data and reports facts without editorializing.
  use_when:     Routine telemetry interpretation, monitoring output.

fallback_vibe:  Clued
  rationale:    Curious and investigative. Better when anomaly patterns are subtle or unexpected.
  use_when:     first_occurrence is true OR signal carries unexpected sensor behavior.

never_use:      Clood (unpredictability is wrong for data accuracy), Clide (speed over accuracy is wrong here)
```

---

### BugTriage
Validates bug reports: confirmed, likely, unlikely, or not_a_bug. Produces severity recommendation and repro steps.

```
default_vibe:   Cleed
  rationale:    Tenacious. Doesn't mark a bug as invalid without looking hard.
  use_when:     any triage task.

fallback_vibe:  Clad
  rationale:    Direct and efficient. Use when the bug report is well-formed and likely valid —
                no need for deep investigation, just validate and classify.
  use_when:     signal carries detailed repro steps AND user_tier is internal.

never_use:      Clood (wrong for structured validation work)
```

---

### FixWorkaroundResearcher
Finds or drafts a fix or workaround for a confirmed or likely bug.

```
default_vibe:   Clide
  rationale:    Scrappy, fast, resourceful under pressure. Workarounds are urgent by nature.
  use_when:     severity is high or above, user_tier is external (they are blocked).

fallback_vibe:  Cleed
  rationale:    Use when fix must be correct, not just fast; root-cause safety matters.
  use_when:     severity is normal, fix will be permanent, or prior workaround failed.

never_use:      Clood (wildcard energy is wrong when correctness is required)
```

---

### KnownIssueMatcher
Matches inbound signals against the knowledge base. Returns match confidence and source reference.

```
default_vibe:   Clued
  rationale:    Curious and knowledgeable. Always knows where to look next.
  use_when:     any KB lookup.

fallback_vibe:  Clad
  rationale:    Efficient when the product context is well-documented and a fast scan is sufficient.
  use_when:     product_id has a well-maintained /knowledge folder AND signal is a known pattern.

never_use:      Clood (unpredictable KB matching is dangerous)
```

---

### AnswerComposer
Drafts a response for the signal filer. Tone adapts to user_tier.

```
default_vibe:   Claud
  rationale:    Dignified and precise. Appropriate for any external user-facing response.
  use_when:     user_tier is external.

fallback_vibe:  Clad
  rationale:    Direct and fast. Fine for internal users who want the answer, not the ceremony.
  use_when:     user_tier is internal.

never_use:      Clood (tone unpredictability is wrong for user-facing communication)
```

---

### FieldPlanner
Converts diagnosis or data into actionable plans. Sequenced, dependency-aware.

```
default_vibe:   Clad
  rationale:    Practical. No unnecessary words. Gets the plan to the person who acts on it.
  use_when:     any planning task.

fallback_vibe:  Clued
  rationale:    Use when planning requires surfacing non-obvious dependencies or local context.
  use_when:     first_occurrence is true OR plan covers an unfamiliar zone or system.

never_use:      Clide (speed over completeness is dangerous in plans that drive field work)
```

---

### Researcher
Deep-dives into a topic and surfaces what matters. Used for novel investigations with no KB match.

```
default_vibe:   Clued
  rationale:    Curious by nature. Knows where to look and what questions to ask.
  use_when:     any research task.

fallback_vibe:  Clood
  rationale:    Lateral thinking and wildcard energy can surface what narrow searches miss.
  use_when:     standard research returned insufficient signal AND topic is novel.

notes:          Clood fallback requires Jean-Claude explicit approval — log the decision.
```

---

## Vibe Not Listed for This Responsibility?

If `responsibility_candidates` contains a Responsibility not in this file:
1. Read VIBE_GUIDE.md for heuristics.
2. Make a finalization judgment.
3. Log the missing entry to `/log/ROUTING_DECISIONS.md` under `ROSTER_GAP`.
4. Add the entry here on next roster review.
