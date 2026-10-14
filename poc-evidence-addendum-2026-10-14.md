# Technical addendum for proposed Proof-of-Control comment — 2026-10-14

**Status:** Prepared for owner approval. Not submitted externally.

## What changed since the original October 12 mapping

The original [comment](poc-comment.md) and [detailed requirement appendix](poc-appendix.md) were pinned to `AAI-Society/ov-poc-standard` commit `93d0e28`. The source repository has moved to [LFDT-ProofOfControl/ov-poc-standard](https://github.com/LFDT-ProofOfControl/ov-poc-standard). The new repository's `master` on October 14 points to the *same full commit* `93d0e282fc177d5df741de4cdd6a0d0dacf1955a`, so the old mapping did not silently become a mapping of a new revision.

The organizer's website calls this the **v1.0 working draft public-comment period**, while the versioned GitHub requirement directory is `0.1/en/`. Cite the specific pinned GitHub text, not an already ratified v1.0.

Official channels: [comment-enabled Google Doc](https://docs.google.com/document/d/1EiiGDwLXvMxoSHp3Ru56AhR2u9gNd-6Fjs_CKZ4kU-w/edit?tab=t.0#heading=h.5cwdygy69mua) or a GitHub issue in the [LFDT repository](https://github.com/LFDT-ProofOfControl/ov-poc-standard). GitHub's contributor instructions prefer a public issue *before* a normative change PR. Public-comment deadline: **October 30, 2026**.

**Research publication status:** The owner reports that the DOI resolves and the record is published, but this review session could not independently fetch the Zenodo record or DOI resolver; this is an access limitation, **not evidence of invalidity**. Citation reflects the owner-confirmed publication metadata.

**New published research:** [OpenLine Technical Report 001, version DOI 10.5281/zenodo.23358012](https://doi.org/10.5281/zenodo.23358012), released October 13, 2026 as **1.0.0-rc1**. It was unpublished when the original October 12 mapping was prepared. Preserve that historical mapping; use the publication DOI in new citations.

| Current PoC clause | Standard's existing concern | Proposed extra discriminator |
| --- | --- | --- |
| [C4.1.2–3 and C4.1.8](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C04-Authorization.md) | Evaluate actions within the grant and block scope escalation; 4.1.8 already prohibits lower-trust diagnostic/advisory outputs from raising authority | Add a negative regression test for verifier-/receiver-authored scope state satisfying its own policy check; deny local actions not configured where an explicit allowlist is used |
| [C7.1.4–5](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C07-Evidence-Generation-and-Properties.md) | Mediation and executed-action evidence | Test inner signed allowance rejected by final local policy; intermediary decision cannot be used downstream as final admission |
| [C8.1.5 / C8.1.8 / C8.2.2](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/0.1/en/0x10-C08-Verifiability-Tiers.md) | 8.2.2 already distinguishes signing-time artifact integrity from runtime behavior; 8.1.5/8.1.8 address independent checking | Audit the **precise claimed property** with explicit coverage limits: public test vectors are not evidence that any real deployment produced the claimed events. Tier 3 does **not** imply record completeness |

### Boundaries retained

- The known failure was a *wrapper policy-composition defect*. The cryptographic owner-mandate checks still operated.
- Two differently configured receiver processes on a single CI host are not two independent organizations.
- The intermediate signed `ALLOWED` result was not shown to escape its wrapper or to authorize a prohibited effect. It is an ordering/design concern, not a demonstrated exploit.
- Three effect rows are **simulated**, not payment records or real external effects.
- A public offline verifier that passes synthetic vectors demonstrates its defined test mechanics, not Tier 3 evidence of a production deployment. The current Proof-of-Control text explicitly says **Tier 3 need not be a complete record**; ask reviewers to test only the stated claim and disclose what was not captured, not to demand exhaustive histories for every Tier 3 claim.
- OpenLine did not run a matched, security-equivalent OAuth/AuthZEN/OPA comparison. The standards can support comparable authorization and signed responses.
- No new work on Home Guard or RECEIVER-002 is proposed; there are no new experiments or commercial claims.
- No standards group or named researcher has been contacted as part of this preparation.

**Visibility:** This technical-notes repository and draft PR are public, although they have not been submitted to the LFDT working group.

**Contribution license:** LFDT's [contribution terms](https://github.com/LFDT-ProofOfControl/ov-poc-standard/blob/93d0e282fc177d5df741de4cdd6a0d0dacf1955a/CONTRIBUTING.md) apply Apache-2.0 to material contributed to that project. The owner must explicitly approve both the external submission and licensing **of that submitted comment** under Apache-2.0. Neither action changes the independent Zenodo report's existing mixed rights.

**Approval gate:** The draft in [poc-submission-2026-10-14.md](poc-submission-2026-10-14.md) is an outward-facing standards comment. Do not submit it until the owner explicitly approves.
