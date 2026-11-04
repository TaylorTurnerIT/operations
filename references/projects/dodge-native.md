# Dodge AI (dodge-native)

Repository: local `/Users/taylor/Projects/dodge-native`. This is a research project, not a GitHub contribution workflow. There is no GitHub Project board and no PR queue today. Acceptance is measured play and audited receipts. Use the supervisor conversation and the owner's packets as the task record. If Taylor asks for a board, apply `github-projects.md`.

## Goal and teams

Goal: train a teacher from structured game data, then train a vision student to copy it.

- **Accountable owner:** thread `518b8c2d-01fb-4f39-acc3-f2d9f1f35c4e` ("Transformer Based Dodge Bot"). It owns runs, packets, leases and results. It runs on Terra Medium. The owner reads Taylor's wording itself, so quote Taylor exactly when you pass a decision down.
- **Idea threads (finished, idle):** wall rays, heuristic inputs, fresh-seed scaling, death telemetry, frame count progression. They hold earlier work and evidence. Read them before you claim something was never tried.
- **New work streams are team threads, not lead subagents (Taylor, 2026-11-03).** Hierarchy: Taylor, lead, team threads, their subagents. When the lead branches off, it starts a team thread (Terra Medium) with a self-contained brief. That thread runs its own Luna implementers and Luna plus Haiku reviewers. Taylor asked for this, so starting a new top-level thread is allowed for a requested work stream.
- **Child threads:** team threads start them for bounded tasks, such as the ray ablation. Two early lead-started subagent threads (the difficulty survey and multi-slot admission) stay as they are.
- **Find every thread.** List with `settled:false`, `includeSubagents:false`, limit 100. A list of the 30 newest threads missed the rays thread. Check the whole list at the first pass of a session.

## Machines and limits

- **M4** (`Taylors-MacBook-Pro-2`) runs the T3 server. Prefer it for evaluations and analysis.
- **M1** (`ssh m1-macbook`) runs training. It is also Taylor's client and Mudcrab's review machine, so check actual occupancy first. Protected consumers: dictation (Phonon, Voxtype) and any interactive session.
- **M1 admission today:** one lease slot in `~/.local/state/dodge-native/accelerator/state.json`, a 6 GiB RSS and MPS limit per run, and no elapsed-time cutoff. M1 Pro: 32 GiB RAM, 10 cores. M4 Pro: 48 GiB, 14 cores. A run peaked near 3 GB RSS and 2.4 GB MPS.
- **Taylor allows many parallel runs (2026-11-03).** The single slot is a design choice, not hardware. Taylor accepts that each run takes longer, because Taylor acts on results in bursts. Budget and allocation belong to the lead, not the owner thread. Initial budget, tunable after measuring concurrent runs: M1 reserves 10 GiB for Taylor's session, dictation and Mudcrab reviews, so it admits 3 runs at the 6 GiB per-run cap. M4 reserves 16 GiB, so it admits 5 runs. Cores are not pinned, and runs use background priority. One run alone gets the whole machine, and the share shrinks as runs join. A separate Terra thread builds multi-slot admission. Until it lands, the single slot stands. The earlier "two game-eval processes" cap is withdrawn.
- Cloud computers and paid spend need Taylor's approval. No later message removed this limit.

## Taylor's holds and authority

- **Training hold (2026-11-03):** "Let's hold training for now." It stands until Taylor lifts it. Diagnosis, analysis, design and evaluation games are allowed.
- **Old stops are discarded (2026-11-02):** no-retry and no-successor rules, and old memory notes such as DODGE-055, no longer block work. Only a stop that Taylor gives later counts. Keep earlier results. Do not delete them.
- **Check holds against Taylor's own words.** Agent reports and memory notes are not Taylor's holds. On 2026-11-02 the lead repeated an unverified "no new run without Taylor's say-so". Search the thread for Taylor's message before you pass a hold down.
- **Feature authority:** inside a requested feature, the owner runs, retries and registers successors without asking. Taylor approves candidate run designs, cloud spend and anything that changes the hold.

## Models and review

- Lead: Sonnet 5.5. Owner: Terra Medium. Implementation: Luna Medium in a fresh context, with a short brief that fits 272k. Sol only for architecture, or a problem Terra failed once. See `model-tiers.md`.
- Review: Luna plus Haiku 5.5. Luna alone counts if Haiku is unavailable. Do not review unchanged source again.
- Rotate the owner thread at a milestone. At about 17.9k items it pays for its history on every request. Never rotate it while a run is live.
- No CI polling loops. Put receipts, lease and `lsof` checks and review dispatch in scripts.
- **Keep the lead thread clean (Taylor, 2026-11-03).** The lead does not start implementation or review threads, and does not relay reviewer findings. A team thread runs the whole loop (implement, Luna and Haiku review, fix, delta review) and reports once with the final commit. The lead reports outcomes, decisions and blockers to Taylor.

## Runs: lessons from DODGE-072 to 075

- **Setup failures cost the most.** 072 lacked a required directory. 074 used a different archive name than the stage script expected. Every setup brief needs pre-launch checks: required directories, archive name, and hash readback of the script and the archive. Run them before the host claim.
- **A failed setup is not a result.** Record zero training counts. Start a separate successor with the fix. Keep the failed evidence.
- **Custody.** A launched run needs a watcher: an attached execution owner, or a script that watches the M1 processes and receipts. In 075 the custody thread failed while the run lived on, and the coordinator was orphaned. Do not trust a thread status for a live run. Check the M1 processes, the log timestamps and the lease directly (read-only `ssh m1-macbook`).
- **Long runs.** Ask for a direct check when a run is long. Report the progress fraction, such as "update 48 of 72". Do not call a run two-thirds done "early".
- **Two layers of stopping (Taylor, 2026-11-03).** A script applies the stop rule. An agent also looks at a one-line summary per live run on a cheap, scheduled pass: age, progress, latest evaluation against the best. It stops a run that is plainly going nowhere, even if the rule has not fired. In 075 nobody looked until Taylor asked. Wake-on-completion alone does not cover this.
- **Stopping.** Stop through the run's own supervisor, not a blind kill. Keep checkpoints, logs and receipts. If no normal terminal receipt exists, the audit cannot accept the stop. Release the lease with a "cancelled by Taylor" receipt. Confirm that no owned process remains. Do not call it success.
- **Stale reservations.** A failed or stopped launch can leave a lease held. Verify that no process belongs to it and read its receipt. Then release it with the reason recorded.

## Results and acceptance

- **Env bug found 2026-11-04.** The port omitted the cartridge's divide-by-16 before squaring kamikaze distance components. Release builds wrapped silently and could select the wrong speed branch, in all difficulty modes. All earlier training and scores used that game. Treat earlier reference numbers as pre-fix until they are re-scored, and do not compare them directly with post-fix runs.
- Judge runs by measured play: mean score, Q1 and early deaths, against the retained control12 (pre-fix: Mean 4533, Q1 1836, early deaths 7 in 512 games). EV, KL and value are diagnostics only.
- Always score all three difficulty modes. Mode 2 appears to be the normal setting (speed 1.0, spawn factor 1.0). Mode 3 is hardest. Say which seeds and how many games each result used, and whether they were consumed.
- A full-state heuristic is the reference. It beats checkpoint 420 in all three modes. It reads enemy personality and pattern history that the model does not get.
- Do not ask Taylor to review what metrics already show. Taylor's human decisions here are run designs, spending and the hold.
- Human testing starts with the vision student. Until then, nothing is "ready for human testing".

## Current direction (2026-11-03)

Richer inputs are the leading lever. Candidate design: richer inputs plus random difficulty per game, skewed toward hard, always scored on all modes. The owner prepares one design for Taylor's approval. Nothing launches until Taylor lifts the hold.

## Writing

Taylor valued the progress paper (`paper/` on branch `t3/thread-progress-metaanalysis`) and wants more of that. When a diagnosis or run is complete, offer a short write-up in the same style. Do not start one without Taylor's go-ahead.

## Supervision

The owner calls home at launch, completion and a real blocker. Do not poll. Add a watch script for any live run that has no custodian.
