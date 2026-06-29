# Contributing to evm-pqc-db

This database is the community-maintained CVE-equivalent for quantum-vulnerable cryptographic patterns in EVM smart contracts.

## How to add a pattern

Open a PR adding your entry to `evm-pqc-patterns-v1.json`. Follow this template:

```json
{
  "id": "EVM-PQC-XXX",
  "name": "Short descriptive name",
  "severity": "CRITICAL | HIGH | MEDIUM | LOW",
  "category": "SIGNATURE | KEM | HASH | SYMMETRIC | KDF | ZK_PROOF",
  "eip_references": ["EIP-N"],
  "affected_opcode": "CALL to precompile 0x01 (or N/A for off-chain)",
  "solidity_patterns": [
    "function_name(",
    "ContractName",
    "importedLibrary.method("
  ],
  "description": "What the pattern is, why it's quantum-vulnerable, and the attack model.",
  "quantum_attack": "SHOR | GROVER | HNDL | SHOR+REPLAY",
  "q_day_risk": "CRITICAL | HIGH | MEDIUM | LOW",
  "hndl_risk": true,
  "hndl_note": "Why this pattern is specifically exposed to Harvest-Now-Decrypt-Later.",
  "affected_protocols": [
    "Protocol Name (specific version if relevant)"
  ],
  "migration_target": "ML-DSA-65 (NIST FIPS 204) or equivalent quantum-safe alternative.",
  "migration_effort": "S (hours) | M (days) | L (weeks+)",
  "references": [
    "EIP or standard document",
    "NIST FIPS XXX (year)"
  ]
}
```

### Severity criteria

| Severity | Criteria |
|---|---|
| CRITICAL | Direct key/signature forgery possible OR double vulnerability (quantum + classical flaw) |
| HIGH | Shor-vulnerable signature scheme used in contract authorization |
| MEDIUM | Grover-affected hash or symmetric primitive (security reduced but not broken) |
| LOW | Informational — best-practice upgrade recommended |

### Quantum attack classification

| Code | Meaning |
|---|---|
| `SHOR` | Shor's algorithm breaks discrete log or integer factorization (ECDSA, RSA, DH, BLS) |
| `GROVER` | Grover's algorithm halves brute-force security (AES-128 → 64-bit effective) |
| `HNDL` | Harvest Now, Decrypt Later — off-chain signed messages or TLS traffic captured today, decrypted post-Q-Day |
| `SHOR+REPLAY` | Shor-vulnerable + classical replay attack (double vulnerability) |

### What counts as `hndl_risk: true`

Mark `hndl_risk: true` when:
- The signing happens **off-chain** and the signed message is broadcast/stored publicly
- A long-lived **TLS session** carries key material (bridge relayers, oracle nodes)
- The vulnerability can be exploited by **storing data today** for decryption after Q-Day (no CRQC needed at capture time)

### Pattern ID assignment

Use the next available `EVM-PQC-XXX` number. Check the current max in `statistics.total_patterns` before submitting.

### PR checklist

- [ ] Pattern ID is unique and sequential
- [ ] `solidity_patterns` array contains real Solidity/ABI identifiers (not pseudocode)
- [ ] `affected_protocols` names at least one real deployed protocol
- [ ] `migration_target` references a NIST FIPS standard or equivalent
- [ ] `references` includes at least one primary source
- [ ] Updated `statistics` block at the bottom of the JSON

## What we don't accept

- Patterns that are purely classical vulnerabilities with no quantum dimension (those belong in SWC registry)
- Patterns without a real deployed protocol as example
- Duplicate patterns — check existing IDs first

## Review process

A maintainer will review within 7 days. Automated validation runs on PR open (checks JSON schema and ID uniqueness). Merged patterns are automatically picked up by the QuantumScan scanner at the next release.

## Questions?

Open an issue or DM [@quantumscan_io](https://twitter.com/quantumscan_io).
