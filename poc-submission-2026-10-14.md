# Receiver-generated scope reflection: proposed regression tests for Proof-of-Control

**From:** Terrynce A. White, independent researcher and creator of OpenLine  
**Prepared:** October 14, 2026  
**Status:** **PUBLIC draft in OpenLine Technical Notes; not submitted to LFDT. FOR OWNER APPROVAL.**  
**Reviewed specification:** [Proof-of-Control Working Draft v0.1, `93d0e28`](https://github.com/LFDT-ProofOfControl/ov-poc-standard/tree/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en). Public-comment deadline: October 30, 2026.

## Why this matters

A receiver checked a valid, owner-signed mandate and still made the wrong local authorization decision. In an unmerged OpenLine reference integration, its wrapper wrote locally required scopes into a field intended to record carried scopes, then checked whether those scopes were present **in the state it had just created**. An action granted by the owner could pass even though the owner had not granted an additional receiver-required scope. Separately, actions missing from the receiver's configured action map defaulted to an empty scope requirement. The underlying cryptographic mandate checks continued to operate; the problem was the wrapper's composition of verified owner authority with receiver policy.

A corrected, unmerged wrapper was exercised in a two-process **synthetic** fixture. I offer this bounded failure case as an auditor regression test—not as a claim of unique invention, a production exploit, or Proof-of-Control conformance.

## 1. C4.1.2, C4.1.3 and C4.1.8 — test the source of authority

The draft already requires per-action grant checks and refusal of out-of-scope requests (**C4.1.2–3**). **C4.1.8 already prohibits diagnostic/advisory outputs from elevating subsequent authorization.** My proposed addition is a *specific falsifier* for the same trust-boundary principle: **receiver-generated evaluation state must not be mistaken for scope issued by the owner.**

**Regression test:** Present a correctly signed owner grant covering action `a` but *omitting* scope `s` that the receiver's local policy requires. The receiver must refuse. Repeat with action `a` covered by the owner's grant but missing from a receiver policy that declares an explicit local action allowlist: it must fail closed. A positive test should allow `a` when both the owner grant and receiver policy authorize it. Report which verified owner-grant fields and which receiver-local rule were actually tested, rather than relying on scope state the receiver itself inserted.

This is implementable using OAuth/AuthZEN/OPA or other conventional authorization mechanisms; no OpenLine-specific format is required.

## 2. C7.1.4–5 and C8.2.2 — test the final-signature boundary

The draft already distinguishes execution evidence from admission evidence (**C7.1.4–5**), and **C8.2.2 already prohibits treating a signature over an artifact as proof of runtime behavior**. The published case study identifies a narrower composition risk: an inner gate signed an in-memory `ALLOWED` result before its wrapper performed additional receiver-local checks. No escape of that intermediate signature, unauthorized downstream acceptance, or unauthorized effect was demonstrated.

**Regression test:** Make the inner gate produce `ALLOWED` while the full receiver policy must deny. Verify that the final emitted decision is refusal, and that no downstream consumer can mistake the intermediate signature for **final** authorization. Either sign `ALLOWED` only after all required checks, or bind any provisional signed result to a non-final status/audience that cannot be accepted as final. Even a final authorization receipt is not evidence an external effect occurred without the effect-boundary evidence required by C7.1.4.

## 3. C8.1.5, C8.1.8 and C8.2.2 — verify *the property claimed*, not the mere availability of a verifier

The standard already specifies that Tier 3 can independently authenticate the evidence available **without guaranteeing a complete record of every action**. I do not propose a completeness prerequisite for all Tier 3 claims. Nor do I suggest that C8.2.2's distinction between signing-time evidence and runtime behavior is absent.

**Suggested auditor question:** Does the independent reproduction test verify the *specific stated property*—for example, canonical syntax and correct signature rejection, or a particular deployed admission/effect claim? Record the scope of observations, remaining trust assumptions, and any known completeness limits. Passing public synthetic vectors shows that the verifier works on those vectors; it cannot by itself establish that a real deployment enforced an authorization boundary or captured every effect.

## Published evidence and explicit limits

White, Terrynce A. (2026). *Receiver-Side Authorization Failures in Cross-Boundary AI Agent Systems: A Case Study of Scope Reflection, Fail-Closed Policy Checks, and Signed Decisions* (v1.0.0-rc1). Zenodo. [doi:10.5281/zenodo.23358012](https://doi.org/10.5281/zenodo.23358012).

Sources: [original receiver PR #104](https://github.com/terryncew/openline-receipt-gate/pull/104); [corrected draft PR #116](https://github.com/terryncew/openline-receipt-gate/pull/116) at pinned [`8b8612c`](https://github.com/terryncew/openline-receipt-gate/commit/8b8612c0fe9d81b521a3fb81e5063420d835ee1a); [CI run 38501462915](https://github.com/terryncew/openline-receipt-gate/actions/runs/38501462915); [frozen artifact 11865617433](https://github.com/terryncew/openline-receipt-gate/actions/runs/38501462915/artifacts/11865617433). With one owner, two worker mandates and two receiver processes **on one CI host**, the synthetic fixture passed 28 assertions and ten regression checks, produced ten verified decision receipts (three ALLOWED, seven STOPPED), and recorded three **simulated** effects. No independent receiving organization, live production effect, or superiority over security-equivalent OAuth/AuthZEN/OPA was established.

The [separate evidence-interop verifier kit](https://github.com/terryncew/openline-evidence-interop) demonstrates public verification mechanics, not production enforcement or Tier 3 conformance. See the [original October 12 requirement mapping](https://github.com/terryncew/openline-technical-notes/blob/64b5e05f96a0d49b3922cfa1272ef95b450786ed/poc-appendix.md) and [October 14 technical addendum](https://github.com/terryncew/openline-technical-notes/blob/f6d827fd443844d8e752b8f96c06f5e4c0008dca/poc-evidence-addendum-2026-10-14.md).

**Requested disposition:** If the current clauses already compel these exact outcomes, please point to the applicable auditor checks. Otherwise I recommend adding the three negative tests above as illustrative test guidance; I do not request endorsement of OpenLine.

**Contribution-license boundary (for owner approval before submission):** LFDT's [CONTRIBUTING.md](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/CONTRIBUTING.md) states that contributions to the specification are under Apache-2.0. External submission of **this comment** should be explicitly approved with that licensing understanding. The already published Zenodo report retains its separate CC BY 4.0 / MIT / Apache-2.0 / evidence-rights treatment; linking to it does not relicense it.
