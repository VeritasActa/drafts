# Veritas Acta IETF Internet-Drafts

This repository contains the source for IETF Internet-Drafts published by the [Veritas Acta](https://veritasacta.com) protocol project.

## Drafts

### draft-farley-acta-signed-receipts

**Signed Decision Receipts for Machine-to-Machine Access Control**

Defines a portable, cryptographically signed receipt format for recording machine-to-machine access control decisions. Each receipt captures the decision maker, the tool or resource accessed, the policy evaluation result, and a timestamp, all signed with Ed25519 and serialized using JCS (RFC 8785).

- **Status**: Active, Individual Submission
- **Datatracker**: [draft-farley-acta-signed-receipts](https://datatracker.ietf.org/doc/draft-farley-acta-signed-receipts/)
- **Current revision**: -01
- **Verifier**: `npx @veritasacta/verify@0.2.5` (Apache-2.0, offline, zero dependencies)

### draft-farley-acta-knowledge-units

**Knowledge Units for Multi-Model Deliberation**

Defines the Knowledge Unit (KU) format for representing verified knowledge produced through structured multi-model deliberation. A KU captures the question asked, models that participated, consensus achieved, points of agreement and disagreement, and cryptographic receipts binding each deliberation round.

- **Status**: Submitted, Individual Submission
- **Current revision**: -00
- **Live implementation**: [acta.today/wiki](https://acta.today/wiki) (50+ Knowledge Units)
- **Specification**: [acta.today/wiki/spec](https://acta.today/wiki/spec)

## Relationship Between Drafts

The Knowledge Units draft builds on the Signed Receipts draft. Each round of a multi-model deliberation produces a signed receipt per `draft-farley-acta-signed-receipts`. The KU binds these per-round receipts into a hash chain, creating a single verifiable artifact for the entire deliberation.

```
Signed Receipts (envelope format)
    |
    +-- Knowledge Units (content schema + deliberation process)
            |
            +-- Round 1 receipts (independent model responses)
            +-- Round 2 receipts (adversarial critique)
            +-- Round 3 receipt (synthesis)
```

## Contributing

Issues and pull requests are welcome. The drafts follow IETF conventions (RFC 2119 key words, xml2rfc v3 format).

## References

- [Veritas Acta Protocol](https://veritasacta.com)
- [Receipt Verifier](https://www.npmjs.com/package/@veritasacta/verify) (Apache-2.0)
- [protect-mcp](https://www.npmjs.com/package/protect-mcp) (MIT, reference implementation)
- [Live Knowledge Base](https://acta.today/wiki)

## License

The IETF Trust's Legal Provisions apply to these documents. See the copyright notices in each draft.
