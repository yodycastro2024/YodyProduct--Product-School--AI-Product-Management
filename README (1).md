# Juno PM

> _Juno is an AI Associate PM at RocketShip that turns raw customer signal — Slack #escalations and #voice-of-customer, Zendesk P0/P1 tickets, and interview transcripts — into an evidence-ranked backlog and draft PRDs, citing a source for every priority._

_Yody Castro · cohort · 10/10/26_

This repo is my final project for the **AI Product Management Certification**. Each module's artefact lives in its own folder; this README is the dashboard and the pitch.

**How to use this template:** click **Use this template → Create a new repository**, name it `juno-pm`, and commit one module's artefact per session. Assemble this dashboard with the **Final Project Deliverables Builder** (paste its `README.md` output over this file).

---

## Module artefacts

### M1 · Prompting
- **System prompt** — [`01-prompting/system-prompt.md`](01-prompting/system-prompt.md)
- **Prototype debrief** — [`01-prompting/prototype.md`](01-prompting/prototype.md)
- **Claude prototype** — https://claude.ai/public/artifacts/9ea28a67-8c25-48b3-88f1-73e526b765a3

### M2 · Strategy
- **Decision matrix** — [`02-strategy/decision-matrix.md`](02-strategy/decision-matrix.md)
- **AI Strategy one-pager** — [`02-strategy/strategy-one-pager.md`](02-strategy/strategy-one-pager.md)

### M3 · RAG / AI PRD
- **AI PRD** — [`03-rag-prd/prd.md`](03-rag-prd/prd.md)
- **Harness diagnostic diff** — [`03-rag-prd/harness-diff.md`](03-rag-prd/harness-diff.md)

### M4 · AI-Native UX
- **AI user flow** — [`04-ai-ux/user-flow.md`](04-ai-ux/user-flow.md)
- **Trust-gap mitigations** — [`04-ai-ux/trust-gaps.md`](04-ai-ux/trust-gaps.md)

### M5 · Agentic Workflows
- **Agent Workflow Spec (AWSpec)** — [`05-agentic-workflows/awspec.md`](05-agentic-workflows/awspec.md)
- **Agent Control Panel** — [`05-agentic-workflows/agent-control-panel.md`](05-agentic-workflows/agent-control-panel.md)

### M6 · Evals & Guardrails
- **Eval stack** — [`06-evals/eval-stack.md`](06-evals/eval-stack.md)
- **Human evaluation rubric** — [`06-evals/human-rubric.md`](06-evals/human-rubric.md)

---

## PM Execution Plan

### Where Juno is today
- **Copilot, not agent (M2).** Juno drafts a ranked, cited backlog; the PM approves before anything publishes.
- **A clickable prototype exists (M1)** and now runs the full harness deterministically: visible tool trace, bounded 5-turn loop, a `write_roadmap` confirmation gate, and a verification gate that either cites the strategy doc or badges the insight "Unverified."
- **The harness is fully specified (M3 PRD, 6 surfaces)**, and the first agentic workflow — daily P0 triage into a top-3 risk list — is spec'd in M5 (ReAct, human-in-the-loop), with the M6 eval stack designed around it.
- **Not yet in production:** the live RAG backend over the RocketShip corpus. Today's grounding runs against a pasted strategy document, not the hourly-synced corpus described in M3.

### What ships next (next 2 sprints)
**Sprint 1 — make the grounding real.** Wire `search_strategy()` / `read_tickets()` to the actual corpus (Strategy One-Pager in git, Zendesk P0/P1, Slack #voice-of-customer), including the 24h "may be stale" citation label and the M3 fallback: if the strategy source is unreachable, rank nothing. Ship the verification gate live — verbatim-clause check, "Unverified" badge, and `write_roadmap()` rejecting unverified items.

**Sprint 2 — the first agent loop.** Stand up the M5 P0-triage workflow: ReAct loop over #escalations, top-3 risk list to #pm-daily, Jira stubs, with the 5-turn ceiling, 3-failure escalation, 90s timeout, and confidence routing (≥80% auto-post, 70–79% review channel, <70% PM approval). Permissions stay where M3 set them: **read/draft auto, write confirm, send blocked.**

_Scope stays M2's V1: rank the existing backlog with citations, surface under-cited items, flag Slack↔Jira conflicts. Out: headcount decisions and customer-facing comms._

### What I watch (dashboards)
- **Cycle time** — weekly prioritisation 2h → 30 min (target −75%).
- **Trust** — <10% of decisions reversed within 1 week; ≥90% of prioritised items carry ≥2 cited sources.
- **Evidence balance** — reject any ranking where <20% of cited sources come from a single source type (guards the training-data-lag skew from M2).
- **Quality (M6)** — golden-set accuracy ≥90%; human eval ≥4.0/5 on accuracy + safety; ≥80% thumbs-up, regenerate ≤15%, abandon ≤20%.
- **Cost / latency** — p95 < 8s, ≤ $0.12/run (~$58/mo at 12 PMs × ~40 runs).

### Red lines (what blocks shipping — numbers, not feelings)
- **0% PII leakage** — auto-block.
- **0 critical safety fails** (any "1" on the human-eval safety dimension) — auto-block.
- **Citation check fails → block.**
- **Golden-set accuracy < 90% → block.**
- **>50% of a run's priorities unverified** → banner and do not ship the ranking; treat the strategy doc as missing, stale, or wrong.
- **Strategy source unreachable → rank nothing** (no cached copy, no model judgement).
- **`send` stays blocked** until M6 evals produce a real precision number.
- _Soft gates (PM sign-off): p99 latency > 5s; off-brand tone flags > 2%._

### Governance
_Compliance · Safety · Reliability · Reputation._

- **Compliance** — excluded sources named on the record (Salesforce closed-lost notes, exec Slack DMs); 0% PII-leakage gate; no persistence of customer contracts or PII (M5 memory out-of-scope); per-team memory isolation.
- **Safety** — permission tiers priced by blast radius (read/draft auto, write confirm, send blocked); legal/contract language triggers refusal; any "churn / legal / security" thread requires PM approval.
- **Reliability** — 5-turn ceiling, escalate after 3 failed tool calls, 90s wall-clock timeout, and a contradiction between two strategy clauses escalates to a human rather than being tie-broken by Juno; p95 < 8s; no silent fallback.
- **Reputation** — nothing customer-facing in V1 (send blocked); a human approves the citation before any roadmap write; the whole system exists to kill loudest-voice-in-Slack prioritisation.

---

## Build Insights

- **Friction point.** The lab assumed a build tool with a one-click backend and AI gateway (Lovable). Without credits, I rebuilt the harness as a self-contained Claude artifact — which meant the "enable backend / Supabase Edge Function" step had no direct equivalent and had to be reframed (in Claude, the artifact itself is the runtime). Separately, it was easy to conflate two different diffs: _harness off vs on_ (old prototype vs harnessed) is not the same as _strategy loaded vs empty_.
- **Key learning.** A harness turns an agent from a black box that just answers into a system you can **watch** (the tool trace), **limit** (the turn ceiling and permission tiers), and **hold to a source of truth** (the verification gate). Permission tiers are priced by blast radius — a wrong draft costs two minutes, a wrong broadcast costs a week and can't be unsent — which is exactly why `write` is confirm and `send` is blocked.
- **Aha moment.** The "**Verified + Not Recommended**" case — a real clause backs the item, yet it's still out of scope — was governance working as designed, not a bug. And the verification rule "never invent a clause to pass the check" only bites once a real model can hallucinate; the deterministic demo passes it for free. That gap _is_ where the demo and the production agent diverge.

---

## Repo structure

```
juno-pm/
├── README.md                          ← this dashboard + pitch
├── 01-prompting/
│   ├── system-prompt.md               ← M1: Juno's system prompt
│   └── prototype.md                   ← M1: prototype link + debrief
├── 02-strategy/
│   ├── decision-matrix.md             ← M2: build / buy / fine-tune / partner call
│   └── strategy-one-pager.md          ← M2: AI strategy one-pager
├── 03-rag-prd/
│   ├── prd.md                         ← M3: AI PRD with retrieval requirements
│   └── harness-diff.md                ← M3: harness off vs on diagnostic notes
├── 04-ai-ux/
│   ├── user-flow.md                   ← M4: AI-native user flow
│   ├── user-flow.png                  ← M4: user-flow diagram
│   └── trust-gaps.md                  ← M4: trust-gap mitigations (⚠ still template)
├── 05-agentic-workflows/
│   ├── awspec.md                      ← M5: Agent Workflow Spec
│   ├── agent-control-panel.md         ← M5: Agent Control Panel
│   ├── agent-control-panel.png        ← M5: control-panel diagram
│   └── Juno Agent.json                ← M5: agent config export
└── 06-evals/
    ├── eval-stack.md                  ← M6: layered eval stack
    ├── eval-stack.png                 ← M6: eval-stack diagram
    └── human-rubric.md                ← M6: human evaluation rubric
```

---

_Certification submission — AI Product Management Certification._
