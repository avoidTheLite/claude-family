# Routing Plan — Vibe-Responsibility Architecture
## Synthesizing the Claude Family naming model with Jean-Claude routing

**Status:** Partially implemented in schema v0.1 (SAP/ARE fields + validation); runtime routing components still pending  
**Source documents:** README.md, docs/jean-claude-routing.md  
**Core problem:** The routing model resolves *what agent does the job* but does not separate *who the agent is* from *what they are doing*. These are two different decisions and must be resolved on two different axes, with explicit decision ownership at each step.

---

## The Gap

The Version 1 routing model (jean-claude-routing.md) maps inbound signals to agent folders:

```
signal_type: bug  →  /agents/bug-triage
```

The Claude Family naming model (README.md) requires agents to be composed from two axes:

```
{Vibe}-{Responsibility}
  │         └── what the agent does
  └── who the agent is
```

In Version 1, routing resolves only Responsibility. Vibe is never resolved — it's implicit, undefined, or absent. This means agent composition is incomplete. Jean-Claude is described as the router who *composes* agents, but the routing table gives him no mechanism to select a Vibe.

This plan adds Vibe resolution as an explicit second step in the routing flow, and promotes ambiguity to a first-class, multi-dimensional routing factor.

---

## Two-Axis Resolution

Every agent composition requires resolving both axes independently before the agent is instantiated.

| Axis | What it resolves | Source of truth | Automated? |
|------|----------------|-----------------|------------|
| **Responsibility** | What job needs doing | SAP `signal_type` × `product_id` → ROUTING.md | Yes, if single match |
| **Vibe** | Who should do it | Task character + context → ROSTER.md defaults | Yes, if default exists |

Neither axis is resolved from the other. They are independent. Jean-Claude composes `{Vibe}-{Responsibility}` only after both are resolved — not as a combined lookup.

---

## Decision Ownership Model

This plan uses a three-layer decision model with strict ownership boundaries:

1. **Deterministic Router (rules engine)**
Resolves Responsibility candidates from explicit rules only. No model judgment.

2. **Joan-clode (scoring layer)**
Provides confidence and short reasoning for the deterministic result. It does not select the final agent.

3. **Jean-Claude (finalizer)**
Finalizes assignment. He can accept the deterministic recommendation or override it, and must log why.

This preserves predictability while still allowing judgment when edge cases appear.

---

## Ambiguity as a First-Class Routing Factor

Ambiguity is currently a single field in the SAP (`confidence: clear | ambiguous | unknown`) that covers signal clarity. This is insufficient — there are three distinct kinds of ambiguity, each requiring different handling.

| Dimension | What is unclear | Where it surfaces |
|-----------|----------------|-------------------|
| **Signal ambiguity** | The user's intent is unclear | Joan-clode confidence + reasoning over SAP |
| **Responsibility ambiguity** | Multiple responsibilities match the signal equally | ROUTING.md — when two or more rows score identically |
| **Vibe ambiguity** | No clear character fit for the task's nature | ROSTER.md — when no default Vibe exists for the Responsibility |

**Jean-Claude finalizes all non-critical work-agent assignments, with special attention when any ambiguity dimension is non-clear.** He resolves only the unclear dimension(s) — he does not re-decide what deterministic routing and ROSTER defaults already resolved cleanly unless he explicitly overrides with rationale.

This is the core constraint: routing is deterministic first, Joan-clode scoring is advisory, and Jean-Claude is the final assignment authority.

---

## Updated Routing Flow

```
Inbound signal
  │
  ▼
/agents/signal-intake (fixed — infrastructure agent)
  → Produces SAP
  → Identifies: product_id, user_tier, signal_type, severity
  │
  ▼
ROUTING.md executes (deterministic)
  → Maps product_id × signal_type → Responsibility candidate(s)
  → Records ambiguity: single | multiple | none
  │
  ▼
Joan-clode executes (advisory only)
  → Adds llm_confidence: clear | ambiguous | unknown
  → Adds llm_reasoning: concise explanation for confidence and risks
  → Does not assign final agent
  │
  ▼
ROSTER.md default mapping
  → Proposes default vibe_candidates for resolved Responsibility
  │
  ▼
Jean-Claude finalization
  → Reviews deterministic output + llm_confidence + llm_reasoning
  → Finalizes {Vibe}-{Responsibility}
  → Logs accept/override rationale
  │
  ▼
ARE dispatch
  → target_agent: "{Vibe}-{Responsibility}"
  │
  ├── severity: critical ──────────────────────────────────────────────────────┐
  │     → /agents/escalation-packager dispatched directly                      │
  │     → Jean-Claude notified, not blocking                                    │
  │                                                                             │
  └── Any non-critical work assignment                                           │
        → Jean-Claude finalization required                                      │
        → Judgment logged to /log/ROUTING_DECISIONS.md                          │
                                                                                ▼
                                                                       [escalation path]
```

---

## What Changes in Each File

### SAP — additions

Add to required fields:

```
responsibility_candidates   (array)   ← Responsibilities returned by ROUTING.md
                                        Empty if no match. Multiple entries if ambiguous.

vibe_candidates             (array)   ← Vibes from ROSTER.md defaults
                                        May be empty. Jean-Claude populates if invoked.

llm_confidence              (enum)    ← clear | ambiguous | unknown
                                        Advisory score over deterministic routing result.

llm_reasoning               (string)  ← Short rationale for confidence and risk factors.
                                        Not chain-of-thought; concise decision note.
```

The original `confidence` field can be retained for backward compatibility, but `llm_confidence` is the routed confidence used by Jean-Claude finalization.

---

### ARE — one format constraint added

`target_agent` must be formatted as `{Vibe}-{Responsibility}` for composed work agents.  
Infrastructure agents (signal-intake, escalation-packager) are exempt — they use fixed names.

No other changes to ARE required.

---

### ROUTING.md — structure addition

Current: maps `product_id × signal_type → agent folder path`  
Updated: maps `product_id × signal_type → Responsibility name` via deterministic rules only

Add a column: `match_quality` (single | multiple | none)  
- `single` → clean automated routing  
- `multiple` → Jean-Claude invoked for Responsibility resolution  
- `none` → Jean-Claude invoked; may log as first_occurrence

ROUTING.md does not touch Vibe and does not perform probabilistic scoring. It resolves Responsibility candidates only.

---

### ROSTER.md — restructure

Current: name, vibe, responsibility, one-liner  
Updated: structured as a Responsibility → Vibe lookup table with Jean-Claude override notes

```
Responsibility: bug-triage
  default_vibe: Cleed          (diagnostic, tenacious, finds what's hidden)
  fallback_vibe: Clide         (scrappy, fast, fixes things under pressure)
  notes: use Clide when user_tier is internal AND severity is high or critical
         (speed matters more than thoroughness for internal unblocks)
```

ROSTER.md proposes Vibe defaults.  
Jean-Claude confirms or overrides those defaults during finalization.  
Jean-Claude never invents a Vibe — all Vibes must exist in the registry.

---

### New file: /router/VIBE_GUIDE.md

Jean-Claude's reference for Vibe selection judgment. Not rules — heuristics.

Structure: one entry per Vibe, describing:
- The type of problem that calls for this Vibe
- The type of problem this Vibe is wrong for
- How this Vibe differs from similar Vibes in practice

Example:

```
## Cleed vs. Clide
Both are action-oriented. The difference is patience.
Cleed stays with a problem until the real cause is found.
Clide unblocks fast and moves on.

Use Cleed when: root cause matters more than speed.
Use Clide when: the user needs to be operational now.
```

Jean-Claude reads VIBE_GUIDE.md only when Vibe resolution is ambiguous.  
He should not read it on every routing decision — that's waste.

---

## Infrastructure Agents vs. Work Agents

The Version 1 folder structure uses paths like `/agents/signal-intake`. That structure stays.  
The distinction is which agents get composed with a Vibe and which don't.

| Category | Examples | Naming | Vibe |
|----------|----------|--------|------|
| **Infrastructure agents** | signal-intake, escalation-packager | Fixed folder names | Baked into IDENTITY.md. Not composed per-task. |
| **Work agents** | bug-triage, answer-composer, known-issue-matcher, fix-workaround-researcher | `{Vibe}-{Responsibility}` | Composed by Jean-Claude per task. |

Infrastructure agents run once per signal and always do the same job. Their vibe can be set once and never revisited.  
Work agents are where character matters — the same responsibility with a different vibe produces a meaningfully different result. This is where Jean-Claude's composition judgment pays off.

---

## Jean-Claude's Complete Input Set

For Jean-Claude to do his job, he needs exactly these, nothing else:

1. **The SAP** — signal, product_id, user_tier, severity, responsibility_candidates, vibe_candidates
2. **Deterministic ROUTING.md output** — Responsibility candidate(s) and match_quality
3. **Joan-clode output** — `llm_confidence` and `llm_reasoning`
4. **ROSTER.md** — to select or confirm Vibe defaults
5. **VIBE_GUIDE.md** — heuristics for Vibe judgment when ROSTER defaults don't resolve
6. **/log/ROUTING_DECISIONS.md** — prior judgment on similar or identical signals
7. **ARE template** — to build the dispatch envelope

If Jean-Claude reaches for something outside this set, the knowledge structure is incomplete and that gap should be closed in the appropriate file — not by expanding Jean-Claude's context.

---

## Jean-Claude Finalization Conditions (consolidated)

Jean-Claude finalizes all non-critical work-agent assignments, and must explicitly review when any of the following apply:

| Condition | Ambiguity dimension | Jean-Claude resolves |
|-----------|---------------------|----------------------|
| `llm_confidence` is ambiguous or unknown | Signal | Responsibility + optionally Vibe |
| ROUTING.md returns multiple equal matches | Responsibility | Responsibility only |
| ROSTER.md has no default Vibe for resolved Responsibility | Vibe | Vibe only |
| `first_occurrence: true` | Signal or Responsibility | Logs to FIRST_OCCURRENCES.md, may resolve both |
| `severity: critical` | — | Notified, not blocking; escalation-packager runs |
| Any ARE step requires `authorization_level: elevated` | — | Reviews and authorizes |
| Two agents score equally plausible for the same signal | Responsibility | Responsibility only |

**Jean-Claude is the final assignment authority for non-critical work.** He resolves only unclear dimensions by default, and if he overrides a clear deterministic route he must log rationale.

---

## What Stays the Same

- The five-stage pipeline (/stages/01 through /05) is unchanged
- The SAP schema structure is unchanged — fields are added, not replaced
- The ARE schema structure is unchanged — only `target_agent` format is constrained
- The folder structure for agents and knowledge is unchanged
- Escalation path for severity: critical is unchanged
- AGENTS.md as the universal entry point is unchanged
- Jean-Claude's singleton status is unchanged
- The /log structure and graduation path (ROUTING_DECISIONS.md → ROUTING.md) is unchanged

---

## Implementation Sequence

1. Done: SAP schema includes `responsibility_candidates`, `vibe_candidates`, `llm_confidence`, and `llm_reasoning`.
2. Done: ROUTING.md created with deterministic signal_type × conditions → Responsibility table and match_quality.
3. Done: Implemented Joan-clode runtime scoring contract in `router/joan-clode.runtime.json` (advisory confidence + concise reasoning, no dispatch authority).
4. Done: ROSTER.md created with Responsibility → default/fallback Vibe mapping.
5. Done: VIBE_GUIDE.md created with per-vibe heuristics and tiebreaker rules for Jean-Claude finalization.
6. Done: Added `router/IDENTITY.md` defining Jean-Claude finalization behavior for non-critical dispatches.
7. Done: ARE validation blocks non-critical dispatch without a Jean-Claude finalization mark.
8. Done: Created `log/ROUTING_DECISIONS.md` with decision template, decision types, and follow-up action taxonomy.

---

## Open Questions

1. **Should infrastructure agents have vibes?** The signal-intake agent always runs. Giving it a Vibe (e.g., `Clad-SignalIntake`: direct, no unnecessary words) would be consistent with the framework even if it's never composed per-task.

2. **ROSTER.md as a file or a section of IDENTITY.md?** Currently ROSTER.md lives in /router. Alternatively, each agent IDENTITY.md could declare its own preferred Vibes, and Jean-Claude aggregates from there. The single-file approach is simpler for now.

3. **Vibe graduation path**: VIBE_GUIDE.md will need versioning as patterns emerge. Jean-Claude's logged judgment calls should be reviewed periodically to refine the heuristics — the same graduation pattern used in ROUTING_DECISIONS.md → ROUTING.md.

4. **Multi-agent composition**: Some tasks may require two work agents in sequence. Should Jean-Claude compose a Vibe per step, or hold the same Vibe across the chain? Likely per step, since the character needed for diagnosis differs from the character needed for response drafting.
