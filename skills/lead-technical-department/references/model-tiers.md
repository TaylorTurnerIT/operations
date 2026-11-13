# Model tiers

Recorded 2026-11-02 at the user's direction (Luna → Terra → Sol on Codex). Revised 2026-11-13 after Taylor changed the plans. The first section below applies now. Where a later section conflicts with it, the first section wins.

## Plans and pools (from 2026-11-13)

Taylor changed the plans: ChatGPT $100 → $20 (Plus), Claude $20 → $100 (Max 5x), and added OpenCode Go ($10). Taylor said the leftover Codex credits are "free reign but don't be wasteful", and said Go is for DeepSeek Flash and Muse only.

| Pool | Plan | Limits | Role |
| --- | --- | --- | --- |
| Claude Max 5x | $100 | 5-hour window and weekly limit (Anthropic does not publish the numbers) | Leads and owners (Sonnet 5.5), decisions (Opus 5.5), independent review (Haiku 5.5), short fixes |
| OpenCode Go | $10 | $12 per 5 hours, $30 per week, $60 per month, in dollars (vendor docs, unverified for this account) | Bulk implementation and long investigations: `opencode-go/deepseek-v4.1-flash`. `opencode-go/muse-spark-1.3-contributor` for trials. |
| Codex Plus | $20 | 5-hour window and weekly limit. The weekly window is 100% used until 2026-11-16T01:07Z. About 2,260 leftover credits carry Codex until then. | Reserve: Luna for bulk, Terra for owners, Sol for review |

Roles, from cheapest to dearest:

1. **Cheap worker** (Tier 3): Codex Luna Medium, or Go DeepSeek V4.1 Flash. Both are unproven against each other on our code. Until a trial shows that Flash passes the same checks as Luna, use Flash for bounded, low-risk tasks, and keep Luna for the rest.
2. **Owner and spot check** (Tier 2): Claude Sonnet 5.5, or Codex Terra Medium.
3. **Decider** (Tier 1): Claude Opus 5.5. Use it only for what the owner could not decide. Say in the brief why.
4. **Senior reviewer:** Codex Sol High. Taylor, 2026-11-13: "we should save sol for review. It's great for that and shouldn't eat much usage." Sol reviews. It does not own, decide, or implement. Use it for the review gate on a frozen source, for risky diffs, and for disputed findings. Give it the diff, the brief, and the test result. Do not hand it the thread history, because input tokens are most of a review's cost.

Rules for the new plans:

- **Set the model on every delegate and every new thread.** A default is not a plan. Defaults now: T3 uses Claude Sonnet 5.5, and Codex uses Terra Medium. Both were Sol High until 2026-11-13. From 2026-11-09 to 2026-11-13, Sol was about 72% of weighted Codex cost (estimate, T3 usage cache v12).
- **Windows end work.** At a 5-hour or weekly limit, an agent stops mid-work. Treat it as a recoverable interruption. Resume after the reset time. Do not switch pools to get around a limit without telling the lead.
- **Cap parallel Claude agents.** Start with four working at once account-wide. This is a starting value, not a measurement. One burst can use a 5-hour window in about an hour. Adjust with the Claude sampler data.
- **Stagger scheduled wake-ups.** Each wake-up on Claude can open a window.
- **Go limits.** Only the two Go models above are enabled. Another model needs Taylor's change to `~/.config/opencode/opencode.json`.
- **Codex spend.** Credits are free but finite. Keep Sol at or below 10% of weighted Codex cost. Sol now runs as a reviewer, and review input is large. If the share passes 10%, shorten what each review reads before you cut reviews. Check this after one week of data.
- **Measure.** `local.codex-usage-sampler` records both Codex windows and the credits. `local.claude-usage-sampler` records the Claude windows. Both run every 15 minutes with no model. Set new targets after one full week of data, and write them here.

Trial log (add a line for each trial):

- 2026-11-13, `hpc-remote` lead: Go DeepSeek V4.1 Flash wrote one new tests-only file (10 tests, 99 lines, fake values, no network) in its own worktree, commit `58690be`. The lead reran the checks itself: 64 tests, `ruff check`, and `ruff format --check` passed, and the diff was one file. It read the file and found no tautological test. This is a pass on bounded test writing. It is not a head-to-head with Luna, it had no second-model review, and Go cost was not measured. Muse was not tried. The lead uses Flash for similar bounded tests-only or docs-only work, and keeps Luna for state-machine and security code.

The old 70/20/10 target mix and the "about 11 points per day" figure below assumed the old plan. They are retired as targets. Keep the Sol limit above.

## Why tiers (Codex, 2026-11-02)

Weekly Codex allowance is the binding limit. Measured on 2026-11-02, Sol High at the current mix would use about 248% of the weekly allowance by the reset. Relative credit cost for the same tokens:

| Tier | Model | Relative cost | Role |
| --- | --- | --- | --- |
| 3 (base) | `gpt-6-luna`, Medium | 0.08× Sol | Does the work |
| 2 (middle) | `gpt-6.1-terra`, Medium | 0.50× Sol | Decomposes, spot-checks, takes over from Luna |
| 1 (top) | `gpt-6.1-sol`, High | 1.00× Sol | Decides what Terra cannot |

## Target token mix

Share of total model tokens: about 70% Luna, 20% Terra, 10% Sol. This mix costs about 0.255× the current all-Sol burn. It projects to about 88% of the weekly allowance at reset, which leaves about 12 points of headroom. Terra and Sol together are about 78% of the credits, so keep their prompts short.

## Rules

1. **Luna implements.** All code changes, tests, mechanical refactors, doc updates, and bug fixes with a clear reproduction go to Luna first. This includes the first debugging pass.
2. **Terra owns and checks.** A team owner runs on Terra. It splits work into small Luna briefs, spot-checks Luna's diffs, and merges the results. A spot check is a senior developer looking over a junior's shoulder: read the summary, the test result, and two or three risky files. Do not repeat a check on unchanged source.
3. **Sol decides.** (Superseded 2026-11-13: Sol reviews and Opus 5.5 decides. See the first section.) Use Sol only for architecture and interface decisions, root causes that Terra could not find, and sign-off on risky merges. Sol hands the plan back down to Luna. Sol does not implement long changes.

## Escalation ladder

1. A cheap worker (Luna, or Go DeepSeek Flash for a bounded, low-risk task) attempts the task.
2. If the result fails, the worker gets one retry with the exact failure output and a clearer brief.
3. If the retry fails, the owner takes over (Sonnet 5.5 or Terra). Do not give the cheap worker a third attempt.
4. If the owner fails once on a hard problem, or the task changes an architecture, the decider takes it (Opus 5.5). Sol does not take tasks. It reviews.
5. After the decider answers, drop back down. A cheap worker implements the answer.

Escalate on evidence: a failing test, a repeated wrong diff, or a design question. Do not escalate on a feeling that the task is hard.

## Bot review

Luna and Claude Haiku 5.5 do the routine passes. Codex Sol is the senior reviewer (2026-11-13): the review gate on a frozen source, risky diffs, and disputed findings. Terra no longer arbitrates a disputed finding. Sol does.

## Measurement caveats

- OpenAI resets the weekly allowance at random times. It also recently doubled the usage rate, so older logs mislead any conversion from credits to allowance points.
- Mix shares (tokens and credits by model) come from per-request logs and published credit rates. They stay valid.
- Quota burn is calibrated only from `tier_monitor.py` snapshots taken after 2026-11-02. A reset interval is excluded. A shift of more than 35% in points per credit is flagged as an OpenAI rate change.
- Earlier projections (about 248% of the weekly allowance at the current mix, about 88% at the target mix) used the pre-change conversion. They are unverified. Credit costs and relative model costs are not affected.
- The monitor also reports a 2× stress value. If the doubling reverts, the target mix may not fit the allowance.

## Spend control

- Check the measured burn once per day on each pool (Codex, Claude, Go). The Codex figure of about 11 points per day belongs to the old plan and no longer applies. Recompute it from the sampler after the 2026-11-16 reset.
- If a pool burns faster than its window allows, shift work down one tier or to another pool before you reduce parallelism.
- If the Sol share exceeds 10% for two days, the next review must find which tasks escalated and why.
- Start with four Luna workers account-wide. Add more only after burn stays under about 10 points per day.

## Lessons from the department leads (2026-11-02)

The Mudcrab and Dodge leads reviewed the draft. Neither has Luna implementation data yet, because every owner thread still runs Sol. Treat the mix as a target to measure, not a proven result.

1. **Bookkeeping costs more than code.** The Mudcrab lead reports that owner tokens go to hash-pinned receipts, freeze and lease checks, `lsof` checks, re-reading evidence after compaction, CI polling, and review dispatch. Move these to scripts or to Luna. The 70/20/10 mix is realistic only after this move. Before it, Terra-level work dominates.
2. **Context size multiplies cost at every tier.** Auto-compaction keeps a request below the model's `context_window` (500k for Sol and Terra, 272k for Luna). It does not reach the 872k maximum or the 1M figure. In the last 24 hours, requests carried about 160k–230k tokens of context, and 95–98% of input was cached. The top five threads used 67% of the credits. A long-lived owner thread stays near its compaction ceiling, so every request pays for that context again. Each compaction also costs a summary request and a rebuild of the evidence. A fresh Luna worker starts small and finishes before it compacts. Start each Luna worker with a fresh context and a short brief. Size each Luna task to finish inside 272k. End or rotate an owner thread at a milestone instead of letting it cycle through compactions.
   - The API long-context surcharge (above 272k input) can apply to Sol and Terra requests between 272k and 500k on API-key billing. OpenAI states no surcharge for Sol on subscription use, and Terra's rule is unlisted. The cost model ignores the surcharge.
3. **Retry brief.** Include the exact failure output and the file list.
4. **Terra spot check.** Compare the diff with the brief. Confirm that the new test fails before the fix. Read two or three risky files. Also read setup, provenance, resource handling, and anything that touches a live run or lease.
5. **Cheap signals that a Luna diff needs a check.** Files touched outside the brief. Test and source changed together. A weakened assertion. A diffstat much larger than the brief. A new `unwrap` or `allow`. Bevy system ordering or ECS parameter changes. A pre-launch check that is missing or did not run.
6. **Failure modes to watch.**
   - Handoff loss: frozen-source identities and receipt hashes break across tiers. Keep them in files and pass file paths, not summaries.
   - Vague briefs cause setup omissions, as in DODGE-072.
   - Review loops on unchanged work. Keep the existing two-return cap.
   - Same-model blind spot: Luna reviewing Luna-written code. Haiku 5.5 is the independent reviewer. A Terra spot check covers the rest.
7. **Do not switch a running owner thread mid-task.** A fresh Terra relearns the evidence, which has a cost. New owner threads and new tasks start on Terra. Existing owners move at a natural milestone.

## DeepSeek

Superseded on 2026-11-13: Taylor added OpenCode Go and enabled DeepSeek V4.1 Flash, because the Codex allowance fell and a cheap worker now matters more. See the first section. The reasoning below is the 2026-11-02 view.

Do not add DeepSeek now. At the target mix, Luna is only about 22% of credits. A free implementation model would save at most that 22%, and it adds $10 of cost plus quality risk. The credits are in Terra and Sol, so tier discipline saves more. Revisit only if the measured Luna share of credits exceeds about 40%.

## Cost controls (2026-11-05)

Taylor approved these after a root-cause review of 3273 credits from 2026-11-03 06:48Z to 2026-11-05 17:14Z. Terra was 70% of credits. Its context averaged 199k, and 80% of its credits were cached re-reads. Waiting requests were 28% of Terra credits. Eight "implementation" threads ran on Terra for 1149 credits. Delegate-completion wake-ups cost 811 credits, more than all Luna work (631). Shell errors cost at most 1.1% (zsh) to 8.8% (all errors). Treat these as estimates from `waste_scan.py` in the analysis scratch directory.

1. **Scripts wait. Models do not.** Run a long job detached, with a script that writes its own heartbeat and status file. Do not poll with `write_stdin` or `wait` calls, and do not touch a heartbeat file from a model command. In the worst thread, 409 model-driven commands only touched a heartbeat file. If you must poll, check once every 15 minutes or less often. End the turn and let the wake-up event return you.
2. **Implementation starts on Luna.** This includes long investigations, benchmark campaigns, and script-driven work. Terra implements only after the Luna retry fails (see the escalation ladder). A Terra thread that edits code on its own is an exception that the lead must justify.
3. **Fewer, larger Luna briefs, and cheap wake-ups.** Merge small tasks into one brief per coherent change. When a completion notice arrives and sibling workers still run, end the turn at once with one line. Do not read files or run status commands. Process the whole batch in one turn.
4. **Owner context stays below 150k.** The Codex default now compacts at 150k tokens (`model_auto_compact_token_limit`). Keep durable state in files. End or rotate an owner thread at a milestone. Do not re-read bookkeeping files after each compaction.
5. **Do not re-read static instructions.** The global `AGENTS.md` now holds the STE rules, and the default model is Terra Medium. Do not read the `asd-ste100` SKILL.md file for ordinary work. Read `MEMORY.md` only when the task needs it. Limit large outputs with `head`, `tail`, or `rg`. Verify a worktree path, such as `python/.venv/bin/python`, once and put the working path in the brief.

Targets for the daily monitor: Terra average context below 150k, Terra wait share below 10%, Terra implementation credits near zero, and Sol share of credits below 10%. New threads use the new defaults. Running threads keep their old settings until they restart.
