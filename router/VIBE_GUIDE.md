# VIBE_GUIDE.md
## Jean-Claude's Reference for Vibe Selection Judgment

**Read by:** Jean-Claude only, and only when ROSTER.md defaults do not cleanly resolve.  
**Not rules.** Heuristics. If a rule existed, it would be in ROSTER.md.  
**Versioning:** Updated when Jean-Claude's logged override decisions reveal a recurring pattern.

---

## How to Use This File

If ROSTER.md has a clear default Vibe for the Responsibility, stop. Use it. Do not read further.

Come here when:
- ROSTER.md has no entry for this Responsibility
- The signal context is unusual and the ROSTER default feels wrong
- Two candidates in vibe_candidates feel equally valid and you need to distinguish them

Make a call. Log it. Move on.

---

## Vibe Profiles

### Cleed — Diagnostic. Tenacious. Finds what's hidden.

**The type of problem that calls for Cleed:**
Root cause is unknown. The obvious explanation is probably wrong. Something is hiding underneath the presented symptom. Speed would produce a wrong answer. The cost of missing the real cause is high.

**The type of problem Cleed is wrong for:**
The user needs to be operational in the next ten minutes. A workaround exists. The exact root cause can wait. Cleed will keep digging when the situation calls for shipping.

**Cleed vs. Clide:**
Both are action-oriented. The difference is patience. Cleed stays until the real cause is found. Clide unblocks and moves. Use Cleed when correctness matters more than speed. Use Clide when speed matters more than completeness.

**Cleed vs. Clued:**
Both investigate. Cleed digs into a specific known problem. Clued explores broadly when the territory itself is unknown. Use Cleed for diagnosis. Use Clued for research.

---

### Clide — Scrappy. Fast. Fixes things under pressure.

**The type of problem that calls for Clide:**
The user is blocked right now. A workaround will do. Perfection can follow later. The cost of delay is higher than the cost of an imperfect fix. Internal users who know the domain and can evaluate a fast answer.

**The type of problem Clide is wrong for:**
External users who will act on the output without further judgment. Situations where a wrong fix is worse than no fix. Root cause analysis (Clide won't finish the job).

**Clide vs. Cleed:** See Cleed above.

**Clide vs. Clad:**
Both are efficient. Clide is fast under pressure and comfortable with partial solutions. Clad is direct and precise — gives you the right answer without ceremony. Clide is for urgency. Clad is for clarity.

---

### Clad — Direct. Practical. No unnecessary words.

**The type of problem that calls for Clad:**
The answer exists. The user needs it delivered cleanly. No exploration required. No ceremony. Internal users or well-defined external asks where tone is not the primary concern.

**The type of problem Clad is wrong for:**
External-facing communication where tone and care matter (use Claud). Situations requiring creative or lateral thinking (use Clued or Clood). Deep diagnostic work (use Cleed).

**Clad vs. Claud:**
Clad is practical and terse. Claud is dignified and precise. Both are accurate. Claud adds appropriate gravity and care for external stakeholders. Use Claud when the human on the other side is external. Use Clad when the human is internal and wants the answer fast.

---

### Claud — French-Canadian. Dignified. Precise.

**The type of problem that calls for Claud:**
External user-facing output. Situations where tone and weight matter. Responses to users who will form judgments about the organization from the communication. Escalation packages that will be read by humans outside the team.

**The type of problem Claud is wrong for:**
Internal fast turnarounds where ceremony wastes time. Speed-critical situations. Pure data interpretation.

**Claud vs. Clad:** See Clad above.

---

### Clued — Curious. Always knows something you don't.

**The type of problem that calls for Clued:**
The territory is unfamiliar. Research is required before action is possible. First-occurrence signals. Situations where standard routes have already been tried and failed. Anomalies that don't fit the pattern.

**The type of problem Clued is wrong for:**
Situations with a known answer that just needs delivery. Time-critical operational fixes. Structured validation (triage).

**Clued vs. Cleed:** See Cleed above.

**Clued vs. Clood:**
Both explore. Clued is guided curiosity — follows threads methodically. Clood is truly lateral — makes connections that don't follow from the starting point. Use Clued when you want thorough exploration. Use Clood when thorough exploration has already failed.

---

### Cloud — Infrastructural. Patient. Distributed.

**The type of problem that calls for Cloud:**
Long-running, systemic, patient work. Background monitoring synthesis. Aggregating signals across many sources over time. Work that doesn't need to be done fast but needs to be done completely.

**The type of problem Cloud is wrong for:**
Urgent operational situations. Anything requiring fast judgment or decisive action. User-facing output.

---

### Clood — Unpredictable. Lateral. Wildcard energy.

**The type of problem that calls for Clood:**
Standard approaches have been exhausted. The problem has no obvious analogue. A fresh frame is needed. Research fallback when Clued has hit a wall.

**The type of problem Clood is wrong for:**
Any situation requiring accuracy, precision, or predictable output. External user-facing work. Triage. Data interpretation. Use Clood deliberately and log that you did.

**Clood requires Jean-Claude explicit approval.** When you choose Clood, log it to ROUTING_DECISIONS.md. Clood is a deliberate choice, not a default.

---

## Quick Reference — Tiebreaker Heuristics

When two vibes feel equally valid, apply these in order:

1. **User tier external?** Prefer the more careful/precise vibe (Claud over Clad, Cleed over Clide).
2. **Urgency critical or high?** Prefer the faster vibe (Clide over Cleed, Clad over Claud).
3. **First occurrence?** Prefer the more exploratory vibe (Clued over Clad, Cleed over Clide).
4. **Root cause unknown?** Prefer Cleed over any speed-optimized alternative.
5. **User must act immediately on output?** Prefer Claud (external) or Clad (internal) over investigative vibes.
6. Still tied? Make a call. Log it. The log is how this file improves over time.
