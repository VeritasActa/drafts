# Agent manifest

An informative note for
[draft-farley-acta-signed-receipts](https://datatracker.ietf.org/doc/draft-farley-acta-signed-receipts/).

Revisions -00 to -03 of the draft had a section on agent identity: a signed
agent manifest, and a planned DPoP binding. From -04 the draft no longer
contains it, and nothing in the draft depends on it. It is kept here as a
note, with one change: the manifest's key member is renamed, as explained
below.

## Passport manifest

An agent's identity is expressed as a signed manifest:

```json
{
  "type": "scopeblind:agent-manifest",
  "id": "sb:agent:3mK9pQ7wXx2b",
  "version": "2.1.0",
  "previous_version": "2.0.0",
  "created_at": "2026-03-20T10:00:00Z",
  "agent_public_key": "<base58-ed25519-public-key>"
}
```

Manifests are IMMUTABLE once signed. Version changes create new manifests
that reference their predecessor via `previous_version`, forming a verifiable
version chain.

### Why `agent_public_key`

The draft named this member `public_key`. From -04 the draft calls the value
of a `public_key`, `verification_key` or `verification_jwk` member of the
signed bytes of a receipt a carried key: a verifier never verifies with a
carried key, and a receipt that carries a key other than the trust-anchored
key is invalid (Section 7.5 of -04, Key Distribution and Trust Anchors). That
rule applies to receipts, not to manifests, but a manifest member of the same
name invites tools and readers to confuse an agent's own identity key with a
key carried in a receipt. The new name keeps the two apart.

`@scopeblind/passport` 0.4.3 still writes `public_key` in the manifests it
creates.

## DPoP binding (future)

For remote MCP transports (HTTP/SSE), agent identity MAY be bound to
per-request proof-of-possession using DPoP
([RFC 9449](https://www.rfc-editor.org/info/rfc9449)). The
`auth_key_bindings` field in the manifest links the agent's Ed25519 identity
key to one or more P-256 DPoP keys.

This binding creates a verifiable delegation chain:

```
Operator -> Agent Identity (Ed25519) -> Request Auth (P-256 DPoP)
```
