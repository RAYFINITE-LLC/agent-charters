# Charter — docs-sync-agent

A filled example: a standing agent that keeps a project's documentation in sync with its own
source tree. See [CHARTER-TEMPLATE.md](../CHARTER-TEMPLATE.md) for the blank form and
[README.md](../README.md) for the discipline behind it.

## Declaration

```
Handle: docs-sync-agent · Owner: platform-docs-lead (role)
Kind: service — fixed role, own standing identity; does not act on behalf of individual requesters
Authority: read the repository's full source tree and existing docs; open pull requests scoped to
  the docs/ directory; comment on its own pull requests · Deny: no merge rights, no access to CI or
  deploy configuration, no access to credentials or secrets of any kind
Budget: 200K tokens per run · 2M tokens per 7-day period · At-ceiling: pause and escalate to
  Owner — never silent continuation
Audit: every run logs its trigger, files read, files changed, and the resulting pull request URL,
  to the project's standard run log · Correlation key: charter id (docs-sync-agent) + run id,
  attached to every log line and to the pull request description
Revocation: Owner can suspend the charter, which blocks the next scheduled trigger from starting
  a new run · Roster halt: the agent checks the shared pause signal at the start of every
  run, before touching the repository
Residency: runs in a dedicated CI job on the project's own infrastructure, one job per trigger ·
  Registry entry: registry/agents.yaml#docs-sync-agent
```

## Authority detail

- [x] Read access to the full repository source tree
- [x] Read access to the existing `docs/` directory
- [x] Write access to open (never merge) pull requests, scoped to paths under `docs/`
- [x] Comment access on its own pull requests, for status updates
- [x] No access to CI workflow definitions, deploy configuration, or any credential store —
      confirmed against the standing deny set

## Budget detail

- Per-run ceiling: 200,000 tokens — behavior at ceiling: pause the run, open a draft pull request
  with progress so far, escalate to Owner
- Per-period ceiling: 2,000,000 tokens / 7 days — behavior at ceiling: no new runs start until the
  period rolls over, or Owner raises the ceiling
- Escalation target: platform-docs-lead, via the project's standard on-call channel

## Revocation check (exercised 2026-01-14)

- [x] Suspend path tested — marking the charter suspended blocked the next scheduled trigger,
      confirmed by an intentionally skipped run
- [x] Roster-wide halt path tested — the agent observed the shared pause signal and exited
      before reading the repository
- [x] Owner confirmed reachable for an escalation

## Review log (terse, append-only)

- 2026-01-10 — charter created; scoped to `docs/` only, after an earlier draft proposed
  repository-wide write access
- 2026-01-14 — revocation checks exercised and passed; charter activated
- 2026-01-28 — budget raised from 100K to 200K tokens per run after two consecutive
  pause-and-escalate events on unusually large documentation sets
