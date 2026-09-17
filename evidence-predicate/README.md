# ACTA — optional `evidence[]` predicate (Issue #3)

Proposed extension to the ACTA Access Decision Receipt carrying the **evidentiary
basis** of a decision: not just *which* decision, but *what it rested on* and
*whose* attestation that is. Additive and safely ignorable by existing verifiers.

Prepared by UseTruth in response to @tomjwxf's review on
`VeritasActa/Acta#3`. Intended for a PR against the draft and a vector for
`agent-governance-testvectors`.

## What this addresses (from the review)

1. **Closed vocabulary** so verifiers can reason rather than parse free text:
   - `state` ∈ `satisfied | unverified | stale | failed`
   - `dimension` ∈ `data_rights | provenance | freshness | contradiction` (core),
     extensible via an explicit `x-<vendor>-<name>` form so unknown values stay
     safely ignorable.
2. **`source` as a structured object**, not a bare string:
   `{ issuer, (key | ref) }` — `issuer` names the attesting authority; exactly one
   of `key` (an inline pinned JWK) or `ref` (a resolvable JWKS/DID reference) is
   present, so the relying party can **pin whose attestation this is**.
3. **Independence, encoded:** `source.issuer` MUST be an authority independent of
   the runtime being gated. Self-attested evidence is not evidence. The example
   also carries `evidence.claims.entitlement_verified_by_runtime: false`.
4. **Composes with the assurance tier, does not restate it.** The tier is derived
   from *who signed* the receipt (`payload.tier` / signer). `evidence[]` is about
   the *basis*; `source.issuer` names the evidence authority, which is distinct
   from the receipt signer.

## Proposed location

`payload.evidence.dimensions[]` — composes with the existing `payload.evidence`
object (e.g. `evidence.claims`). Happy to rename/relocate to a top-level
`payload.evidence[]` array if the draft prefers; the predicate schema is unchanged.

## Files

- `evidence-dimension.schema.json` — JSON Schema (draft 2020-12) for the predicate array.
- `receipt-with-evidence.example.json` — a **real, signed** ACTA v2 `decision_receipt`
  carrying the predicate. Not a mock: signed with Ed25519 over the canonical envelope.
- `verification-key.json` — the public verification key(s) (JWK + hex). **TEST keys**
  (deterministic, sha256-derived); not for production.

## Verifying the example

This vector verifies **VALID under the official `@veritasacta/verify` (v0.9.3)**:
format v2 `decision_receipt`, tier T1 (`ed25519-signature, jcs-canonicalization`).
Its `evidence.dimensions[]` also validate against `evidence-dimension.schema.json`.
It was signed with `@veritasacta/artifacts` (the same primitive the verifier uses):
strip `signature`, JCS-canonicalize the rest, Ed25519 over the canonical bytes.

Reproduce:

```
npx @veritasacta/verify receipt-with-evidence.example.json \
  --key <receipt_signer_hex from verification-key.json>
```

Note: run without `--mode` (auto-detect). Forcing `--mode receipt` can push a v2
receipt into passport detection and report a false signature failure.

The pinned key in the `provenance` dimension is a second, independent test key
(the "provenance authority"), included in `verification-key.json` — illustrating a
`source` whose attestation the relying party pins inline rather than resolving.

## Example, in one line

The vector is a **deny** of `DeleteGithubRepository` because `freshness` **failed**;
`data_rights` and `provenance` were `satisfied`, `contradiction` `unverified`. The
receipt proves the deny rested on a stale-evidence finding from an authority
independent of the runtime — which, for a regulated action, is the point.
