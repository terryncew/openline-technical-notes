# Technical addendum for proposed Proof-of-Control comment — 2026-10-14

**Status:** Prepared for owner approval. Not submitted externally.

## What changed since the original October 12 mapping

The original [comment](poc-comment.md) and [detailed requirement appendix](poc-appendix.md) were pinned to `AAI-Society/ov-poc-standard` commit `93d0e28`. The source repository has moved to [LFDT-ProofOfControl/ov-poc-standard](https://github.com/LFDT-ProofOfControl/ov-poc-standard). The new repository's `master` on October 14 points to the *same full commit* `93d0e282fc177d5df741de4cdd6a0d0dacf1955a`, so the old mapping did not silently become a mapping of a new revision.

The organizer's website calls this the **v1.0 working draft public-comment period**, while the versioned GitHub requirement directory is `0.1/en/`. Cite the specific pinned GitHub text, not an already ratified v1.0.

Official channels: [comment-enabled Google Doc](https://docs.google.com/document/d/1EiiGDwLXvMxoSHp3Ru56AhR2u9gNd-6Fjs_CKZ4kU-w/edit?tab=t.0#heading=h.5cwdygy69mua) or a GitHub issue in the [LFDT repository](https://github.com/LFDT-ProofOfControl/ov-poc-standard). GitHub's contributor instructions prefer a public issue *before* a normative change PR. Public-comment deadline: **October 30, 2026**.

**New published research:** [OpenLine Technical Report 001, version DOI 10.5281/zenodo.23358012](https://doi.org/10.5281/zenodo.23358012), released October 13, 2026 as **1.0.0-rc1**. It was unpublished when the original October 12 mapping was prepared. Preserve that historical mapping; use the publication DOI in new citations.

| Current PoC clause | Standard's existing concern | Proposed extra discriminator |
| --- | --- | --- |
| [C4.1.2–3](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C04-Authorization.md) | Test action within granted scope / reject out-of-scope | Reject valid owner mandate missing separately configured receiver-local scope; fail closed on unconfigured action |
| [C7.1.4–5](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C07-Evidence-Generation-and-Properties.md) | Mediation and executed-action evidence | Test inner signed allowance rejected by final local policy; intermediary decision cannot be used downstream as final admission |
| [C8.1.5 / C8.1.8](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C08-Verifiability-Tiers.md) | Outside verification of Tier 3+ claims | Do not confuse independent validation of test vectors with full independent verification of deployed effects, custody and completeness |

### Boundaries retained

- The known failure was a *wrapper policy-composition defect*. The cryptographic owner-mandate checks still operated.
- Two differently configured receiver processes on a single CI host are not two independent organizations.
- The intermediate signed `ALLOWED` result was not shown to escape its wrapper or to authorize a prohibited effect. It is an ordering/design concern, not a demonstrated exploit.
- Three effect rows are **simulated**, not payment records or real external effects.
- A public offline verifier that passes synthetic vectors proves its own defined mechanics, not Tier 3 evidence of a production deployment.
- OpenLine did not run a matched, security-equivalent OAuth/AuthZEN/OPA comparison. The standards can support comparable authorization and signed responses.
- No new work on Home Guard or RECEIVER-002 is proposed; there are no new experiments or commercial claims.
- No standards group or named researcher has been contacted as part of this preparation.

**Approval gate:** The draft in [poc-submission-2026-10-14.md](poc-submission-2026-10-14.md) is an outward-facing standards comment. Do not submit it until the owner explicitly approves.
