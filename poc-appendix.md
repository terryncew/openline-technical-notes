# Appendix: OpenLine evidence vs. Proof-of-Control v0.1 (Working Draft) — requirement-by-requirement mapping

**Author:** Terrynce White, independent researcher; creator of OpenLine. **Date:** 2026-10-12.
**Status:** Technical appendix to a draft public comment (comment window closes October 30, 2026). Not a conformance claim.
**Companion:** `poc-comment.md` (the concise comment this appendix supports).

## Method and sources

Mapped against Working Draft v0.1, repo `github.com/aai-society/ov-poc-standard` (master @ `93d0e28`, 127 requirements; verified 2026-10-12). Chapters C4 (Authorization), C5 (Identity), C7 (Evidence Generation & Properties), C8 (Verifiability Tiers). Chapters C1–C3, C6, C9, C10 are out of scope — OpenLine has no relevant evidence for them.

**The kit vs. the system.** Two different things are evaluated separately throughout:
- **The evidence-interop kit** (`github.com/terryncew/openline-evidence-interop`, HEAD `4e110a6`): public repo — frozen SPEC.md, standalone `verifier.py`, 11 synthetic vectors, trust policy, SHA-256 manifest, `check_vectors.py`. Claims about *receipt verification mechanics* belong here.
- **The OpenLine system** (wallet + receipt gate + bureau, public repos): claims about *enforcement behavior* (the gate deciding allow/refuse, revocation effects, provider-switch continuity) belong here. These rest on the author's recorded experiment runs, described in the frozen paper and repo READMEs.

A kit-level property never implies a system-level property. Where a claim is author-reported but not yet independently reproduced, it is marked as such.

## Status taxonomy

- **DEMONSTRATED** — shown in a recorded, reproducible run with published artifacts.
- **PARTIALLY DEMONSTRATED** — the mechanism exists and is partially tested, but not the full property.
- **NOT TESTED** — could plausibly hold; nobody has run the test. Not a failure, an open item.
- **UNSUPPORTED** — architecturally out of scope; no mechanism exists in the public materials.
- **N/A** — the requirement does not apply (e.g., no claim register exists).

## Three specific checks (principal's corrections, verified 2026-10-12)

**1. Wallet's agent-key verification.** The public `openline-wallet` README documents: subject Ed25519 keys (`keygen-subject`), a per-action one-time challenge tied to the exact action ("Agent B passes because it holds the key named in the current permission"), a long-lived identity key certifying a separate wallet signing key, and a signed hash-linked permission-change history. What the wallet does *not* publish: a recorded agent-credential *validation-failure* test with validation events in the evidence record (which is what 5.1.2's auditor evidence asks for). Mechanism: present and runnable. Failure-case evidence: absent.

**2. Receipt Gate's execution boundaries.** The public `openline-receipt-gate` README (v0.6.0rc6, MIT) states the gate checks the exact call against receiver-owned mandate/policy, current receiver-owned state, receiver-controlled evidence, trusted receipt keys, and replay/one-use state — then executes, quarantines, or denies. It also states: "This repository is not a hosted authorization service or a production certification. A production deployment must own its keys, policies, evidence providers, replay state, and every route to the protected effect." So: **decision evidence is demonstrated; executed-action evidence depends on the deployment mediating every route to the effect, which the public materials do not demonstrate.** Per PoC 7.1.5's own rule, OpenLine's public evidence reads as *decision* evidence.

**3. Reproduction limitations.** Reproducer invitations are OPEN and unanswered: `terryncew/openline-receipt-gate#109` (reference gateway, pinned commit `23b9031`) and `terryncew/openline-bureau#5` (autonomy Phase 1.5, pinned commit `5f75687`), both created 2026-10-12, both state OPEN (verified via API). The interop kit README states its external test "has not started." **No independent reproduction of any OpenLine enforcement claim has occurred.** Nothing in this document implies otherwise.

## Numerical claims — verification table

| Claim | Status | Source |
|---|---|---|
| check_vectors.py: 29 cases, 33 envelope verdicts, PASS | **Verified by direct run** 2026-10-12 (cloned public repo, ran in clean venv) | `openline-evidence-interop` HEAD `4e110a6` |
| 11 synthetic vectors | **Verified** (11 files in `vectors/`) | same repo |
| 104/104 Linux checks (receipt-gate) | **Published** in repo README | `openline-receipt-gate` README |
| 17/17 composition tests (kill-switch receipt integration) | **Published** in frozen paper evidence ledger | `openline-paper` v1.0 `evidence-ledger.md` |
| 45 recorded experiments (2026-09-04 – 2026-10-06) | **Published** in frozen paper abstract | `openline-paper` v1.0 `draft.md` |
| Paper I v1.0, DOI 10.5281/zenodo.23219814 | **Verified** (public repo, v1.0 tag) | `openline-paper`, Zenodo |
| PoC draft: 127 requirements, comment closes Oct 30 2026 | **Verified** | `aai-society/ov-poc-standard` README |
| Gateway "17/17 adversarial scenarios", "536/536 receipts verify" | **Not cited** — from the author's internal experiment records (branch not public); no public named source. Described qualitatively below as author-reported, reproduction invited. | — |
| Revocation timings (p50 ~1.4ms, max 35.6ms, n=30) | **Removed** — from internal measurements, no public source. Revocation described qualitatively from public sources. | — |
| Bundle default expiry 10 minutes | **Published** | `openline-wallet` README |

## C4 — Authorization

| Req | PoC requirement (paraphrase) | Status | Evidence / notes |
|---|---|---|---|
| 4.1.1 | Grant (permission set, scope, expiry) written to execution record before first action | PARTIALLY DEMONSTRATED | Architecture: mandates exist before actions (frozen paper). Kit receipts reference mandates. The temporal "before first action" property as a *tested* property: author-reported in gateway runs, not independently reproduced. |
| 4.1.2 | Every action evaluated against grant at execution time; record names permission or denial reason | PARTIALLY DEMONSTRATED | Gateway evaluates per action; recorded runs show named decisions/reasons (e.g., `STOPPED / ACTION_OUTSIDE_MANDATE`, paper L-B1). Kit DEMONSTRATES that decision/reason fields are present and well-formed in receipts. |
| 4.1.3 | Out-of-scope actions blocked at interception gateway, rejection recorded (L3) | PARTIALLY DEMONSTRATED | Block + rejection record demonstrated in recorded runs (paper L-B1; receipt-gate refund demo DENY/QUARANTINE). "At a gateway with no bypass path" is 7.1.1 — not tested. |
| 4.1.4 | Tool params validated against registered schema; digest stored (L2) | NOT TESTED | Adjacent mechanisms exist (argument freezing before evaluation; `extensions.seal` input/output hashes in kit) but schema-validated parameter digests are not demonstrated. |
| 4.1.5 | Per-task credentials; no standing broad credential (L2) | UNSUPPORTED | No per-task credential issuance mechanism described in public materials. |
| 4.1.6 | Human approval records: approver identity, presented content, decision, timestamp (L2) | NOT TESTED | Approvals occur in demos (e.g., manager approval in refund demo) but no published approval-record evidence format. |
| 4.1.7 | Path-aware evaluation of composed call sequences (L2) | UNSUPPORTED | No mechanism described. |
| 4.1.8 | Diagnostic outputs can't raise later authorization state (L2) | UNSUPPORTED | No mechanism described. |
| 4.2.1–4.2.4 | Delegation: token validation, chain signatures to principal, scope intersection, confidential-chain proofs | UNSUPPORTED | Single-principal mandates; no multi-hop delegation chains in public materials. (Provider *succession* under owner-signed intent is demonstrated — paper L-A5 — but that is not delegation.) |

## C5 — Identity

| Req | PoC requirement (paraphrase) | Status | Evidence / notes |
|---|---|---|---|
| 5.1.1 | Every execution record carries agent + principal identifiers (L1) | PARTIALLY DEMONSTRATED | Kit receipts carry `actor`/`principal` fields (SPEC.md — DEMONSTRATED as format). But the SPEC itself states these are signed identity *claims*, "not a discovered or independently certified identity" — so resolution to a real principal via an identity system is NOT TESTED. |
| 5.1.2 | Agent authenticates with cryptographic credential validated pre-execution; validation events recorded (L2) | PARTIALLY DEMONSTRATED | Mechanism exists publicly: subject Ed25519 keys, per-action one-time challenge tied to the exact action, `keygen-subject` (`openline-wallet` README, runnable demo). A recorded validation-*failure* test with validation events in the evidence record: NOT TESTED. |
| 5.1.3 | Per-call delegation token cryptographically linking call to grant (L2) | UNSUPPORTED | No delegation tokens. |
| 5.1.4 | Agent credential bound to attested environment (L3) | UNSUPPORTED | No attested binding. |
| 5.2.1/5.2.2 | Signed agent-to-agent messages; validation from published keys (L2/L3) | UNSUPPORTED | No agent-to-agent messaging. |

## C7 — Evidence Generation & Properties

| Req | PoC requirement (paraphrase) | Status | Evidence / notes |
|---|---|---|---|
| 7.1.1 | Separate-process Action Interception Gateway; no bypass path (L3) | PARTIALLY DEMONSTRATED | Separate-process deployment demonstrated in the Envoy gateway build (paper). The public `receipt-gate` `@authorize` decorator is in-process — a different deployment shape. No-bypass-path as a *tested* property: NOT TESTED (paper L-B8 reports no structural bypass found for the covered resource, author-reported). |
| 7.1.2 | Before/during/after independently-signed records per action (L3) | UNSUPPORTED | One signed decision receipt per evaluated action; no triple mechanism exists. |
| 7.1.3 | Evidence emission preconditions action release (L3) | NOT TESTED | Could plausibly hold; no write-before-forward test published. |
| 7.1.4 | Effect channel mediated within policy-evaluation trust boundary (L3) | NOT TESTED | Receipt-gate README assigns mediation to the deployment ("must own… every route to the protected effect"). No 7.1.4-option test published. |
| 7.1.5 | No executed-action claims without 7.1.4 (L1) | DEMONSTRATED | As a *discipline*: kit README + NOT_PROOF.md explicitly bound claims ("VALID authenticates a statement under a supplied policy. It does not prove the action happened"). OpenLine's public evidence reads as decision evidence. |
| 7.2.1 | Records written in-transaction with capture timestamps (L1) | PARTIALLY DEMONSTRATED | Receipts carry capture timestamps (kit format). In-transaction vs batch-reconstructed writing: NOT TESTED. |
| 7.2.2 | External timestamp anchoring (L3) | UNSUPPORTED | No TSA/transparency-log/consensus anchoring. |
| 7.2.3/7.2.4 | Attestation refresh interval; key bound into hardware quote (L3) | UNSUPPORTED | No hardware attestation. |
| 7.3.1 | Hash-chained / Merkle-anchored evidence records (L2) | UNSUPPORTED | Evidence records are per-record Ed25519-signed (RFC 8032, SPEC.md); no cross-record chaining. Note: the *revocation timeline* is hash-linked (paper) — a different artifact, not evidence records. |
| 7.3.2 | Signing keys held by generating mechanism, inaccessible to operator/agent (L3) | NOT TESTED | No key-custody separation demonstrated. In the demonstrated deployments the signing key is operator-held — which, under the draft's own 8.1.3 ("a system signing its own logs or outputs with its own key"), caps those evidence claims at Tier 2. |
| 7.3.3 | Equivocation resistance (L3) | UNSUPPORTED | Kit flags contradictory signed decisions per-record (`CONFLICT_AUTHENTICATED_CONTRADICTION`) — adjacent but not equivocation resistance. |
| 7.3.4/7.3.5 | Inclusion + consistency proofs (L3) | UNSUPPORTED | No Merkle proofs. |
| 7.4.1 | Published trust-assumption disclosure per C10.2 (L1) | PARTIALLY DEMONSTRATED | Honest-limits disclosures exist (paper claim classification; kit NOT_PROOF.md) but not in C10.2's standardized format. |
| 7.5.1 | Schema records execution facts only (L1) | DEMONSTRATED | Kit: NOT_PROOF.md and the README explicitly bound what VALID means ("authenticates a statement under a supplied policy; does not prove the action happened"); schema fields are identifiers, digests, timestamps, decision results. |
| 7.5.2 | Documented review showing public claims stay within those bounds (L1) | PARTIALLY DEMONSTRATED | NOT_PROOF.md exists as the claims-bound document and public materials are written against it, but a complete documented review of *all* public claims against the bound has not been published. |
| 7.6.1–7.6.6 | Custody: failure alerting, sequence numbers, fail-closed, store RBAC, retention, root anchoring | UNSUPPORTED | No evidence-custody mechanisms described. (Kit's FREEZE.sha256/MANIFEST.txt covers test vectors, not evidence custody.) |

### C7.7 — The Interoperable Property (kit-level: verification mechanics)

| Req | PoC requirement (paraphrase) | Status | Evidence / notes |
|---|---|---|---|
| 7.7.1 | Machine-readable schema covering every field, published where a verifier can obtain it; implementation output validates against it (L2) | PARTIALLY DEMONSTRATED | The schema is completely specified in normative text (SPEC.md §1 field table: types, required values, allowed members) and enforced by published code (`contract.py`/`verifier.py`), and the frozen vectors validate against it. But there is no standalone machine-readable schema artifact (e.g., a JSON Schema file) — a third party wanting to validate independently must use the published verifier code or transcribe §1. |
| 7.7.2 | Single canonical serialization declared; states exactly which bytes each digest and signature covers (L2) | DEMONSTRATED | SPEC.md §2: lexicographic key sort by code point, `,`/`:` separators with no whitespace, full escaping rules (incl. non-ASCII → lowercase `\uXXXX`, integers without leading zeros/exponents). Signed bytes = the canonical receipt bytes via DSSE Pre-Authentication Encoding (§4). `noncanonical-signed.json` → INVALID with reason `PAYLOAD_NOT_CANONICAL` (reason-checked by the harness). |
| 7.7.3 | Explicit algorithm identifiers on every digest and signature; unidentified digests rejected, not assumed (L2) | PARTIALLY DEMONSTRATED | Digests: the `sha256:` prefix is mandatory on seal hashes; `seal-hash-malformed.json` → INVALID (reason `output_hash`). Signatures: the contract *fixes* Ed25519 and REJECTS `alg` fields as invalid (SPEC §3) — this satisfies the requirement's anti-confusion intent (no algorithm negotiation; cf. the JWT `alg:none` class of attacks) but not its letter, since there is no per-signature algorithm identifier. An auditor applying 7.7.3 strictly could flag this; the design rationale is documented in the spec. |
| 7.7.4 | Conformance demonstrated against published vectors including negative cases; each negative rejected *for the reason it was written to test* (L3) | DEMONSTRATED | `check_vectors.py`: 29 cases / 33 verdicts PASS (run 2026-10-12 in a clean venv, no credentials). The harness asserts each negative vector's specific rejection reason (`duplicate JSON key`, `PAYLOAD_NOT_CANONICAL`, `output_hash`, wrong-key, tampered payload, etc.) — exactly what the auditor note demands ("a negative vector rejected for the *wrong* reason is not a pass"). |
| 7.7.5 | Parser rejects duplicate object keys rather than resolving last-wins (L2) | DEMONSTRATED | SPEC §2 mandates duplicate-key rejection at every depth ("duplicates already discarded by a permissive parser cannot be recovered"). `contract.py` raises `duplicate JSON key`; `duplicate-signed.json` and `duplicate-envelope.json` → INVALID with that exact reason (reason-checked). |

## C8 — Verifiability Tiers

| Req | PoC requirement (paraphrase) | Status | Evidence / notes |
|---|---|---|---|
| 8.1.1–8.1.3 | Claim register with tiers; trust analysis per claim; single-trusted-party claims capped at Tier 2 | N/A | No claim register exists. Note: 8.1.3's own examples ("a system signing its own logs or outputs with its own key") describe OpenLine's demonstrated deployments — operator-held signing keys — which is why no Tier 3 implication is made anywhere in this comment. |
| 8.1.4 | "Proof-of-Control" wording only for Tier 3+ claims | N/A (compliant) | No conformance claim is made in this document. |
| 8.1.5 | Tier 3+ claims need a recorded verification run without operator credentials (L3) | PARTIALLY DEMONSTRATED | For the kit's verification mechanics: demonstrated 2026-10-12 — cloned public repo, ran `check_vectors.py` in a clean venv, 29 cases / 33 verdicts PASS, no credentials of any kind. But 8.1.5 attaches to Tier 3+ *claims*, which are not made. |
| 8.1.6 | After-the-fact-only mechanisms register at Tier 3 max | N/A | No tier registrations exist. |
| 8.1.7 | Vendor-rooted attestations need independent anchoring for Tier 3 | N/A | No attestations. |
| 8.1.8 | Verification tooling published, versioned, usable without NDA/license/credentials (L3) | DEMONSTRATED | Kit: public repo, versioned (commit `4e110a6`, `FREEZE.sha256` manifest); cloned and run 2026-10-12 with nothing but public materials. |

## What would disprove the fit

Concrete findings that would show OpenLine's evidence does not meet a requirement it is mapped to above:

1. If 7.1.2 (before/during/after triple) is mandatory for any authorization-domain claim, the single-receipt model fails structurally.
2. If 7.3.1 (chained/Merkle-anchored log) is required at the claimed level, per-record Ed25519 signatures are insufficient on their face.
3. If 4.2.x delegation chains are required wherever authority is delegated, the single-principal mandate model is out of scope for those deployments.
4. If 7.1.1's no-bypass-path is required alongside 4.1.3, the current recorded runs do not satisfy it — bypass testing would be needed.
5. If "authority defined before it acts" requires the grant→action linkage inside one tamper-evident chain rather than per-decision records referencing a mandate, OpenLine's per-receipt `authority.status` does not satisfy it.
6. Per the draft's own 8.1.3, any deployment where the evidence signing key is operator-held is Tier 2 — which describes OpenLine's demonstrated deployments. Key-custody separation would be needed before any higher-tier discussion.

## Sources (all public, verified 2026-10-12)

- PoC draft: `github.com/aai-society/ov-poc-standard` @ `93d0e28`, 127 requirements, comment closes Oct 30 2026.
- Paper: `github.com/terryncew/openline-paper`, v1.0 tag, DOI `10.5281/zenodo.23219814`.
- Interop kit: `github.com/terryncew/openline-evidence-interop` @ `4e110a6` (SPEC.md, `verifier.py`, `check_vectors.py`, 11 vectors).
- Wallet: `github.com/terryncew/openline-wallet` (public README: subject keys, one-time challenge, hash-linked revocation timeline, 10-min bundle expiry).
- Receipt gate: `github.com/terryncew/openline-receipt-gate` v0.6.0rc6, MIT (public README: decision checks, quarantine/deny, 104/104 checks, refund demo).
- Reproduction invitations: `openline-receipt-gate#109`, `openline-bureau#5` — both OPEN, created 2026-10-12.
