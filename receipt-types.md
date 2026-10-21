# Acta receipt types

An informative registry of receipt types for
[draft-farley-acta-signed-receipts](https://datatracker.ietf.org/doc/draft-farley-acta-signed-receipts/).

The draft defines the receipt format, how a receipt is signed and verified,
and one receipt type, the Access Decision Receipt (`protectmcp:decision`).
The types below were defined in the draft itself up to and including
revision -03, and are listed here from -04 on. Nothing here changes how a
receipt is signed or verified: a receipt of any type listed here is checked
exactly as the draft says.

Each type is named by the receipt's `type` member, a namespaced string whose
part before the colon is the namespace. Each entry keeps the description,
example and field list the draft had, except where the entry says otherwise.
The members common to every payload (`type`, `issued_at`, `issuer_id` and the
others in Section 2.2 of the draft) are not repeated.

## Agent Lifecycle Receipt

- Type: `protectmcp:lifecycle`
- Namespace: `protectmcp`
- Status: registered, informative; first published in
  draft-farley-acta-signed-receipts-01

Records agent lifecycle events in multi-agent orchestration systems (swarms,
coordinator mode, scheduled agents). Enables reconstruction of the full agent
topology from the receipt DAG.

```json
{
  "type": "protectmcp:lifecycle",
  "lifecycle_event": "subagent_start",
  "agent_id": "worker-a1b",
  "agent_type": "general-purpose",
  "parent_session_id": "ses_7f8a2b",
  "team_name": "backend-ops",
  "sandbox_state": "enabled",
  "issued_at": "2026-04-01T10:15:00.000Z",
  "issuer_id": "sb:issuer:4Kpm7Q3wXx2b"
}
```

Fields:

- `lifecycle_event` (REQUIRED): One of: `"subagent_start"`,
  `"subagent_stop"`, `"session_start"`, `"session_end"`, `"task_created"`,
  `"task_completed"`, `"teammate_idle"`, `"config_change"`.
- `agent_id` (OPTIONAL): The identifier of the agent involved in the
  lifecycle event.
- `agent_type` (OPTIONAL): The type of agent (e.g., `"coordinator"`,
  `"worker"`, `"standalone"`, `"general-purpose"`).
- `parent_session_id` (OPTIONAL): The session identifier of the
  parent/coordinator agent. Creates a verifiable parent-child relationship in
  the DAG.
- `team_name` (OPTIONAL): The team or swarm name, if operating in multi-agent
  mode.

## Arena Battle Receipt

- Type: `blindllm:arena-battle`
- Namespace: `blindllm`
- Status: registered, informative; first published in
  draft-farley-acta-signed-receipts-00

Records the outcome of a competitive evaluation between two AI agents, as
conducted by a neutral arena platform.

```json
{
  "type": "blindllm:arena-battle",
  "battle_id": "bat_9x8f7a2b",
  "lane_id": "lane_creative_writing",
  "agent_a": {
    "id": "sb:agent:3mK9pQ7wXx2b",
    "manifest_version": "2.1.0"
  },
  "agent_b": {
    "id": "sb:agent:8xKm3Qw2Yb1c",
    "manifest_version": "1.4.0"
  },
  "winner": "A",
  "issued_at": "2026-03-22T15:00:00.000Z",
  "issuer_id": "sb:issuer:4Kpm7Q3wXx2b"
}
```

Fields:

- `battle_id` (REQUIRED): Unique identifier for the battle instance.
- `lane_id` (REQUIRED): The evaluation category or "lane" in which the battle
  occurred.
- `agent_a`, `agent_b` (REQUIRED): Objects identifying each participant,
  containing:
  - `id`: The agent's passport identifier.
  - `manifest_version`: The agent's manifest version at battle time.
- `winner` (REQUIRED): One of: `"A"`, `"B"`, `"tie"`.

## Spending Authority Receipt

- Type: `scopeblind:spending_authority`
- Namespace: `scopeblind`
- Status: registered, informative; first published in
  draft-farley-acta-signed-receipts-01

Records the outcome of an issuer-blind spending authorization check. Proves
that an agent's purchase is within authorized limits without revealing
organizational identity, budget ceiling, or delegation chain.

```json
{
  "type": "scopeblind:spending_authority",
  "amount": 99.50,
  "currency": "USD",
  "category": "cloud_compute",
  "utilization_band": "low",
  "decision": "allow",
  "policy_digest": "sha256:a8f3...c91e",
  "issued_at": "2026-04-01T12:00:00.000Z",
  "issuer_id": "sb:issuer:4Kpm7Q3wXx2b"
}
```

Fields:

- `amount` (REQUIRED): The monetary amount of the transaction.
- `currency` (REQUIRED): ISO 4217 currency code (e.g., `"USD"`).
- `category` (OPTIONAL): A classification of the spending type (e.g.,
  `"cloud_compute"`, `"saas_subscription"`, `"api_usage"`).
- `utilization_band` (OPTIONAL): A coarse indicator of budget utilization.
  One of: `"low"` (<25%), `"medium"` (25-75%), `"high"` (75-100%),
  `"exceeded"`. The exact budget ceiling is NOT disclosed, only the band. This
  prevents price discrimination while proving budget compliance.
- `decision` (REQUIRED): One of: `"allow"`, `"deny"`.

The receipt deliberately EXCLUDES: organization name, total budget ceiling,
exact remaining budget, agent identity, and delegation chain.

One sentence of the draft's text, about verification with VOPRF, is not
carried over: the receipt names its issuer in `issuer_id` and is checked under
that issuer's key.

## Formal Debate Receipt

- Type: `blindllm:formal-debate`
- Namespace: `blindllm`
- Status: registered, informative; first published in
  draft-farley-acta-signed-receipts-00
- Fields: not yet defined.

Records the outcome of a structured debate with audience and judge scoring.
Extends the arena battle format with additional governance fields. The draft
gave an example receipt for this type but defined none of its fields, so the
example is not carried over.

## Folded types

- `protectmcp:restraint`, the Restraint Receipt of revisions -00 to -03, is
  folded into the Access Decision Receipt from -04 on. A `"deny"` whose
  `reason` is `"policy_block"` or `"agent_refusal"` records what its
  `denial_type` recorded, and its `agent_id` and `agent_manifest_version`
  members are OPTIONAL members of the Access Decision Receipt.
