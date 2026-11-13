# Model tiers: Luna → Terra → Sol

Recorded 2026-11-02 at the user's direction. All three tiers run on the T3 `codex` provider instance. Claude stays limited to department leads, as the main skill states.

## Why tiers

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
3. **Sol decides.** Use Sol only for architecture and interface decisions, root causes that Terra could not find, and sign-off on risky merges. Sol hands the plan back down to Luna. Sol does not implement long changes.

## Escalation ladder

1. Luna attempts the task.
2. If the result fails, Luna gets one retry with the exact failure output and a clearer brief.
3. If the retry fails, Terra takes over. Do not give Luna a third attempt.
4. If Terra fails once on a hard problem, or the task changes an architecture, Sol takes it.
5. After Sol answers, drop back down. Luna implements the answer.

Escalate on evidence: a failing test, a repeated wrong diff, or a design question. Do not escalate on a feeling that the task is hard.

## Bot review

Keep the existing roster: Luna and Claude Haiku 5.5. Terra reviews only when a review finding is disputed.

## Measurement caveats

- OpenAI resets the weekly allowance at random times. It also recently doubled the usage rate, so older logs mislead any conversion from credits to allowance points.
- Mix shares (tokens and credits by model) come from per-request logs and published credit rates. They stay valid.
- Quota burn is calibrated only from `tier_monitor.py` snapshots taken after 2026-11-02. A reset interval is excluded. A shift of more than 35% in points per credit is flagged as an OpenAI rate change.
- Earlier projections (about 248% of the weekly allowance at the current mix, about 88% at the target mix) used the pre-change conversion. They are unverified. Credit costs and relative model costs are not affected.
- The monitor also reports a 2× stress value. If the doubling reverts, the target mix may not fit the allowance.

## Spend control

- Check the measured weekly burn once per day with `project_usage.py`. The sustainable rate is about 11 percentage points of weekly allowance per day.
- If daily burn exceeds that rate, shift work down one tier before you reduce parallelism.
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

Do not add DeepSeek now. At the target mix, Luna is only about 22% of credits. A free implementation model would save at most that 22%, and it adds $10 of cost plus quality risk. The credits are in Terra and Sol, so tier discipline saves more. Revisit only if the measured Luna share of credits exceeds about 40%.

## Cost controls (2026-11-05)

Taylor approved these after a root-cause review of 3273 credits from 2026-11-03 06:48Z to 2026-11-05 17:14Z. Terra was 70% of credits. Its context averaged 199k, and 80% of its credits were cached re-reads. Waiting requests were 28% of Terra credits. Eight "implementation" threads ran on Terra for 1149 credits. Delegate-completion wake-ups cost 811 credits, more than all Luna work (631). Shell errors cost at most 1.1% (zsh) to 8.8% (all errors). Treat these as estimates from `waste_scan.py` in the analysis scratch directory.

1. **Scripts wait. Models do not.** Run a long job detached, with a script that writes its own heartbeat and status file. Do not poll with `write_stdin` or `wait` calls, and do not touch a heartbeat file from a model command. In the worst thread, 409 model-driven commands only touched a heartbeat file. If you must poll, check once every 15 minutes or less often. End the turn and let the wake-up event return you.
2. **Implementation starts on Luna.** This includes long investigations, benchmark campaigns, and script-driven work. Terra implements only after the Luna retry fails (see the escalation ladder). A Terra thread that edits code on its own is an exception that the lead must justify.
3. **Fewer, larger Luna briefs, and cheap wake-ups.** Merge small tasks into one brief per coherent change. When a completion notice arrives and sibling workers still run, end the turn at once with one line. Do not read files or run status commands. Process the whole batch in one turn.
4. **Owner context stays below 150k.** The Codex default now compacts at 150k tokens (`model_auto_compact_token_limit`). Keep durable state in files. End or rotate an owner thread at a milestone. Do not re-read bookkeeping files after each compaction.
5. **Do not re-read static instructions.** The global `AGENTS.md` now holds the STE rules, and the default model is Terra Medium. Do not read the `asd-ste100` SKILL.md file for ordinary work. Read `MEMORY.md` only when the task needs it. Limit large outputs with `head`, `tail`, or `rg`. Verify a worktree path, such as `python/.venv/bin/python`, once and put the working path in the brief.

Targets for the daily monitor: Terra average context below 150k, Terra wait share below 10%, Terra implementation credits near zero, and Sol share of credits below 10%. New threads use the new defaults. Running threads keep their old settings until they restart.
