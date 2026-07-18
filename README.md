# Agent Charters

**The declared, validatable terms under which a standing AI agent operates — so a roster of agents stays answerable, not just busy.**

An agent that runs once and exits is a task. An agent that keeps a fixed role — a recurring job runner, a standing reviewer, a service identity other systems call into — is a *standing agent*, and a standing agent needs more than a good prompt. A charter answers, before the agent ever runs, everything an auditor would have to reconstruct after:

**On whose behalf does it act? What may it touch? What does it cost? What trace does it leave? How does it stop?**

Most agent stacks answer the first four unevenly, and the fifth barely at all. A session log might show who acted. A tool-permission list might show what it could reach. Spend dashboards, if they exist, rarely tie back to one standing identity rather than an API key shared across a dozen of them. Evidence of what happened usually means grepping chat transcripts after something has already gone wrong, not a correlation key built in from the start. And "how does it stop" — the question every incident eventually asks — is too often answered with "we're not sure, let's find out."

An **Agent Charter** is the fix: a short, structured declaration that makes those five questions answerable by construction, for every standing agent, before it ever runs — not reconstructed afterward from logs nobody designed for the purpose.

## Why this is a governance problem, not a prompting problem

Better prompts make an agent behave better *this run*. They do nothing for the roster question: once an organization has ten, then fifty, then a few hundred agents holding standing roles, "which one did this, and was it allowed to?" stops being answerable from memory. That is a governance gap, not a modeling one — and across current multi-agent practice, the two concerns most often left informal, even in otherwise disciplined setups, are enforced cost governance and a clear point where a human can see and intervene. Both are exactly what a written, checkable declaration fixes: not by adding process for its own sake, but by making the answer to on whose behalf, what it may touch, what it costs, what trace it leaves, and how it stops a property of the system instead of a forensic exercise after the fact.

## The Agent Charter

A charter declares six things about one standing agent. All six are required — a standing agent without a complete charter is running on trust, not governance.

### 1. Identity — *on whose behalf does it act?*
A stable handle, an accountable owner (the human or role who answers for this agent), and a **kind**:

- **service** — the agent has its own standing identity and acts as itself: a fixed role such as a scheduled job runner, a monitoring bot, or a standing reviewer.
- **delegate** — the agent acts *on behalf of* a specific person or tenant, forwarding their identity rather than asserting its own. Authorization is evaluated as that person, not as the agent.

Naming the kind up front resolves the single most common authority mistake: granting a delegate the standing permissions of a service, or a service the narrow, borrowed permissions of a delegate.

### 2. Authority — *what may it touch?*
What the agent may do without asking — which tools, which surfaces, which write scopes — plus an explicit deny list for what it must never touch regardless of what it can technically reach. Authority is written as data, a scope list, not a paragraph of prose, so it can be checked at invocation time and not merely read at design time.

### 3. Budget and attribution — *what does it cost?*
A cost or token ceiling per run and per period, and a stated rule for what happens at the ceiling: **pause and escalate, never continue silently.** Every run is tagged with its charter's identity, so spend rolls up by agent, by owner, by project — the third question, at what cost, answered by the accounting itself rather than by a postmortem.

### 4. Audit obligations — *what trace does it leave?*
What the agent must log, where that log lives, and the correlation key that lets one action be traced end to end, from the triggering event through every tool call to the final effect. An agent with authority but no audit trail has answered "on whose behalf" and "what may it touch" and failed "what trace does it leave."

### 5. Revocation — *how does it stop?*
How the agent is paused or retired — individually, this one charter suspended, and as part of a roster-wide halt. This is the question the other four don't cover on their own, and the one most stacks never actually answer until they're forced to, mid-incident. **A charter that cannot be revoked is not a charter; it is a permanent grant with paperwork attached.** Revocation must be a real, exercised control, not a theoretical one nobody has ever pulled.

### 6. Residency
Where the agent actually runs — which environment, repository, or workspace convention it follows — and which registry entry is the source of truth for it. An agent whose real running location has drifted from its declared residency is a charter that is already lying.

## The registry is the collection of charters

A **registry** is not a separate system layered on top of charters — it *is* the set of charters, held in one schema-validated place: a version-controlled file (or a small set of them), checked by a validator, not a spreadsheet someone edits by hand and slowly loses track of. Any human-readable roster of "which agents exist" should be *generated from* the registry, never maintained beside it. A roster and a registry that can disagree is the same failure as the five questions going unanswered — just one step removed from where it actually bites.

## The boundary: standing agents vs. session-scoped work

Not everything that runs needs a charter. A short-lived helper spawned to do one bounded piece of work inside a larger delegation — a fan-out worker, a build step, a sub-task that starts and ends within a single parent run — is **session-scoped**, not standing. It inherits the spawning agent's charter: its authority, its budget attribution, its audit trail. The correlation key still traces back to a real charter; it simply is not a new one.

The line is standing identity, not how long a process happens to live or how many steps it takes along the way. An agent that shows up once and never again does not need a charter. An agent that shows up under the same name every day, every week, or on every trigger of a given kind is standing, and it needs one. Chartering every ephemeral helper would bury the five questions in paperwork instead of answering them; *not* chartering a genuinely recurring role is the opposite failure — and, in practice, the more common one.

## Anti-pattern catalog

| Anti-pattern | Smell | Cure |
|---|---|---|
| Registry drift | the roster of "agents we have" and the agents actually running disagree | the registry is generated, never hand-maintained alongside the truth |
| Uncharted agent | a recurring role is quietly running on a shared credential or someone's personal token, with nothing declared for it | give it its own identity and a charter before it runs again |
| Meter without a breaker | spend crosses the declared ceiling and the agent just keeps running | pause-and-escalate is the only acceptable behavior at the ceiling; a limit nobody enforces is a suggestion, not a budget |
| Authority by accident | the agent's real tool access is broader than what its charter, if it even has one, declares | authority is data, checked at invocation — not a description that quietly goes stale |
| Permanent by default | nobody can say, with confidence, how this one would actually be stopped | the revocation path is drill-tested before it's ever needed, not improvised while an incident is live |
| Session leakage | a short-lived helper is granted a standing identity of its own, "to be safe" | session-scoped work inherits the spawner's charter; it does not mint a new one |
| Untraceable trail | logs exist for every hop the action took, but nothing ties them into one story | the correlation key is part of the charter from day one, not bolted on after an incident demands it |

## Adopting this in any agent stack

Agent Charters is deliberately tool-agnostic — a discipline, not a dependency on any particular framework, orchestrator, or vendor:

1. **Charter before you schedule.** Before a role becomes standing — before it runs on a trigger, a cron, or "whenever this kind of event happens" — write its charter using [`CHARTER-TEMPLATE.md`](CHARTER-TEMPLATE.md); see [`examples/docs-sync-agent.charter.md`](examples/docs-sync-agent.charter.md) for a filled-in one. A one-off task does not need one; a role does.
2. **Make authority machine-checkable.** However your stack enforces tool access — policy files, API scopes, IAM roles, a permissions middleware — the charter's authority section should be the source those mechanisms read from, not a description that lives apart from them and can drift.
3. **Attribute spend at the source.** Tag cost at the point it is incurred with the charter's identity, rather than reconstructing attribution later from a shared billing line.
4. **Test revocation before you need it.** A kill switch that has never been exercised is a hypothesis, not a control.
5. **Generate the roster; do not hand-edit it.** Whatever tells a human "here are our agents" should read from the registry, not maintain a parallel truth of its own.

## See also

- **[Objective Contracts](https://github.com/RAYFINITE-LLC/objective-contracts)** — the discipline for one bounded *task* handed to an agent. A charter governs the standing agent; an objective contract governs the work it is doing right now. A well-run standing agent still needs objective contracts for each piece of work it takes on.
- **[Standing Orders](https://github.com/RAYFINITE-LLC/standing-orders)** — the discipline for one recurring, unattended *job*: declared triggers, autonomy level, stop conditions, and a kill switch. A standing order is often what a charter's agent is doing on repeat; the charter is what makes the agent running it answerable in the first place.

The three sit at different altitudes: an objective contract bounds one task, a standing order bounds one recurring job, an agent charter bounds one standing identity. None of the three replaces either of the others.

## License

[MIT](LICENSE) — © 2026 Pradeep Singala Reddy / RAYFINITE LLC.
This document synthesizes widely-shared industry lessons on multi-agent governance.
Use it, adapt it, hold your agents to it.
