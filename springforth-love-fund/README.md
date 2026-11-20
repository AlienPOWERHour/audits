# Springforth / Love.fund Pilot: Independent Review

A case study of three read-only review cycles on the public Springforth ops export and source, posted in the Gitcoin forum thread [Love.fund pilot: a commitment-pooling experiment](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438).

Reviewer: [AlienPOWER@Hour](https://gov.gitcoin.co/u/AlienPOWERHour) (`@AlienPOWERHour`). Thread author and Springforth developer: TerexitariusStomp (display name Terexitarious). Dates below are in America/Denver. Quoted text is verbatim from the current public version of each post. This write-up summarizes the record; it does not replace the posts themselves.

| | |
| --- | --- |
| Thread | [gov.gitcoin.co/t/25438](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438) |
| Source repository | [TerexitariusStomp/springforth](https://github.com/TerexitariusStomp/springforth) |
| Reviewer's posts | [#32](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/32), [#35](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/35), [#37](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/37), [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38), [#41](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/41), [#45](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/45), [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48), [#51](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/51), [#55](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/55), [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56) |
| Author responses | [#33](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/33), [#36](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/36), [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39), [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40), [#42](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/42), [#46](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/46), [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49), [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53), [#57](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/57), [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58) |
| Period | Tuesday, November 3, 2026, 8:13 PM through Thursday, November 19, 2026, 7:40 PM, America/Denver |

## Summary

On November 3, 2026 I offered a read-only review of Springforth's public export and source. Between November 3 and November 19 I posted three review cycles. In each cycle TerexitariusStomp confirmed findings, shipped fixes, and credited the review.

The work stayed on public records: the live and dated ops export, the export specification, and cited source revisions. It used fabricated or isolated tests where a live failure had not been observed. It did not request credentials, wallet keys, or access to funds, and it did not attempt to deanonymize participants.

This is a record of what was reviewed, what was confirmed, what shipped, and what was left open. It is not a certification of the production service or of the Mars College round.

## Scope and limits

These limits were stated during the review and still apply to this write-up:

- The export under review contained sample / hackathon data. As the author put it in [#46](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/46), "this reads as verification of mechanism and fixes, not pilot results." The checks do not establish results for the Mars round.
- The review was read-only. Nothing was posted to or changed on the author's service by the reviewer.
- No production certification was claimed. Local tests in later passes used SQLite rather than a deployed Cloudflare worker. Production salt configuration, the exact deployed revision, and full database coverage were not all verified at every pass.
- In the third pass, the live export returned HTTP 403, so hashes, counts, and the then-current deployment were not revalidated from the live file.
- The no-deanonymization boundary was accepted from the start and restated after the first findings. Participants were not identified, and pseudonyms were not joined to outside information.
- Cycle 1 did not establish a live vulnerability, an actual disclosure, or a failure of the existing verifier. Several early items were source observations or synthetic tests pending confirmation of intended behavior and of what was deployed.
- The full upstream test suite and the charter were outside the second pass. The third pass did not rerun a complete concurrency suite or certify every writer path.
- This write-up does not itself re-verify commit `943385f` against a live export. The deployment claim for that commit is the author's, in [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58).

## Cycle 1: preliminary review

**Reviewed.** Saved development-branch source and isolated tests with fabricated data, posted in [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) on Thursday, November 5, 2026, 2:56 AM America/Denver (current public text last edited Friday, November 6, 2026, 8:39 PM). The first post did not cite a live sequence number. The author then ran the five observations against the live export.

**Live cut used for confirmation** ([#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39), Saturday, November 7, 2026, 11:24 PM): seq 9272, head `7df5d525…`, 707 records, "both anchors recompute."

**Shipped cut** ([#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40), Sunday, November 8, 2026, 12:16 AM): seq 9279, head `eb99b5e2…`, export built 07:00:43Z, 708 records.

### Findings

1. **Evidence links in public record paths.** In the reviewed source, an evidence link could appear in an export identifier and in a ledger event payload, and the ledger reader returned that payload. This was framed as a privacy question, not as proof that a sensitive link had been published. The author confirmed a broader live issue: payloads used `refHash` / `textHash` as specified, but record IDs embedded plaintext names across record types, so the identifier column undid actor pseudonyms. Harmless in sample data; a problem in a Mars-sized cohort.
2. **Claims without evidence.** Claims without associated evidence or attestations might have no standalone row, so a reviewer might not be able to distinguish an unsupported claim from a claim outside the export's coverage. The author confirmed the sharp form: the one `via: attestation` edge targeted `claim-poss-mida-creek-mangrove`, which had no row in the file.
3. **Snapshot-write failure.** A synthetic test showed that failure to save the dated export could be ignored while processing continued. This had not been observed on the live service. The author accepted it as hardening: record the failure as an append-only event.
4. **Cache behavior.** With a fabricated unchanged ledger sequence but a changed ledger hash, the reviewed code returned the cached export. This was a defensive case, not evidence that normal append-only operation would reach it. The author accepted it as hardening: the cache key should include the head hash, because keying on sequence alone assumes what the two-hash check exists not to assume.
5. **Pseudonym configuration.** Pseudonyms were reproducible when using the documented public fallback salt. It was not known whether that configuration was used in production, and no attempt was made to identify participants. The author first marked this as a deployed-config check still owed.

[#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) also asked how to retrieve a same-cut ledger verification reference when replaying an older export. In [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39) the author confirmed a contract gap: `/ledger/verify` ignored `seq` (`?seq=100` and `?seq=9272` returned identical heads).

A correction was also recorded in [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38): replacing a dated export with a later build from the same day was permitted by the reviewed specification and was not treated as a defect. The recommendation was still to pin the exact reviewed bytes and record their digest, sequence, `generatedAt`, and ledger head.

### Author confirmation

From [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39):

> Ran the five observations against the live export at this cut (seq 9272, head `7df5d525…`, 707 records — both anchors recompute).

> **Net:** two confirmed fixes (#1, #2), one contract gap (`?seq=`), two hardening items, one config check owed back here.

### What shipped

In [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40) the author reported that the findings were shipped and recomputable:

- **Identifiers.** Every record `id` and id-shaped join field then shipped as `x_<salted-hash>`. The name-in-identifier leak was reported gone from all 692 records.
- **Claims without evidence.** A new `claim` record type gave every claim a row, evidence or none. The previously missing target resolved to claim row `x_6e70a4a75fc0edcc`.
- **Snapshot and cache hardening.** A failed dated-snapshot write appends an `ops-export.snapshot_failed` ledger event; cron refresh failures get `ops-export.refresh_failed`. The rebuild gate compares sequence *and* head.
- **Salt.** The suspicion was confirmed: production was running on the documented public fallback, and no `EXPORT_SALT` was configured. The worker then minted a random salt once and persisted it server-side. `a_` / `x_` values rotated at that cut, with zero overlap with the previous file, described as an epoch boundary rather than an inconsistency.
- **Same-cut lookup.** `/ledger/verify?seq=N` then returned the head at exactly that cut (for example `?seq=9279` → `eb99b5e2…`; out-of-range requests 404).

> This was a good review cycle: two findings were live-confirmable from the export itself, one was a config truth I owed you, and `?seq=` came straight from your clarification list.

### Left open after cycle 1

- Earlier dated files kept the old format; history was not rewritten, and only that day's file carried the new schema.
- The remaining ledger-payload path for raw evidence URLs was not closed in this cycle. [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39) found no URIs in the live payloads at that cut, but the source-level ledger path raised in [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) was later shown still present in cycle 2.
- Snapshot failure recording had landed, but later passes found residual recovery cases.
- `?seq=N` closed the same-cut lookup gap; it was not yet a chain-recomputing verification of that prefix.
- The assessment was still preliminary and was run against sample data.

In [#41](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/41) I thanked the author and offered this kind of work more broadly, as a human-directed service supported by tools and AI collaborators, not as a finished audit platform. In [#42](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/42) the author replied: "Thanks for your help. Much appreciated."

## Cycle 2: post-fix rerun

**Reviewed cut**, from [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48), Saturday, November 14, 2026, 10:50 PM America/Denver (current public text last edited 11:08 PM that night):

| Field | Value |
| --- | --- |
| seq | 10494 |
| generatedAt | 2026-11-15T04:49:52.728Z (Friday, November 14, 2026, 9:49:52 PM America/Denver) |
| ledger_head | `5f2c9727b64f7be55690375e12f770682777ff3f47942c50bd2711d90e7bc867` |
| Source | `mars-fix` at commit [`d0f6bf3`](https://github.com/TerexitariusStomp/springforth/tree/d0f6bf30f4101cf809c5d3ff3738108264dbc279) (`d0f6bf30f4101cf809c5d3ff3738108264dbc279`) |
| Export | Latest and dated copy (`2026-11-15.ndjson`) were byte-identical: 764 data records, then the manifest |
| SHA-256 before the manifest (including LF endings) | `803396d9c9c8db885cf6ea18a5e13cbed225676933810bdb9f36c959ebace570` |
| SHA-256 of the complete file | `7c967160e7a0ff06a466ef8b4da210258db99c6567aa557d17633efeace35936` |

The author had pointed this pass at the post-fix export in [#46](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/46): "your review cycle shipped Nov 8, so the export today is post-fix (#40)." Sequence lookups for 100, 9279, and 10494 returned the expected cuts; sequence 999999999 returned HTTP 404. The 9279 reply matched the `eb99b5e2` prefix from [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40).

### Before-and-after on the original five, plus new items

1. **Identifiers.** The captured export passed format checks for 1,527 selected opaque identifier fields and 128 actor fields. The targeted identifier exposure was fixed in that file. The separate artifact handler in the reviewed source still wrote the original evidence URL into a ledger payload.
2. **Missing claims.** The previously broken attestation relationship joined to a claim row. A local claim with no artifacts also produced its own row.
3. **Snapshot failures.** A failed dated write now recorded `ops-export.snapshot_failed`, and the next refresh recovered after storage returned. A remaining local failure case required both the dated write and its failure-event write to fail. After recovery, an unchanged head left the missing dated copy unretried because refresh returned the cached latest.
4. **Cache invalidation.** The cache now checked both sequence and head hash. The same-sequence / changed-hash regression test rebuilt the export as expected.
5. **Salt persistence.** Existing saved salts and configured overrides were reused correctly in local tests. Concurrent first-time initialization still produced different salts for the two callers, with only one persisted winner. That needed an atomic initialization step that returned the same saved value to both callers.

**Additional finding: snapshot consistency.** A local test inserted a claim and its ledger event between the export's head read and entity scan. The claim appeared in the export with the earlier sequence label. The stable-database control passed. The question asked was what ensures those reads describe one consistent ledger cut during concurrent writes.

[#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) also noted that `ledgerHeadAt` retrieves the stored hash, while full chain validation is handled separately by `ledgerVerify`, and asked the author to confirm the deployed commit and whether raw artifact URLs were intended to be public. A stale test still expected identifier `ap-1` after the builder had switched to opaque IDs, and the specification wording needed reconciling.

### Author confirmation and what shipped

[#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49), Sunday, November 15, 2026, 9:16 PM America/Denver, was a formal reply and an @mention:

> Thanks @AlienPOWERHour  for the second pass — all five open items from #48 are now fixed and deployed, and the two questions get direct answers below.

What the author said had shipped:

- **Raw artifact URIs in ledger payloads.** `claim.artifact` events then commit `uriHash` (SHA-256 of the URL) instead of the URI. Artifact URLs are meant to be public on the claim API, but they then live only in the mutable claim entity; the immutable ledger carries hashes.
- **Snapshot TOCTOU.** The head query and all entity scans then run in a single D1 `batch`, so an export cannot ship records under a sequence label that never coexisted with them.
- **Entity/event tearing** (described by the author as the underlying cause of earlier `rev-` events without claims). New `ledgerAppendWith` writes entity rows and their ledger event in one atomic batch, retrying on seq-PK collisions, applied to `claim.built`, `claim.attested`, and `claim.artifact`.
- **Salt-init race.** `exportSalt` became an atomic insert-if-absent with read-back, so concurrent first callers converge on the single persisted salt.
- **Dated-copy gap.** The fast path then self-heals a missing `day-*` file, with failure markers deduped on `file_sha256`.
- **Deployed commit.** The manifest then carries `build` (stamped at deploy from `BUILD_ID`) and `revision: "v1.3"` alongside the v1 wire `spec`. Current deploy stated in [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49): version `02a0357e`, `build: "18a2ff9+dirty"`, a working-tree snapshot over `18a2ff9`.

### Left open after cycle 2

[#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) itself limited the pass: the local adapter used SQLite rather than a deployed Cloudflare worker; production salt configuration, the exact deployed revision, and full database coverage remained unverified at the time of that post; and the review covered the sample-data mechanism, not anonymity, pilot outcomes, or an overall production certification.

In [#51](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/51), Monday, November 16, 2026, 1:42 AM, I reported that the current export passed the integrity checks we ran, and asked for the exact source for deployed build `18a2ff9+dirty` before taking a closer look at the concurrency and recovery fixes.

[#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53), Tuesday, November 17, 2026, 1:13 AM, answered that request and @mentioned @AlienPOWERHour. The author stated the deployed revision as commit [`949e706`](https://github.com/TerexitariusStomp/springforth/commit/949e70689855fe406dec5ba264028251e47476d1) (`feat/trustgraph-epochs`), noted that the cached snapshot could still show the prior deploy's build stamp until the ledger head moved, and wrote: "Your five findings — all fixed in current source." The five items listed there were the salt race, snapshot consistency, the dated-write recovery hole, cache invalidation, and the stale test / specification wording (reconciled through export v1.4).

[#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) also described a larger CLC integration and said "Attribution loop closed." The author disclosed remaining gaps of his own: `CLC_SERVICE_TOKEN` pending from GE, holdings sync and `/admin/clc/pool` deploys coded but auth-gated upstream, `trackingId`-only tracks staying submitted until that landed, and `pool_exchange` projections treated as observations with the chain authoritative. Those author-disclosed gaps were not the subject of the next pass, except for the attribution wording.

## Cycle 3: review of the #53 source claims

**Reviewed revision**, from [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56), Thursday, November 19, 2026, 3:54 AM America/Denver: [`949e706`](https://github.com/TerexitariusStomp/springforth/commit/949e70689855fe406dec5ba264028251e47476d1) (`949e70689855fe406dec5ba264028251e47476d1`), still the head of the cited `feat/trustgraph-epochs` branch at the time of the review.

The live export returned HTTP 403 during that pass, so the current deployment was not certified from the live file.

### Findings

1. **Snapshot recovery still had a hole.** If the dated snapshot write failed, the failure marker saved, and the failure-event write also failed, the ledger head stayed unchanged. On the next attempt, that marker prevented another snapshot write even after storage recovered. The case was reproduced with the actual recovery functions and isolated, simulated storage failures: two control cases passed; this recovery case failed. This concerned missing audit snapshots, not demonstrated theft or fund loss.
2. **The documented "verification" command did not verify the chain.** Documentation recommended `/ledger/verify?seq=N` as a chain-integrity check. That route looked up the stored hash at that sequence; it did not recompute the chain. The route without `seq` did perform verification. Matching two stored hashes is useful consistency evidence, but it is not the verification the instructions promised.
3. **"Attribution loop closed" was too strong.** Confirming and decoding a swap establishes that the transaction happened. It does not establish that the person receiving attribution authorized it. The author's own verification document already acknowledged that actor-to-wallet binding was still needed. That limitation should appear beside the claim, not only in the supporting document.

Credit was also given in [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56) for substantive source changes to salt initialization, batched snapshot reads, and cache checks.

### Author confirmation and what shipped

[#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58), Thursday, November 19, 2026, 7:40 PM America/Denver, was a follow-up that acted on the three findings. It was not a formal `reply_to` and did not @mention AlienPOWERHour.

> Thanks for the careful pass — all three findings were real, and two were mine to own. Fixed in [943385f](https://github.com/TerexitariusStomp/springforth/commit/943385f03a3a1b3fef80416a0693775904c90dd3) (deployed):

1. **Snapshot recovery hole: fixed.** The author agreed that `stampDatedSnapshot` returned early on a matching `file_sha256` marker, so marker-success plus event-failure left the dated file permanently missing. The marker now only dedupes the `snapshot_failed` event and sets `nextAttemptAt`; the data write retries on a bounded 10-minute backoff, and identical bytes heal with a `snapshot_recovered` event. The author reproduced the exact sequence in `test/opsexport-store.test.ts` (real SQLite, production schema).
2. **`?seq=N` was a lookup.** `?seq=N&verify=1` now runs `ledgerVerifyPrefix`, which recomputes every event hash from genesis through that cut and compares the tip to the stored head. Plain `?seq=N` keeps the cheap head lookup but now returns `verified: false` plus a note pointing at the real check. `docs/VERIFICATION.md` was rewritten to distinguish the two. A corrupt-link test is in the suite: the stored head still resolves while prefix verify reports `brokenAt`.
3. **Attribution claim: fair.** `confirmed` proves the transaction mined and what it did; it does not prove the named actor initiated it. The docs now say that beside the claim (CLC-INTEGRATION attribution row, OPS-EXPORT `clc_track` row, and the VERIFICATION table). Strong actor attribution needs the wallet binding plus a signed intent, stated wherever the claim appears rather than only in a limitations section.

> Credit where due: your repro harness was the right call — the store-level tests now run the actual recovery functions against real SQL instead of mocks.

### Left open after cycle 3

- The third pass could not certify the live deployment, because the live export returned HTTP 403. The claim that `943385f` was deployed is the author's in [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58). This write-up has not independently revalidated a live export built from that commit.
- The review still does not certify production as a whole, anonymity, or Mars-round outcomes.
- Author-disclosed CLC gaps from [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) (`CLC_SERVICE_TOKEN` pending, some admin and holdings paths auth-gated upstream, `trackingId`-only tracks, `pool_exchange` as observations) were not closed by this review cycle. The attribution wording was corrected; the underlying wallet-binding requirement remains an honest boundary, now stated beside the claim.

## Findings and status

| Finding | Reported | Confirmation | What shipped | Status |
| --- | --- | --- | --- | --- |
| Plaintext names in record IDs / opaque identifiers | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38); confirmed wider in [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39) | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39): "Confirmed live, and wider than reported." | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): `x_<salted-hash>` IDs at seq 9279 | Fixed in cycle 1; re-verified in the seq 10494 export ([#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48)) |
| Raw evidence URLs in ledger payloads | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38); still present in source at [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) listed it as an open item and addressed it | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) / [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53): `uriHash` instead of the raw URI | Fixed in cycle 2, per the author |
| Claims without a row | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39): "Confirmed in the sharpest form." | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): new `claim` record type; target resolved to `x_6e70a4a75fc0edcc` | Fixed in cycle 1; re-verified in [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) |
| Silent dated-snapshot failure | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39): accepted as hardening | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): `ops-export.snapshot_failed` and `ops-export.refresh_failed` | Initial hardening in cycle 1; residual recovery cases found later |
| Dated-copy gap after two-write failure | [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49): fast path self-heals a missing `day-*` file; markers deduped on `file_sha256` | Partially addressed in cycle 2; a remaining hole was found in cycle 3 |
| Residual snapshot-recovery hole (marker blocks retry after event-write failure) | [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56) at `949e706` | [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58): "all three findings were real" | [`943385f`](https://github.com/TerexitariusStomp/springforth/commit/943385f03a3a1b3fef80416a0693775904c90dd3): marker only dedupes the event; data write retries on bounded backoff | Fixed in `943385f`, per the author |
| Cache keyed on sequence only | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39): accepted as hardening | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): rebuild gate compares sequence and head | Fixed in cycle 1; re-verified in [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) and [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) |
| Public fallback salt in production | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) (configuration unknown at the time) | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): "your suspicion confirmed" | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40): persisted random salt; `a_` / `x_` rotation at that cut | Fixed in cycle 1, per the author |
| Concurrent first-time salt-initialization race | [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49): atomic insert-if-absent with read-back; [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53): `putIfAbsent` | Fixed in cycle 2, per the author |
| Snapshot consistency (TOCTOU between head read and entity scan) | [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49): single D1 `batch`; [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53): `snapshotReads` plus `ledgerAppendWith` | Fixed in cycle 2, per the author |
| Entity/event tearing | Described by the author in [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) as the underlying cause of earlier `rev-` events without claims | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49): `ledgerAppendWith` | Fixed in cycle 2, per the author |
| Same-cut `?seq=` lookup versus chain verification | Clarification requested in [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38); lookup gap in [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39); lookup-versus-verify in [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) and [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56) | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39): contract gap; [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58): "you were right, it was a lookup" | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40) added `?seq=N` head lookup; [`943385f`](https://github.com/TerexitariusStomp/springforth/commit/943385f03a3a1b3fef80416a0693775904c90dd3) added `?seq=N&verify=1` prefix verification | Head lookup added in cycle 1; real prefix verification added in `943385f`, per the author |
| Attribution claim broader than the evidence | [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56), in response to "Attribution loop closed" in [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) | [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58): "fair" | [`943385f`](https://github.com/TerexitariusStomp/springforth/commit/943385f03a3a1b3fef80416a0693775904c90dd3): the limitation is stated beside the claim | Documentation / claim-scope corrected in `943385f`, per the author. Wallet binding plus signed intent remains the stated requirement for strong actor attribution. |
| Stale identifier test / specification wording | [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) | [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53): test asserts opaque `oid('ap-1')`; spec wording reconciled through export v1.4 | Fixed in source as of [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53), per the author |

## Key acknowledgments

Verbatim excerpts, with links to the full posts:

> This was a good review cycle: two findings were live-confirmable from the export itself, one was a config truth I owed you, and `?seq=` came straight from your clarification list.

From TerexitariusStomp, [post #40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40)

> Thanks for your help. Much appreciated.

From TerexitariusStomp, [post #42](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/42)

> The standard you're holding is the right one — and there's a concrete revision to test against: your review cycle shipped Nov 8, so the export today is post-fix (#40).

From TerexitariusStomp, [post #46](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/46)

> Thanks @AlienPOWERHour  for the second pass — all five open items from #48 are now fixed and deployed, and the two questions get direct answers below.

From TerexitariusStomp, [post #49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49)

> Thanks @AlienPOWERHour  — answers to your questions, then where the pilot stands now.

> Your five findings — all fixed in current source:

From TerexitariusStomp, [post #53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53)

> Thanks for the careful pass — all three findings were real, and two were mine to own. Fixed in 943385f (deployed):

> Credit where due: your repro harness was the right call — the store-level tests now run the actual recovery functions against real SQL instead of mocks.

From TerexitariusStomp, [post #58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58)

Related confirmation in [post #39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39), which was not a formal reply and did not @mention AlienPOWERHour:

> Ran the five observations against the live export at this cut

> **Net:** two confirmed fixes (#1, #2), one contract gap (`?seq=`), two hardening items, one config check owed back here.

## Timeline (America/Denver)

| When (America/Denver) | Post | What happened |
| --- | --- | --- |
| Tue, Nov 3, 2026, 8:13 PM | [#32](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/32) | Introduction and offer of a read-only review |
| Tue, Nov 3, 2026, 8:41 PM | [#33](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/33) | Formal reply: welcome, and the live export as the starting point |
| Wed, Nov 4, 2026, 7:34 AM | [#35](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/35) | Review underway (edited 7:35 AM) |
| Wed, Nov 4, 2026, 10:55 AM | [#36](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/36) | Follow-up answering the questions from #32 |
| Wed, Nov 4, 2026, 11:54 PM | [#37](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/37) | Findings already appearing (edited Thu, Nov 5, 12:34 AM) |
| Thu, Nov 5, 2026, 2:56 AM | [#38](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/38) | First preliminary review: five observations (edited Fri, Nov 6, 8:39 PM) |
| Sat, Nov 7, 2026, 11:24 PM | [#39](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/39) | Author ran the five observations against the live export (seq 9272, head `7df5d525…`) |
| Sun, Nov 8, 2026, 12:16 AM | [#40](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/40) | Cycle 1 findings shipped (seq 9279, head `eb99b5e2…`); public fallback salt confirmed; `?seq=` added |
| Sun, Nov 8, 2026, 3:31 AM | [#41](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/41) | Thanks, and an offer to do more of this work (edited 3:44 AM) |
| Sun, Nov 8, 2026, 10:01 AM | [#42](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/42) | Formal reply: "Thanks for your help. Much appreciated." |
| Thu, Nov 12, 2026, 4:31 PM | [#45](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/45) | Status update before the second pass (edited Sat, Nov 14, 9:41 PM) |
| Fri, Nov 13, 2026, 10:49 AM | [#46](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/46) | Formal reply pointing at the post-fix export |
| Sat, Nov 14, 2026, 10:50 PM | [#48](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/48) | Second-pass review at seq 10494, commit `d0f6bf3` (edited 11:08 PM) |
| Sun, Nov 15, 2026, 9:16 PM | [#49](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/49) | Formal reply and @mention: all five open items from #48 fixed and deployed (`18a2ff9+dirty`) |
| Mon, Nov 16, 2026, 1:42 AM | [#51](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/51) | Export passed the integrity checks that were rerun; asked for source of `18a2ff9+dirty` |
| Tue, Nov 17, 2026, 1:13 AM | [#53](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/53) | @mention and status update: deployed revision `949e706`; five findings fixed in source |
| Wed, Nov 18, 2026, 6:55 PM | [#55](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/55) | Reply to an earlier community post: lives in Colorado Springs (not a review post) |
| Thu, Nov 19, 2026, 3:54 AM | [#56](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/56) | Third-pass review of the #53 claims at `949e706`; live export returned HTTP 403 |
| Thu, Nov 19, 2026, 7:26 PM | [#57](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/57) | Formal reply to #55 (meetup offer from Salida); not a review response |
| Thu, Nov 19, 2026, 7:40 PM | [#58](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438/58) | Follow-up on #56: all three findings were real; fixed in `943385f` (deployed) |

## Sources

- Forum thread: [Love.fund pilot: a commitment-pooling experiment](https://gov.gitcoin.co/t/love-fund-pilot-a-commitment-pooling-experiment/25438)
- Official post text: Discourse `/posts/{id}.json` on gov.gitcoin.co, retrieved Friday, November 20, 2026, and checked against the current public wording
- Source repository: [TerexitariusStomp/springforth](https://github.com/TerexitariusStomp/springforth)
- Cited revisions: [`d0f6bf3`](https://github.com/TerexitariusStomp/springforth/commit/d0f6bf30f4101cf809c5d3ff3738108264dbc279), [`949e706`](https://github.com/TerexitariusStomp/springforth/commit/949e70689855fe406dec5ba264028251e47476d1), [`943385f`](https://github.com/TerexitariusStomp/springforth/commit/943385f03a3a1b3fef80416a0693775904c90dd3)

Quoted acknowledgments were verified against the official raw text of posts #39, #40, #42, #46, #49, #53, and #58. Where a post was later edited (#35, #37, #38, #41, #45, #48), this write-up uses the current public version and notes the edit time.

G @ AlienPOWERHour  
Alien@AlienPOWERHour.com
