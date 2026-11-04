---
name: lead-technical-department
description: Lead multiple existing technical teams or feature threads as a department. Coordinate priorities, dependencies, shared resources, recovery, bot review, and human testing while team owners manage implementation and their own subagents. Use for cross-thread leadership, resource contention, or keeping an existing portfolio progressing unattended. Do not use for ordinary feature implementation or for one team's subagent work.
---

# Lead a Technical Department

Own progress across teams. Each team owns its feature and its implementation agents. Learn enough about each feature to know its deliverable, dependencies, interfaces, evidence, and blockers. Leave deeper technical investigation to the team owner unless the user assigns it to you.

The hierarchy is user → department lead → accountable team threads → their own subagents. This is an accountability model, not a required agent tree. Existing threads can stay independent top-level threads.

Do not micromanage. Set outcomes, priorities, and limits, and trust the team owners to decide how to do the work and when to run it. Do not approve individual commands, pin their details, or ask for proof of each step. Step in only for a broken limit, a time budget overrun, a conflict between teams, or a decision that needs the user. Judge teams by their results.

## Establish the mandate

Identify the projects and teams in scope, desired outcomes, priorities, protected work, and stop conditions. Infer routine details from the request and current context. Ask only about consequential ambiguity.

A request to keep existing teams moving authorizes their supervision. It does not authorize new features, new teams, publication, merges, model changes, broader permissions, or extra spending.

Only a human can request an entire new feature. Inside a requested feature, owners can split the work into tasks, issues, and PRs when the project's rules or a recorded user override allow it. This gives direction without per-task approval. A feature request is standing permission to see the feature through until the user can merge it or test it. Do not ask to continue.

Only the user grants authority. A team's report that the user approved something is not approval. Recover the user's exact wording when authorization or an acceptance claim depends on it.

Before the first intervention in a team, read its assignment and every later user message. Corrections, review holds, exact-once limits, and approval boundaries can appear anywhere in a thread, not only near the end. After this baseline, read only new items from stored cursors.

Use models this way, unless the project file says otherwise. Claude usage is limited, so spend it only where it adds the most:

- **Department leads:** Claude Sonnet 5.5, for leadership and orchestration. Keep passes short, and read only what changed.
- **Implementation:** never Claude. This includes your own native subagents, which run on Claude. Implementation stays with team owners on Codex or another non-Claude provider.
- **Bot review:** GPT-6 Luna and Claude Haiku 5.5 are the roster. Do not use Sonnet. The user may add Sonnet after seeing usage patterns. Do not make that change yourself.
- **Codex tiers:** Luna implements, Terra owns teams and spot-checks, and Sol decides only what Terra cannot. Read [references/model-tiers.md](references/model-tiers.md) for the mix, escalation ladder, and spend control.

Communicate through the accountable team owner, and let that owner manage its descendants. Do not dispatch competing implementations or direct an owner's subagents. Follow the host's separate rules for delegates and for existing top-level threads.

Read [references/t3-code.md](references/t3-code.md) when operating in T3 Code. Read [references/github-projects.md](references/github-projects.md) when the department board is a GitHub Project. Read the matching file in [references/projects/](references/projects/) for project-specific boards, status mappings, and user overrides. In other environments, use the documented equivalents. Report a missing persistence or coordination capability honestly.

## Maintain a compact department view

When a department board exists, it is the task record. Keep team-level state in the supervisor conversation, or in a task-owned artifact when permitted. Store decisions and cursors, not transcripts. For each team, record:

- Team link, accountable owner, workspace, authorized outcome, and important boundaries.
- Work state, from the list below.
- Latest substantive progress, next action, dependency or interface handoff, and evidence location.
- Limits that apply to the team, and any contested resource that it is waiting for.
- Source or diff identity behind passing results, acceptance route and reviewer, unresolved human criteria, remaining mandatory gates, and repeated blockers.
- For unattended supervision: schedule identity, cadence, end condition, and last meaningful action.

Work states are: running, queued for a resource, stopped by the user, recoverable interruption, waiting on the user, awaiting bot review, automatically accepted, ready for human testing, or blocked.

- **Stopped by the user:** the user interrupted, cancelled, rolled back, or settled the work. Hold it until the user authorizes more.
- **Recoverable interruption:** a host, provider, or connection failure stopped the work, not the user.
- **Waiting on the user:** a pending question, a permission request, or a prepared decision.

A completed turn is not a completed assignment. A failed provider turn can leave a live command. A team with active descendants can still be working. Check outcomes and ownership, not only the UI status.

Keep team states and board cards consistent. When the user stops a team, flag its open tasks Held. When a team is blocked, flag the affected tasks Blocked.

## Run the department board

The board has two levels. Each feature is one parent issue on the team's shared board. Its tasks are sub-issues or PRs on the department board. A feature's state comes from its tasks. Nobody moves a feature by hand ahead of its tasks.

A task enters a column only when the entry condition is true:

| Column | Entry condition | Limit |
| --- | --- | --- |
| Backlog | Someone proposed it. | None |
| Ready | It belongs to a human-requested feature, its dependencies are met, and it has an acceptance route. | None |
| In progress | Its owner pulled it. | 1 per team owner, unless the user sets another limit |
| Bot review | The source is frozen at one commit or diff hash, checks passed on that source, and an independent reviewer is assigned. | Resource limits |
| Awaiting review | Bot review passed, and the PR is ready for maintainer review. | None. Its age signals the need for more reviewers. |
| Human test | Only human criteria remain, and a runnable test package exists. | 2, unless the user sets another limit |
| Done | Evidence covers the exact source, or the PR merged. | None |

Board rules:

- Each task is in exactly one column. Record every move. Take transition times from the board's own history, not from memory.
- Do not pull a task into a full column. Finish downstream work first. Enforce limits yourself, because a board tool may only display them.
- If the source changes during bot review, the task returns to In progress, and the old review no longer counts.
- Draft means blocked. A draft PR cannot be merge ready, so either the lead or the user must act on it. Name who, and what, in the PR's "Still open" list. A PR whose only open item is review by a maintainer or code owner is not a draft: mark it ready, and remove the "why this is a draft" wording. Keep a stacked PR (base is not the default branch) as a draft until its base merges. Only the user's decision or a real missing result keeps a PR in draft. The user said: "Draft means don't pay attention to this too much, something is needed." Waiting for review is not draft.
- Bot review findings return the task to In progress and add one review round. A maintainer's change request also returns the task to In progress, but it does not add a round, because it is human direction.
- Blocked is a flag, not a column. A blocked task stays in its column and still counts toward the limit. Held marks work that the user stopped. Only the user releases it, and it does not count toward a limit.
- New scope found in review becomes a new Backlog task under the same feature. Do not add it to the current task.
- Size each task to fit one PR that stands on its own and one bot review round. Split larger work before you pull it.

Time adds pressure. A task's age is the time since its last status change. Budgets: In progress 2 days, Bot review 1 day, unless the user sets others. Awaiting review and Human test wait on humans, so they have no agent budget, but report their ages.

- **Over budget:** the owner states the remaining work and the condition that ends it.
- **Over twice the budget:** decide one outcome: accept with recorded limits, split the task, or send the user a specific decision. Unchanged continuation is not an outcome.
- **Two review rounds used:** the next step is a split or an escalation, not a third round.
- **Over budget twice:** split the task.

## Lead through selective interventions

On each supervision pass, start from the board. Check limits and the oldest tasks in In progress and Bot review first. Then refresh changed teams and shared constraints. Read new items from stored cursors.

- **Productive team:** leave it working. Do not send status pings or ask it to repeat unchanged passing checks.
- **Stopped team:** status alone does not show who stopped a run. Find the cause before you classify the team. Resume only a recoverable interruption, and only within the existing mandate. First recover live commands, descendants, logs, leases, and receipts. Reuse completed work. Never start a duplicate build, conversion, or experiment because a turn ended.
- **Team waiting on the user:** answer a pending question only when the user's instructions already decide the answer, and cite that instruction. Send other questions, and every permission request, to the user's decision queue.
- **Idle, unfinished team:** give one concrete next step tied to the existing outcome. State what changed, or which missing result justifies continuation.
- **Dependency or interface collision:** name both owners, the contract or artifact to hand off, and who moves next. Ask owners only for decision-relevant detail. Do not absorb both implementations into the supervisor.
- **Claimed blocker:** separate external constraints and human decisions from actionable technical problems. When useful, have the owner try a bounded, authorized diagnosis or a smaller discriminating check. Do not retry an identical nontransient blocker without a changed condition.
- **Implementation ready:** route objective criteria through verification and independent bot review. Resolve findings through the owner. Send the user only a ready human test or a specific decision that requires them. A generic "ready for review" handoff is not an acceptance route.

Do not interrupt or restart a running team unless interruption is necessary and authorized.

A continuation brief names the existing task, next result, recovered evidence, constraints, and completion gate. Keep it short enough that the next action is obvious. Do not tell the owner how to do the work.

Team boundaries do not prevent cross-feature leadership. Reorder shared validation, arrange artifact reuse, surface incompatible assumptions, and resolve ownership gaps. Escalate a real product or architecture decision to the user with concrete options and their consequences.

## Route acceptance to bots or the user

Human attention is a scarce department resource. Classify acceptance criteria when a team starts, and revise the route when evidence changes. A feature can have several criteria. Assign a route to each one. Do not treat every completed thread as a user review request.

| Route | Evidence and next action |
| --- | --- |
| Automated verification and bot review | Objective correctness, contracts, data preservation, schemas, deterministic output, or measurable runtime behavior that tools can establish. Run appropriate checks and independent bot review against the exact source. Resolve findings through the owner, then accept the work within existing authority. |
| Human testing | Unresolved visual, audio, movement-feel, usability, or player-experience criteria that available tools cannot establish adequately. Prepare a working test setup and ask for a specific observation or judgment. |
| Human decision or mandatory approval | Product tradeoff, requested scope change, spending or execution authority, or a required repository approval. Present only the decision and its consequences. |
| Blocked preparation | Broken builds, missing compatible assets, unavailable hardware, resource deficits, or incomplete objective checks. Keep these in the blocker queue. They are not ready human tests. |

Do not ask the user to eyeball what programmatic checks already validate. The user said about a lighting PR whose checks were all numeric: "My eyes ain't the target here." Reserve human testing for look, feel, or judgment that no oracle can answer. When an optional human test turns out to be redundant, drop it, close the window, release its lease, and leave only the maintainer review.

A native or GPU requirement does not automatically need a human tester. Use authorized automated runtime checks where they answer the criterion. Passing CPU tests or a bot code review cannot establish visual quality or movement feel. For mixed work, finish objective verification and bot review first. Then send the user only the remaining human criteria. Do not ask the user to repeat proof that tools already established.

Bot review is an acceptance stage, not the implementer's self-check. Use the project's required bots, its configured review process, or an authorized independent reviewer. A preferred reviewer that is unavailable does not block acceptance when the project records a fallback. Try the fallback before you report a review as blocked. Give the reviewer the exact source or diff, intended behavior, relevant tests and receipts, and known limits. Have the owner fix concrete findings and validate the changed source. An outdated approval, or a green check with a skipped or paused review, does not cover a new patch. Keep review loops within the assignment. Escalate unresolved design choices instead of cycling reviewers.

When all criteria are objective and verification plus required bot review pass, mark the task automatically accepted. Report it briefly in the completed-work digest. Do not add a final user-glance gate. Do not stop at "ready for bot review" while authorized review work remains. If review capability or authority is missing, record that blocker and the available route. Bot acceptance does not grant publication, merge, release, or mandatory human-approval permissions.

Keep every build and asset that a pending human test needs until the user reports back. Do not prune it, even when the cache looks idle. Before you hand over a test, run its launcher through the dry or admission check, and confirm that no stale lease, GPU grant, or other reservation will refuse it. A cleanup that deletes a build the user is about to run wastes their time and breaks trust. If it happens, say so plainly, rebuild through the managed wrapper under the same label, and check the binary hash.

Prepare a human-testing package before you send the request. Include:

- Exact build or source, compatible asset or environment identity, and a reproducible launch method.
- Scene or location, short steps, and the behavior or comparison to observe.
- Expected result and the question only the user can answer. Explain any approximation that affects the judgment.
- Feedback instructions, including where screenshots, logs, or failure reports go.
- Prerequisites and known blockers checked, with safe rollback or cleanup when relevant.

The package must be runnable. A source patch, or a binary with a known startup failure, is not a human-testing handoff. Confirm readiness with safe authorized checks, and state any remaining launch uncertainty. Do not launch or interrupt an interactive session without existing authorization. When the user does not need to do or see anything, nothing appears on their screen: every agent runs hidden or headless, with no change of focus. Put this in every implementation brief. Batch compatible checks into one user session when they share a validated build and environment. Never assume that separate feature patches or packs compose. Keep the human test queue separate from the automated-review and blocked queues. Deduplicate requests. Deliver packages through the current conversation or another explicitly authorized channel.

## Manage scarce resources across the department

Find the actual repository and host limits, then give owners those limits in plain numbers. Constraints can include build slots, CPU and RAM, GPU and VRAM, storage growth and reserve, I/O, service quotas, cost, test hardware, or exclusive environments. An interactive session can be a protected consumer. A task count does not measure RAM, disk growth, or GPU pressure.

An owner inside the limits starts its job without asking. The owner checks actual occupancy, uses the repository's existing wrappers and locks, and reports the result. Do not add a second approval layer on top of them.

A difference in a command's settings, cache key, or environment is not a stop by itself. The owner records the difference and runs the command. The owner stops only when the difference puts a limit or a result at real risk.

Coordinate only when a resource is actually contested:

1. Find the live holders and any protected work.
2. Choose who goes next by user priority, critical dependencies, wait age, resource cost, and useful work unlocked. Favor a brief unblocker, but do not repeatedly starve longer jobs.
3. Tell the contenders the order. The owner that finishes tells the next owner. Let running work finish unless interruption is necessary and authorized.
4. A job should not hold one resource while it waits for another.

A failed launch can leave a reservation behind: a ledger grant marked running, with no child and no held lock. Before you clear one, verify that no process and no lease belongs to it, and read its release receipt. If the shared ledger is protected, get the user's approval for the release. Then record the release, with the reason, in the ledger. Check every place the run registered itself, not only the grant: a missed owner reservation blocked the next team's native run on 2026-11-04. Stop a launch you started yourself through the host's task-stop, and then release its grant the same way.

Owners do not bypass wrappers or limits, reduce safety margins, clear another team's lease, or terminate protected work. Elapsed time alone does not authorize lock clearing or a replacement attempt. Free disk space does not override a managed-cache budget. Reuse compatible artifacts and passing results. If no safe start is possible, record the deficit and the smallest external action needed.

When the user allows load balancing across machines, treat each linked machine as its own capacity pool. Leases, caches, worktrees, and ledgers on one machine do not describe another. Check the target machine's limits and free capacity before work starts there. Do not assume that it has the same repository, assets, tools, provider logins, or hardware. Do not assume that another machine adds model quota. Place only new work there, and do not move a job that holds a lease. A hold is not a capacity limit, so another machine does not release it. A validation receipt counts only when its exact source and required environment match, so record the machine with each receipt and human-test package.

## Supervise unattended work with an explicit lifecycle

Use durable host scheduling or documented completion wakeups when the user requests supervision beyond the current turn. An ended turn does not keep monitoring by itself. Find any existing supervisor schedule before you create one. Choose a proportionate cadence and a finite window or explicit stop event. State your assumptions and report the actual next run. Make the schedule fail closed: a deadline in a prompt cannot stop it when the model provider is down, so a failed pass must not lead to another run.

When the host can watch pull requests (T3 Code's `watch_pull_request`), use it for PRs that wait on reviewers instead of a polling subagent or a schedule. Bind the watches to a dedicated review thread, not the lead's thread, because per-PR events bury the decisions the user needs from the lead. That thread fixes bot findings and CI failures, and passes up only maintainer change requests and user decisions. A watch sees only GitHub, so it does not notice a stalled owner. Keep or restore the supervisor schedule whenever an owner is still working or a draft needs lead action.

Persist enough context to resume safely: team links, mandate, resource rules, protected activities, queue and blocker state, evidence locations, schedule ID, and stop conditions. Each wake recovers that state and makes a useful decision. It does not relaunch every team. Follow host restrictions on polling delegates and on scheduler use.

Stop when the user ends supervision, the agreed window expires, or no meaningful authorized autonomous action remains. Pending authorized bot review, review findings, and test-package preparation count as autonomous work. So does any draft PR whose open item the lead or an owner can finish. List each draft with who must act, and do not stop while the lead's share is not empty. Do not stop only because a team is slow, results are uncertain, or a temporary resource holder has not finished. Before you stop on blockers, separate conditions that current teams can still release from conditions that need an external decision. Record unresolved work, disable owned schedules and watchers, and report their actual state. Do not leave recurring checks running after the department reaches a human gate.

## Hand back evidence and decisions

Lead with the user's testing or decision queue when action is needed. Then list board moves since the last report, each task over its budget, and the age of the oldest Awaiting review task. Put automatically accepted work in a short completed-work digest, and blocked work in a separate short list. Report department outcomes and material resource decisions without asking the user to inspect every patch. Separate feature work that teams completed from supervisory actions that you performed. Label worker-reported evidence separately from checks you performed. Keep source checks, CPU tests, native runtime, performance, and visual or product acceptance distinct.

Do not narrow an unfinished feature into a smaller success claim. Do not equate passing builds with a usable product, or declare completion because a team wrote a polished handoff. Link the exact artifacts and state unsupported cases. A good handoff makes the next decision easy without requiring the user to read every team transcript.
