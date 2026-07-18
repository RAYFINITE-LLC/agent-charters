# Charter — `<agent handle>`

Copy this file into the agent's registry entry (or fill the equivalent fields in your registry's
schema) before the agent's first standing run. All six declaration parts are REQUIRED — an agent
without a complete charter is not ready to run on a standing basis. See [README](README.md).

## Declaration

```
Handle: <stable agent handle> · Owner: <accountable human or role>
Kind: service <fixed role, own standing identity> | delegate <acts on behalf of a person/tenant, forwards their identity>
Authority: <modules/surfaces/tools it may use, as a scope list> · Deny: <standing deny list>
Budget: <ceiling per run> · <ceiling per period> · At-ceiling: pause and escalate — never silent continuation
Audit: <what it must log> · <where the log lives> · Correlation key: <charter id + run id, or equivalent>
Revocation: <how this charter is suspended> · <how it is retired> · Roster halt: <how it observes a roster-wide pause>
Residency: <where it runs — environment/repository/workspace> · Registry entry: <the file or record that owns this charter>
```

## Authority detail

- [ ] <each granted surface/tool/write-scope, one line each>
- [ ] Deny list confirmed against the standing deny set for this environment

## Budget detail

- Per-run ceiling: `<value>` — behavior at ceiling: pause and escalate
- Per-period ceiling: `<value / period>` — behavior at ceiling: pause and escalate
- Escalation target: `<who or what is notified>`

## Revocation check (exercise before the first standing run)

- [ ] Suspend path tested — the agent takes no new runs once its charter is marked suspended
- [ ] Roster-wide halt path tested — the agent observes the shared pause signal at its next checkpoint
- [ ] Owner confirmed reachable for an escalation

## Review log (terse, append-only)

- `<date>` — `<what changed: authority grant, budget revision, ownership transfer, suspension, retirement>`
