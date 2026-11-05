# Proposals

Anyone can add a proposal. Newest goes first. Each one needs an incident, the smallest change, and a way to know that it worked.

## Mailbox: batch department mail outside the agents

- **Incident:** every `t3_thread_send` starts an LLM turn on the receiver. The usage thread (`0d37f2c1`, position 69) measured 55 agent-originated messages to the Mudcrab lead in 24 hours. Receipts and "call home" timers (mail rule 3) also depend on an agent staying alive. Taylor's idea, 2026-11-05: a mailbox that holds small messages and sends them only when there are enough of them, or when one is urgent.
- **Change, step 1 (no fork):** a durable mailbox outside T3, for example one SQLite file with one row per memo (`messageId`, sender, recipient, urgency, created time, receipt). A `mail send` command writes a row and starts no LLM. A small launchd job, not an agent, flushes a recipient's mailbox as one digest message with `t3_thread_send` in `queue` mode. It flushes when any of these is true: the count reaches N, one memo is marked urgent, or the oldest memo reaches an age cap. The age cap means no memo waits forever, which prevents the deadlock that rule 3 exists to catch. Each memo in a digest keeps its own `messageId`, so rule 1 (authority) still works.
- **Change, step 2 (only if step 1 is not enough):** T3's own queue (`t3_queue`) can edit, reorder, and cancel a queued message, but a queued message sends when the current run ends, and I found no "hold" state. This is from the tool description. I have not read the T3 source. A small fork could add a held state. Keep the fork as a patch on top of the nightly tag, so a daily job can rebase it and fall back to upstream if the rebase fails. Do not start step 2 before step 1 shows a gap.
- **Check:** the same memos arrive with receipts, and the number of receiver turns per memo falls from the baseline (55 per day to the Mudcrab lead). Also check that no urgent memo waited for the count threshold, and that none passed the age cap.
- **Unknowns:** whether a non-agent process may call T3's send API, and with which credential. Use a `secretRef` and never read the value in chat. Also whether a digest saves tokens when the receiver reads each memo anyway.
- **Status:** open. Wait for the mail pilot review (10 memos or 2 weeks) before building.

## Replace Slurm accounting polling with a push stream

- **Incident:** the `dodge-native` and `hpc-remote` leads designed a local watcher that polls Slurm accounting with no agent involved. It needed a ruling against the no-polling rule. Taylor ruled on 2026-11-04, in the `operations` lead thread, that the local watcher is acceptable as designed. Taylor also said that a websocket stream would be better than polling.
- **Change:** ask the `dodge-native` and `hpc-remote` leads whether the cluster has a push source for job state. Examples are `strigger`, a job epilog hook, or a small HTTP server that offers a websocket stream. If one exists, the watcher reads the stream instead of polling. If none exists, keep the watcher as it is. Check what the cluster allows first: hooks may need admin rights, and inbound connections may be blocked, which would need an SSH tunnel. These limits are unverified.
- **Check:** the watcher receives a job state change without a poll interval, and its latency is lower than the old poll interval. Agent turns do not increase.
- **Status:** open. The leads have not been asked yet.

## Filter user messages by `createdBy`

- **Incident:** `skills/lead-technical-department/references/t3-code.md:9` tells a lead to find every user message. Agent-sent messages also have `type: user_message`. Position 0 of the `hpc-remote` lead thread is an example, with `createdBy: agent` and `senderThreadId` set.
- **Change:** tell leads to read `createdBy: user` as Taylor's messages.
- **Check:** a lead adopting a team lists only Taylor's messages as instructions from Taylor.
- **Status:** open.

## Move the daily tier check out of scratch

- **Incident:** the scheduled task `Daily Codex tier-mix check` runs `tier_monitor.py` from `/Users/taylor/.t3/scratch/2026-11-02-can-you-investigate-per-thread-a0dd610f`. Scratch folders are not meant to last.
- **Change:** move the script and its CSV here, and update the schedule's path.
- **Check:** the next daily run appends a row from the new path.
- **Status:** open.
