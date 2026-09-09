## Harness Diagnostic Diff · Juno Harness Lab

_Working notes from Module 3 Lab 1. Do not paste over `03-harness-prd/prd.md`. That file comes from the AI PRD Builder._

**Prototype:** https://claude.ai/public/artifacts/9ea28a67-8c25-48b3-88f1-73e526b765a3

### Harness off

Dark Mode:
- Unverified, no source.
- No recommended, aesthetic / optics request with no user problem or strategic backing.
- Blocked, Out of scope.

CSV export crashes:
- P1
- Unverified, no source.
- Blocked, write_roadmap rejects unverified insights

### Harness on

Dark Mode:
- Verified
- No recommended, aesthetic / optics request with no user problem or strategic backing.
- Blocked, Out of scope.
- Show supporting clause · Data & Model Approach

CSV export crashes:
- P1
- Unverified, no source.
- No supporting clause found in the loaded strategy document.
- Blocked, write_roadmap rejects unverified insights

PRD: 
- It's identical, with one diference, without the harness it shows the words "quality mode"

### Tool trace

READ
read_tickets
AUTO
166ms
{"filter":"open"}
2 tickets from fixture

READ
search_strategy
AUTO
158ms
{"query":"csv export crash reliability report excel"}
0 clauses — no support

DRAFT
draft_priority
AUTO
102ms
{"insight_id":"I-1","priority":"P1"}
drafted P1 for I-1 (unverified)

READ
search_strategy
AUTO
166ms
{"query":"dark mode colour color aesthetic theme appearance"}
1 clause(s) · top: “Data & Model Approach”
DRAFT
draft_priority
AUTO
111ms
{"insight_id":"I-2","priority":"not-recommended"}
drafted not-recommended for I-2

### Takeaway

> The harness makes the agent's work traceable and governable, one can see every step it takes and gate the ones that matter. By forcing each priority to cite the strategy document, it keeps the agent grounded in evidence instead of its own memory.
