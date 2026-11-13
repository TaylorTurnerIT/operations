# Proposals

Anyone can add a proposal. Newest goes first. Each one needs an incident, the smallest change, and a way to know that it worked.

## Stop Sol from being the default (2026-11-13)

- **Incident:** Sol was 3–4% of weighted Codex cost on 2026-11-05 and 2026-11-06, and absent on 2026-11-07 and 2026-11-08. It returned on 2026-11-09 at 58%, and was 79–100% on 2026-11-10 to 2026-11-12. From 2026-11-09 to 2026-11-13, Sol had about 4.1 million fresh input plus output tokens, against 2.4 million for Terra and 4.9 million for Luna. Weighted by tier cost (Sol 1, Terra 0.5, Luna 0.08), that is about 72% Sol, 21% Terra and 7% Luna. This is an estimate that ignores cached re-reads. The target was 10% Sol. Source: `~/.t3/userdata/usage-scan-cache-v12.json`.
- **Causes (from files and run records):**
  1. T3's `settings.json` has `defaultModelSelection` = Codex `gpt-6.1-sol`, reasoning `high`. The backup from 2026-11-08 has the same value.
  2. `~/.codex/config.toml` has `model = "gpt-6.1-sol"`. Its reasoning effort was `medium` on 2026-11-05 and is `high` now (file changed 2026-11-12 12:25 local; who changed it is unknown). The Nov 5 note in `model-tiers.md` says the default is "Terra Medium". The files do not match that note, and I found no evidence that the default was ever Terra.
  3. A thread started from the web takes the default. The Hamiltonian lead did: 112 runs on Sol from 2026-11-09 to 2026-11-11, plus 22 runs in its experiment and review threads. By 2026-11-12 the lead itself ran on Claude Sonnet 5.5, so its own Sol use had already stopped.
  4. A delegated task aimed at Codex with no model set most likely uses Codex's default (Sol). This is likely, not proven. The Mudcrab lead had 29 Sol runs from 2026-11-09 to 2026-11-12T18:06Z, then moved to Claude Sonnet 5.5. Its delegated investigations ran on Sol after that, the latest at 2026-11-13T05:21Z. The Tonga-NixLab "Coolify" thread also ran on Sol.
  5. The tier plan exists only as prose in a skill. Nothing enforces it.
- **Change (proposed; Taylor owns defaults):** (a) change T3's `defaultModelSelection` and the Codex `config.toml` default away from Sol. (b) Move the leads off Sol. (c) The skill says: a delegate sets its model explicitly. (d) A non-LLM daily check counts Sol requests per thread from the v12 cache and tells Taylor when Sol passes 10%.
- **Done 2026-11-13, on Taylor's "Agreed. Make it happen.":** (a) T3's default is now Claude Sonnet 5.5, and the Codex default is Terra Medium. Backups are in the operations lead's scratchpad. (b) Already true: the Hamiltonian and Mudcrab leads run on Sonnet 5.5. (e) Memos about the plan changes and the "set the model explicitly" rule went to the five leads. Still open: (c) needs a skill change, and (d) needs the monitor ported to the v12 cache.
- **Also found:** `analyze_usage.py` and `tier_monitor.py` read `usage-scan-cache-v10.json`. T3 now keeps `v12`. That is why the daily check looked stale. It was not missing data. The monitor needs a port to v12.
- **Check:** Sol is at or below 10% of weighted cost for three days. Every new thread and delegate shows its model in the run record.
- **Status:** (a), (b), (c) and (e) done. (c) merged 2026-11-13 as `6add5f8`: the skill now says to set the model explicitly. (d) open. Check Sol share from 2026-11-14 on.

## Rebalance usage for the new plans (2026-11-13)

- **Incident:** Taylor changed the plans: ChatGPT $100 → $20 (Plus), Claude $20 → $100 (Max 5x), and added OpenCode Go $10. The usage sampler shows the Codex effect. At 2026-11-13T01:31Z the plan changed from `prolite` to `plus`, and the weekly window went to 100% used. It resets 2026-11-16T01:07Z. Until then Codex runs only on credits: 2,274.99 left at 2026-11-13T04:59Z. A new 5-hour Codex window now exists, and it is at 0%.
- **Facts (measured):** the old pace was 3,273 credits in 58.4 hours (about 56 per hour, about 1,345 per day; from `model-tiers.md`). At that pace the credits last about 41 hours. The weekly reset is 68 hours away. To reach it, Codex must average at most 33 credits per hour, which is about 60% of the old pace. Claude baseline, from local session logs: 1 to 4 million fresh tokens per day (mostly Sonnet 5.5), and the busiest 5-hour block was 6.5 million (2026-11-05). OpenCode Go in T3: only `deepseek-v4.1-flash-free` is listed, so the Go models are not wired yet. The Muse provider is disabled.
- **Facts (from the vendors' pages, unverified for this account):** OpenCode Go limits are $12 per 5 hours, $30 per week, $60 per month, in dollar terms ([docs](https://opencode.ai/docs/go/)). Claude Max 5x gives 5 times the Pro limit per 5-hour session, plus a weekly limit that Anthropic does not publish.
- **Estimate (assumes Plus is one fifth of the old plan; unverified):** the old weekly allowance was about 8,600 credits (about 11.6 points per 1,000 credits, one short interval, ±20%). One fifth is about 1,700 credits per week, or about 10 credits per hour. Codex use would then fall by about 80%. The sampler now records the weekly window, so the first full week after 2026-11-16 will replace this guess.
- **Change (proposed; Taylor decides each item):**
  1. **Until 2026-11-16T01:07Z:** keep Codex to Luna and short tasks. No new Sol or Terra owner threads. Ask the leads whose owners run on Terra or Sol to move them at a milestone.
  2. **New split of work:**

     | Pool | Plan | Role |
     | --- | --- | --- |
     | Claude Max 5x | $100, 5-hour window plus weekly | Leads and owners (Sonnet 5.5), spot checks, independent review (Haiku 5.5), hard decisions (Opus 5.5) |
     | OpenCode Go | $10, dollar caps | Volume work: implementation, long investigations, script-driven runs (DeepSeek first; Muse Spark if Taylor enables it) |
     | Codex Plus | $20, 5-hour window plus weekly, credits | Reserve. Luna for bulk until Go is wired. Terra and Sol only when Claude cannot do it. |

  3. **Escalation ladder, replacing Luna → Terra → Sol:** cheap worker (DeepSeek or Luna) attempts → one retry with the failure text → Sonnet 5.5 takes over → Opus 5.5 decides. This reverses the rule "implementation never Claude". That rule existed because Claude was $20. It needs a change to the lead skill and `model-tiers.md`, so it waits for Taylor.
  4. **5-hour windows:** cap how many Claude agents run at once, so one burst does not use a window in an hour. A schedule that wakes an agent opens a window, so stagger unattended jobs. When a window ends, an agent stops mid-work. Leads already treat that as a recoverable interruption.
  5. **Measure Claude:** there is no Claude sampler. Option A: Taylor reports `/usage` numbers now and then. Option B: a non-LLM job that reads Claude's usage endpoint. Option B needs the local Claude credential, so it needs Taylor's explicit yes.
  6. **Pause `Daily Codex tier-mix check`** until the new plan is set. It reads request logs that ended 2026-11-03, and it assumes the old plan's economics. It would spend credits on stale data.
- **Check:** Codex credits are above zero at 2026-11-16T01:07Z. The sampler's weekly window gives Plus a measured credits-per-week. The count of 5-hour limit hits on Claude is zero or low for a week. Throughput does not fall: tasks that Luna or DeepSeek fail do not queue up.
- **Unknowns:** whether the credits cost money (Taylor knows), the real Plus weekly size, the Claude weekly limit, whether DeepSeek on Go matches Luna on our code (needs a trial on a bounded task), and whether Go and Muse can be connected to T3.
- **Done already:** the usage sampler (`~/.local/share/codex-usage-sampler/sample_usage.py`) now also records the weekly window. It is not in this repo.
- **Status, 2026-11-13:** Taylor approved the proposal. Done: item 2 and 3 (new split and ladder merged into the skill as `6add5f8`), item 5 (Claude sampler running, Taylor approved the credential read), item 6 (daily check paused), Go models enabled for DeepSeek Flash and Muse only, and the leads informed by memo. Item 1 softened: Taylor says the credits are free but not to be wasted. First Go trial passed (`hpc-remote` lead, tests-only task). Open: item 4 (the Claude agent cap is a starting value of four, unmeasured), a monitor port to the v12 cache, and a measured Plus weekly size after 2026-11-16T01:07Z.

## Mailbox: batch department mail outside the agents

- **Incident:** every `t3_thread_send` starts an LLM turn on the receiver. The usage thread (`0d37f2c1`, position 69) measured 55 agent-originated messages to the Mudcrab lead in 24 hours. Receipts and "call home" timers (mail rule 3) also depend on an agent staying alive. Taylor's idea, 2026-11-05: a mailbox that holds small messages and sends them only when there are enough of them, or when one is urgent.
- **Change, step 1 (no fork):** a durable mailbox outside T3, for example one SQLite file with one row per memo (`messageId`, sender, recipient, urgency, created time, receipt). A `mail send` command writes a row and starts no LLM. A small launchd job, not an agent, flushes a recipient's mailbox as one digest message with `t3_thread_send` in `queue` mode. It flushes when any of these is true: the count reaches N, one memo is marked urgent, or the oldest memo reaches an age cap. The age cap means no memo waits forever, which prevents the deadlock that rule 3 exists to catch. Each memo in a digest keeps its own `messageId`, so rule 1 (authority) still works.
- **Change, step 2 (only if step 1 is not enough):** T3's own queue (`t3_queue`) can edit, reorder, and cancel a queued message, but a queued message sends when the current run ends, and I found no "hold" state. This is from the tool description. I have not read the T3 source. A small fork could add a held state. Keep the fork as a patch on top of the nightly tag, so a daily job can rebase it and fall back to upstream if the rebase fails. Do not start step 2 before step 1 shows a gap.
- **Check:** the same memos arrive with receipts, and the number of receiver turns per memo falls from the baseline (55 per day to the Mudcrab lead). Also check that no urgent memo waited for the count threshold, and that none passed the age cap.
- **Unknowns:** whether a non-agent process may call T3's send API, and with which credential. Use a `secretRef` and never read the value in chat. Also whether a digest saves tokens when the receiver reads each memo anyway.
- **Status:** step 1 built 2026-11-05, on Taylor's go: [`mailbox/`](mailbox/README.md). Tested with a fake sender and once end to end. A non-agent process can send with `t3 thread send`, and T3 marks those messages `createdBy: agent`. No department uses it yet. The mail rules still say to cite a T3 `messageId`, so a department switch needs a rule change. Taylor, 2026-11-05: no need for the T3 fork (step 2) yet. Revisit only if step 1 shows a gap.

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
