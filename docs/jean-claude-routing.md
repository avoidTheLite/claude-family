# Version 1 — Jean-Claude Routing Model
## Agent-Navigable Product Repository with Judgment-Based Orchestration

**Runner compatibility:** Claude, Claude Code, Copilot, Cursor, any LLM-capable agent  
**Core pattern:** Rules-based routing with named judgment fallback  
**Human role:** Reviews gate-flagged decisions and maintains knowledge files  
**Orchestration model:** Environment routes the happy path. Jean-Claude handles everything else.

---

## Design Principles

The routing logic lives in files, not in an agent. Any runner dropped into this
repository can orient from AGENTS.md and execute the happy path without Jean-Claude
being instantiated. Jean-Claude exists for the cases the routing table cannot resolve.

Each stage has a contract. The contract defines what comes in and what must come out.
An agent that cannot satisfy the output contract must escalate rather than guess.

The human's judgment has been encoded into the routing table. What remains for
human review is what the table explicitly flags — not random checkpoints.

---

## Folder Structure

```
/repo-root
│
├── AGENTS.md                          ← Universal entry point. Read this first.
│                                         Describes the product domain, what this
│                                         repository does, how to navigate it, and
│                                         how to behave when a file is missing.
│                                         The barrel. Every repo needs exactly one.
│
├── /protocols
│   ├── SAP.md                         ← Signal Assessment Payload
│   │                                     Required fields:
│   │                                       product_id
│   │                                       user_tier        (internal | external)
│   │                                       signal_type      (question | bug | fix | unknown)
│   │                                       severity         (critical | high | normal | low)
│   │                                       confidence       (clear | ambiguous | unknown)
│   │                                       first_occurrence (true | false)
│   │                                       raw_signal       (original text, unmodified)
│   │
│   ├── ARE.md                         ← Agent Response Envelope
│   │                                     Required fields:
│   │                                       target_agent
│   │                                       sap_reference
│   │                                       resolution_shape (answer | bug | fix | escalation)
│   │                                       authorization_level  ← per step, not per envelope
│   │                                       user_tier            ← carried from SAP
│   │                                       judgment_call    (true | false)
│   │
│   └── ESCALATION.md                  ← Escalation paths by severity and user tier.
│                                         Defines who receives what, under which conditions.
│                                         Not a routing rule — a destination registry.
│
├── /router
│   │
│   ├── AGENTS.md → (root AGENTS.md)   ← Agents orient at root, not here.
│   │                                     This folder is Jean-Claude's house.
│   │
│   ├── IDENTITY.md                    ← Jean-Claude.
│   │                                     Who he is. What his mandate covers.
│   │                                     Describes judgment conditions, not routing rules.
│   │                                     Routing rules live in ROUTING.md.
│   │                                     His job starts where ROUTING.md ends.
│   │
│   ├── ROUTING.md                     ← The rules-based decision table.
│   │                                     Two-axis: product_id × signal_type.
│   │                                     Gate conditions encoded here — not in Jean-Claude.
│   │                                     Specifies when to route, when to gate,
│   │                                     when to escalate, when to invoke Jean-Claude.
│   │
│   └── ROSTER.md                      ← Agent registry.
│                                         Name, vibe, responsibility, one-liner capability.
│                                         Product affinities noted.
│                                         Jean-Claude reads this to know who he can call.
│
├── /agents
│   │
│   ├── /signal-intake                 ← Normalizes raw inbound to SAP.
│   │   ├── IDENTITY.md                   Handles any source: form, email, API, chat.
│   │   └── CONTRACT.md                   Input:  raw signal + source metadata
│   │                                      Output: valid SAP
│   │                                      Owns the confidence field assessment.
│   │
│   ├── /known-issue-matcher           ← Matches SAP against knowledge base.
│   │   ├── IDENTITY.md                   Reads /knowledge/{product_id}/ first.
│   │   └── CONTRACT.md                   Falls back to /_shared if no match.
│   │                                      Input:  SAP
│   │                                      Output: match result + confidence + source ref
│   │                                      No match → flags for triage or escalation.
│   │
│   ├── /answer-composer               ← Drafts response for the signal filer.
│   │   ├── IDENTITY.md                   Tone adapts to user_tier from SAP.
│   │   └── CONTRACT.md                   Input:  SAP + knowledge match
│   │                                      Output: draft response, staged for gate review
│   │
│   ├── /bug-triage                    ← Determines bug validity, severity, repro path.
│   │   ├── IDENTITY.md                   Reads product context for blast radius assessment.
│   │   └── CONTRACT.md                   Input:  SAP
│   │                                      Output: triage report
│   │                                        confirmed | likely | unlikely | not_a_bug
│   │                                        severity recommendation
│   │                                        repro steps if derivable
│   │
│   ├── /fix-workaround-researcher     ← Finds or drafts a fix or workaround.
│   │   ├── IDENTITY.md                   Only invoked post-triage when bug confirmed
│   │   └── CONTRACT.md                   or when internal user needs unblocked fast.
│   │                                      Input:  SAP + triage report
│   │                                      Output: workaround draft or fix recommendation
│   │
│   └── /escalation-packager           ← Packages signal for human engineer handoff.
│       ├── IDENTITY.md                   Knows escalation paths from ESCALATION.md.
│       └── CONTRACT.md                   Adapts package format by user_tier and product.
│                                          Input:  SAP + any prior agent outputs
│                                          Output: escalation brief, ready to dispatch
│
├── /knowledge
│   │
│   ├── /_shared                       ← Cross-product patterns. Auth errors. Rate limits.
│   │   ├── KNOWN_ISSUES.md               Common integration failure modes.
│   │   └── WORKAROUNDS.md
│   │
│   ├── /{product-id}                  ← One folder per product. Repeat as needed.
│   │   ├── KNOWN_ISSUES.md               Agents read. Humans maintain.
│   │   ├── WORKAROUNDS.md                Version-tagged entries.
│   │   └── PRODUCT_CONTEXT.md            What it does. Key concepts. Current version.
│   │                                      Known fragile areas. Recent changes.
│   └── README.md                      ← Describes the knowledge structure.
│                                         Tells agents: read product folder first,
│                                         fall back to _shared, never write here.
│
├── /stages
│   ├── /01-intake                     ← Raw signal lands here. Source metadata preserved.
│   ├── /02-normalization              ← SAP produced. product_id and user_tier set.
│   ├── /03-routing                    ← ROUTING.md executes. Jean-Claude if needed.
│   ├── /04-execution                  ← Named agent works. Prior outputs available here.
│   └── /05-resolution                 ← Final artifact staged before delivery.
│
└── /log
    ├── ROUTING_DECISIONS.md           ← Jean-Claude's dated judgment calls.
    │                                     Novel signal types accumulate here.
    │                                     Graduate to ROUTING.md once patterns emerge.
    └── FIRST_OCCURRENCES.md           ← New product × signal type combinations.
                                          Reviewed periodically to grow the routing table.
```

---

## How Routing Works

```
Inbound signal
  │
  ▼
/agents/signal-intake
  → Produces SAP with product_id, signal_type, confidence
  │
  ▼
/router/ROUTING.md  (rules execute here)
  │
  ├── confidence: clear + known product + known signal type
  │     → ARE built automatically
  │     → Target agent dispatched
  │     → Jean-Claude not involved
  │
  ├── confidence: ambiguous OR first_occurrence: true
  │     → Intent gate fires
  │     → Jean-Claude reviews, builds ARE manually
  │     → Judgment logged to /log/ROUTING_DECISIONS.md
  │
  └── severity: critical
        → Skip intent gate
        → /agents/escalation-packager dispatched directly
        → Jean-Claude notified, not blocking
```

---

## Gate Conditions (encoded in ROUTING.md, not in Jean-Claude)

**Intent gate fires when:**
- confidence is ambiguous or unknown
- first_occurrence is true
- Two agents score equally plausible for the same signal

**Envelope gate fires when:**
- Any step in the ARE requires authorization_level: elevated or above
- Fallback agent was selected over primary
- user_tier is external AND resolution_shape is unconfirmed

**Jean-Claude is invoked when:**
- Either gate cannot resolve without judgment
- Signal type has no routing table entry
- Product has no knowledge folder and no prior routing decision

---

## Fallback Guarantee

If this repository contains only AGENTS.md, any runner performs at baseline —
equivalent to asking the model a plain question with no context.

Each additional file improves performance monotonically.
No file, when missing, causes performance to drop below baseline.
