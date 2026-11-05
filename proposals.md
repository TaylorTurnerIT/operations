# Proposals

Anyone can add a proposal. Newest goes first. Each one needs an incident, the smallest change, and a way to know that it worked.

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
