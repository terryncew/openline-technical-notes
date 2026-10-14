# Proposed public comment: evidence of receiver-side authorization
**Author:** Terrynce A. White, independent researcher and creator of OpenLine  
**Prepared:** October 14, 2026  
**Status:** FOR OWNER APPROVAL — not submitted to the working group  
**Reviewed draft:** [LFDT-ProofOfControl/ov-poc-standard, commit 93d0e28](https://github.com/LFDT-ProofOfControl/ov-poc-standard/tree/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en) (the current `master` at this review). The public-comment deadline is October 30, 2026.

This comment offers two authorization/evidence clarifications and one tier-verification distinction. It is based on a published case study of an **unmerged, synthetic** receiver implementation. The proposals are technology-neutral; OAuth, OpenID AuthZEN, OPA, or capability systems could satisfy them. This is not a Proof-of-Control conformance claim or a request that the working group adopt OpenLine.

## 1. C4.1.2 and C4.1.3: distinguish upstream authority from receiver-local policy

**Observed defect.** In an initial OpenLine receiver wrapper, locally required caller scopes were supplied to an inner gate parameter used to record *carried recipient scopes* for routing. The wrapper then checked its requirements against state that its own gate had just populated. A cryptographically valid owner mandate covering action `a` could pass even though the owner had not granted the additional scope the receiver required. An unconfigured receiver action also defaulted to an empty scope requirement, allowing an owner-authorized action that the receiver had not explicitly configured. The underlying gate still verified the owner signature, subject binding, one-use challenge, and action inclusion. This was a receiver-policy composition error, not a compromise of the underlying cryptographic checks.

**Requested clarification.** C4.1.2 should explicitly require that applicable receiver-local policy be evaluated separately from verified upstream principal authority before an action is admitted. Receiver-created routing state and caller-carried claims must not be promoted into evidence that the upstream issuer granted the corresponding scope. C4.1.3's auditor guidance should require an explicit deny for an action that the receiver has not configured as admissible.

**Suggested auditor test:** present a valid owner mandate authorizing `a` but omitting receiver-required scope `s`. The receiver must refuse `a`, and the evidence should identify the upstream grant and receiver-local rule as distinct inputs. Repeat for an owner-authorized action absent from the receiver's local action registry: deny. Finally show an explicitly configured, properly scoped action allowed. This test is independent of token format and policy implementation.

## 2. C7.1.4 and C7.1.5: an intermediate signed decision is not final admission or an effect receipt

**Source-level architectural concern (no exploit demonstrated).** In a corrected experimental wrapper, the inner authorization gate can sign an in-memory `ALLOWED` result before the wrapper has evaluated every additional receiver-local scope requirement. When the wrapper refused, that intermediate result was not observed escaping, being logged, or driving an unauthorized effect. Yet a downstream component could misunderstand an inner signature as a complete admission decision if the boundary were changed.

**Requested clarification.** Auditor guidance should distinguish a provisional component-level decision from a **final receiver authorization**. A receiver should either sign final `ALLOWED` only after all required owner-authority and receiver-local-policy checks succeed, or cryptographically constrain any intermediate result so it cannot be accepted as final admission. Test a case where an inner component reports allowed but receiver-local policy denies; the final output must be refusal and the intermediate signature must not authorize any downstream action.

This leaves C7.1.5 intact: even a properly signed *final decision* does not prove that the real-world effect occurred. Claims about executed actions require the effect-channel mediation and evidence specified in C7.1.4.

## 3. C8.1.5 and C8.1.8: independently repeatable verifier tests are not independently verified deployment claims

Publishing a canonical receipt format, open verifier, and negative vectors lets third parties test the **verification mechanics** with no operator credentials. It does not establish that a production operator produced an exhaustive, non-equivocating history of effects. The current tier criteria should make that distinction unmistakable.

**Requested clarification.** In auditor evidence for C8.1.5/C8.1.8, separately assess (a) the reproducibility of a public verification program against synthetic vectors, and (b) independent verification of a *specific deployed control claim* with a documented effect boundary, key custody, completeness and trust assumptions. A passing (a) must not confer Tier 3 on (b). The OpenLine kit demonstrates (a); the receiver implementation does not demonstrate a Tier 3 control-evidence deployment.

## Evidence, limits, and disposition requested

**Published report:** White, Terrynce A. (2026). *Receiver-Side Authorization Failures in Cross-Boundary AI Agent Systems: A Case Study of Scope Reflection, Fail-Closed Policy Checks, and Signed Decisions* (v1.0.0-rc1). Zenodo. https://doi.org/10.5281/zenodo.23358012

**Reproducibility:** [original source PR #104](https://github.com/terryncew/openline-receipt-gate/pull/104); [corrected draft PR #116](https://github.com/terryncew/openline-receipt-gate/pull/116), tested commit [8b8612c](https://github.com/terryncew/openline-receipt-gate/commit/8b8612c0fe9d81b521a3fb81e5063420d835ee1a); [CI run 38501462915](https://github.com/terryncew/openline-receipt-gate/actions/runs/38501462915), frozen evidence [artifact 11865617433](https://github.com/terryncew/openline-receipt-gate/actions/runs/38501462915/artifacts/11865617433). The two-process fixture used one owner, two worker-specific mandates, and two differently configured receiver processes **on one CI host**. It passed 28 fixture assertions and 10 prior regression checks, producing 10 verifiable signed decisions (3 ALLOWED, 7 STOPPED) and three **local simulated effects**. It did *not* demonstrate independently operated receiving organizations, production effects, or superiority to security-equivalent OAuth/AuthZEN/OPA.

The separate [OpenLine evidence-interop kit](https://github.com/terryncew/openline-evidence-interop) demonstrates verifier and vector mechanics, not a complete operational control-evidence system. The original [October 12 requirement mapping](poc-appendix.md) and the [October 14 addendum](poc-evidence-addendum-2026-10-14.md) disclose further gaps, including effect bypass, custody, chaining, equivocation, and Tier 3 key assumptions.

**Requested disposition:** Please treat these as concrete auditor-test and wording suggestions under C4, C7, and C8. If existing clauses already require these outcomes, identifying the exact expected audit test would resolve the ambiguity. No product endorsement or standards certification is sought.
