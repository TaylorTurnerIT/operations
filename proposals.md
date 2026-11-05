# Proposals

Anyone can add a proposal. Newest goes first. Each one needs an incident, the smallest change, and a way to know that it worked.

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
