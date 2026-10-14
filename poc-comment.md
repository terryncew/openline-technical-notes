# OpenLine evidence against the Proof of Control working draft

**Author:** Terrynce White, independent researcher; creator of OpenLine.
**Date:** 2026-10-12. **For:** Proof-of-Control public comment (window closes October 30, 2026).
**Pinned commit:** `93d0e28` — `github.com/aai-society/ov-poc-standard`, verified as repo HEAD on 2026-10-12.
**Status:** Draft — one independent researcher's annotated reading. Not a conformance claim. Technical appendix with the full requirement-by-requirement mapping: `poc-appendix.md`.

## What I mapped, and how

I took the public Proof-of-Control draft (`aai-society/ov-poc-standard` @ `93d0e28`) and checked OpenLine's public evidence against chapters C4 (Authorization), C5 (Identity), C7 (Evidence Generation & Properties), and C8 (Verifiability Tiers) — requirement by requirement, using only public sources. Every requirement gets one of four statuses: DEMONSTRATED, PARTIALLY DEMONSTRATED, NOT TESTED, or UNSUPPORTED. Unfavorable results are preserved; they are the point.

One structural distinction runs through the whole mapping, and I want to state it up front because it is easy to blur: **the evidence-interop kit is not the OpenLine system.** The kit (`terryncew/openline-evidence-interop`) is a frozen, public specification plus a standalone verifier — it demonstrates *receipt verification mechanics* (I cloned it and ran `check_vectors.py` on 2026-10-12: 29 cases, 33 verdicts, PASS, no credentials of any kind). The system (wallet + receipt gate, both public repos) demonstrates *enforcement behavior* — the gate deciding allow/refuse/revoke — in the author's recorded runs. A kit-level property never implies a system-level property, and no independent reproduction of the enforcement claims has occurred yet (open invitations: `openline-receipt-gate#109`, `openline-bureau#5`).

## Key findings

**Where the evidence lands.** The kit genuinely demonstrates openly reproducible verification mechanics: frozen spec, versioned tooling, offline checking with no operator involvement — directly responsive to 8.1.8. The receipt schema is honestly bounded (NOT_PROOF.md: VALID authenticates a statement under a supplied policy; it does not prove the action happened), which is what 7.5 asks for. On the system side, the public repos document real enforcement behavior with unusual honesty about limits: the wallet README states plainly that "revocation is a race against consequence" and that late revocation is recorded as late, not rewritten as prevention.

**Where it falls short — and this is most of C7.** No before/during/after evidence triple (7.1.2). No hash-chained evidence log (7.3.1) — evidence records are per-record Ed25519-signed; the hash-linked structure that exists is the *revocation timeline*, a different artifact. No external timestamp anchoring (7.2.2), no equivocation resistance (7.3.3), no inclusion or consistency proofs (7.3.4/7.3.5), no evidence-custody story at all (7.6.x). No delegation chains (4.2.x), no path-aware evaluation (4.1.7/4.1.8), no per-task credentials (4.1.5). No-bypass-path for the interception gateway is untested (7.1.1), and effect-channel mediation is explicitly the deployer's responsibility (receipt-gate README) — so per 7.1.5's own rule, the public evidence reads as *decision* evidence, not executed-action evidence.

**The tier question, answered by the draft itself — as I read it.** I make no Tier 3 implication anywhere in this comment, and the draft tells me why I shouldn't: 8.1.3's own examples place "a system signing its own logs or outputs with its own key" at Tier 2. OpenLine's demonstrated receipts are signed by the deploying receiver's key, and no key-custody separation (7.3.2) is demonstrated. Under the draft's own rules, that is Tier 2 evidence with openly reproducible verification mechanics — a precise, unflattering, and I think useful classification. That Tier 2 reading is my interpretation of the current draft, not an official conformance determination; only the working group's conformance process can make that call.

## What I think is useful to the working group

1. **A worked example with the failures left in.** The appendix lists 20+ requirements OpenLine does not meet, each with the exact reason. That failure list discriminates between mechanism families — it shows which requirements bite against a signed-receipt design — and that is useful input regardless of what anyone thinks of OpenLine.
2. **A citable published-tooling artifact.** The frozen interop kit is directly responsive to 8.1.8 (verification tooling "usable without an NDA, license negotiation, or operator-issued credentials"). `python check_vectors.py` runs in under a minute.
3. **A trust-assumption disclosure written the way C10.2 wants them written.** Every party, assumption, and boundary is named, including "lab-scale, no production deployments, no external adoptions, no independent reproduction yet."

## What this comment is not

Not a conformance claim (none is made, per 8.1.4). Not a product pitch (there is no product). Not a request for endorsement. Not a suggestion that the working group needs OpenLine. It is one researcher's annotated reading of the draft against running code, submitted before the deadline.

---

## Proposed first message to Ken Huang (Terrynce's version — for approval, DO NOT SEND)

Ken, I've been reading the Proof of Control draft and put together a short implementation note comparing OpenLine's signed receipts with the evidence requirements. A few things line up, and quite a few don't. I thought it might be useful for the comment period. Mind if I send it?

---

## Submission record

- **Submitted:** October 14, 2026 (12:42 PDT) as [LFDT-ProofOfControl/ov-poc-standard issue #122](https://github.com/LFDT-ProofOfControl/ov-poc-standard/issues/122) — "Public comment: receiver-generated scope reflection — proposed regression tests (C4.1.8, C7.1.4-5, C8.2.2)"
- **Status:** Open, publicly visible. Contributed under Apache-2.0 per the working group's contribution rules.
- **Content:** Three proposed auditor regression tests (receiver-generated scope reflection; final-signature boundary; verify-the-property-claimed), with the demonstrated failure case, explicit limits, and citations to the published report (doi:10.5281/zenodo.23358012), PR #104, draft PR #116, CI run 38501462915, and the October 14 technical addendum.
- Prior related submissions by the same author: issues #57, #63, #84 (unchanged).
