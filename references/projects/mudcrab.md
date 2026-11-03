# Mudcrab

Repository: `Mudcrab-Team/mudcrab` (public organization repository). Read its `docs/AI_POLICY.md`, `docs/contributing/labels.md`, and `docs/contributing/workflow.md` before you act on GitHub.

## Boards

**Feature level: "Mudcrab Project"** (org project #1, `PVT_kwDOFAnoMs4BlQ-M`). This board is public and shared with the team.

- Columns: To do, In progress, In Review, Merge Ready, Done.
- The "Features" view (#6) shows `type:Feature`. Feature parents use the org issue type Feature (`IT_kwDOFAnoMs407QpY`).
- Team automation adds every issue, sub-issue, and PR, and it sets most statuses. Our sub-issues appear here too. The Features view hides them.
- Change only Taylor's feature parents. Do not change other people's items, the team's workflows, or the "Backlog" view.
- Set a feature to In Review only when all its open tasks are in Awaiting review or later. Leave Merge Ready to maintainers. The "Item closed" workflow sets Done.

**Task level: "Technical Department"** (Taylor's private project #4, `PVT_kwHOB9IJbc4Bpw7F`, https://github.com/users/TaylorTurnerIT/projects/4).

| Field | ID | Options |
| --- | --- | --- |
| Status | `PVTSSF_lAHOB9IJbc4Bpw7FzhoK_7U` | Backlog `f75ad846`, Ready `36bb4019`, In progress `47fc9ee4`, Bot review `a422d9d0`, Awaiting review `7cf41265`, Human test `4124a6b0`, Done `98236657` |
| Flag | `PVTSSF_lAHOB9IJbc4Bpw7FzhoLAGQ` | Blocked `8f952360`, Held `2b6b8225` |
| Since | `PVTF_lAHOB9IJbc4Bpw7FzhoLAGE` | Date of the last status change |
| Review rounds | `PVTF_lAHOB9IJbc4Bpw7FzhoLAGI` | Returns from Bot review |
| Owner | `PVTF_lAHOB9IJbc4Bpw7FzhoLAGM` | Link to the accountable team thread |
| Feature | `PVTF_lAHOB9IJbc4Bpw7FzhoLfN0` | Parent feature issue number, for example `#471`. Use it on PR cards, because a PR cannot be a sub-issue. |

- Views: "Board" (columns by Status) and "Aging" (table, filter `-status:Done,Backlog`).
- Workflows that are on: "Item added to project", "Pull request linked to issue", "Item closed", "Pull request merged", and "Auto-add sub-issues to project". The API does not show their target columns. GitHub's defaults set Backlog, In progress, Done, and Done. The Status update kept the original option IDs for Backlog, In progress, and Done so that these targets stay valid. If a card moves unexpectedly, check the workflow settings in the GitHub UI.
- "Auto-close issue" was deleted on purpose. Do not add it again.

## Status mapping from Mudcrab signals

| Signal | Task column or flag |
| --- | --- |
| Active owner, no PR yet or draft PR | In progress |
| Draft PR with no active owner | Ready. Nobody is working on it, so it is not In progress. |
| Frozen source under independent bot review, PR still a draft | Bot review. The PR becomes ready when review passes and only maintainer review remains. |
| PR marked ready for review, with or without `S-ready-for-review` | Awaiting review |
| Open PR whose only open item is Code review (maintainers) | Not a draft. Mark it ready (`gh pr ready`), and remove "why this is a draft" from the body. CodeRabbit then reviews it, and the owner answers each comment. |
| Label `S-waiting-on-author` | In progress. This does not add a review round. |
| Label `S-blocked` | Flag Blocked, in the current column |
| PR merged | Done |
| Closed without a merge | Done only with a recorded reason. Otherwise, return the task to Backlog. |

## Taylor's local override of AI policy rule 3

Taylor is a core developer. On 2026-11-01, Taylor granted this override for agents that work for Taylor. It is local and is not part of the public policy.

- Agents can open issues and PRs, and push to their own PR branches, without an OK for each item.
- Only a human can request an entire new feature. Inside a requested feature, agents can create tasks, sub-issues, and PRs.
- Comments to people still need Taylor's OK. Replies to bots are allowed when they are labelled as AI-written.
- Rule 1 still applies. Put the AI-assistance note at the top of each issue and PR. Add `Assisted-by:` or `Co-authored-by:` trailers to commits.
- Rule 4 asks for about five open PRs per contributor. Taylor accepts a larger review backlog, so that the visible queue draws in more reviewers. Awaiting review therefore has no limit.
- Rule 5 still applies. Search issues and PRs before you start. If someone else claimed the area, send that to Taylor's decision queue, because the required comment needs Taylor's OK.

## Issues, proposals, and media (added 2026-11-03)

- Taylor approved batch creation of atomic issues from Luna drafts ("Just go for it... I trust your judgment"). Before you create any, check each draft against the live issue list, `origin/main`, and the file lists of open PRs. The PR check caught a duplicate of #419 and an overlap with #259. Skip or redirect those. Put the AI-disclosure line at the top, use existing labels only, add `help wanted` or `good first issue` where the draft class says so, and link each issue to its parent as a sub-issue. The sub-issue call is `gh api repos/R/issues/PARENT/sub_issues -F sub_issue_id=<integer id>`. It needs `-F`, not `-f`. Record the created numbers in `created.md`.
- Items that need a maintainer decision stay unlabelled and go on the stage issue as a decision checklist.
- `proposal` issues with no owner or PR get `help wanted` and keep `proposal`. Taylor approved this on 2026-11-03. Do not comment on them.
- GitHub has no upload API for comment images. To show GIFs in a PR comment, push them to a throwaway branch such as `media/<topic>-<date>` through the contents API, embed the raw URLs, and say in the comment that the branch is not for merge. Say what the media shows and what it does not show, for example a local prototype that is not the PR's code.
- A watched PR group: use `watch_pull_request` on every Taylor-owned PR that waits on maintainers. Draft and stacked PRs wait until they are ready.
- Implementation delegates started by an `auto` lead cannot write Git locks in their worktree or the shared storage lock (`Operation not permitted` on the index lock and `registry.lock`). They can read, reason and write handoff files, so use them for review, audit and analysis only. Real implementation, builds and pushes need a `full-access` thread, which the lead cannot grant. Ask Taylor for one, or reuse an existing full-access owner. Learned on 2026-11-03 with #485, #486.
- CodeRabbit's free quota is about two reviews an hour. Mark PRs ready one at a time, spaced out. If it says "Review limit reached", wait for the reset, then post `@coderabbitai review` (AI-labelled) if it does not resume.
- Owner threads that run `full-access` reject sends from an `auto` lead, and the lead cannot change their model. Owners write status files and notice files in `handoffs/`. Taylor changes those threads in T3.

## Review panel

On 2026-11-02, Taylor said: "Luna + Haiku is our review roster." The roster is GPT-6 Luna (`codex/gpt-6-luna`) and Claude Haiku 5.5 (`claudeAgent/claude-haiku-5-5`). Sonnet is not used for review unless Taylor changes this after seeing usage patterns. The older six-model roster is retired.

- Use both reviewers on each review. When Haiku is unavailable or Claude usage is low, Luna alone counts.
- A missing Haiku review is not a gate, a blocker, or an open acceptance item.
- A review is blocked only after an actual Luna attempt failed. Record that failure. Do not report a block without one.
- Keep both review angles: correctness and adversarial missed cases.
- A passing Luna review of the exact current source counts. Do not review unchanged source again.
- This rule does not change native, human, repository, or adoption gates, or the review-round cap. It does not authorize paid fallbacks or resets.

## Load balancing

On 2026-11-01, Taylor allowed load balancing from the M4 Pro, which runs the department's T3 server, onto Taylor's second Mac. The second Mac is the M1 MacBook (Tailscale `m1-macbook`). Taylor also uses it as a client, so treat an interactive session there as protected. The department lead decides what runs where.

- Its environment ID is `20659a2f-1b36-4ab6-9cd6-b191b8f5dd09` (label "Taylor's MacBook Pro"). Taylor chose `full-access`. The link expires on 2026-12-02. Check that `t3_environment_links` shows it as reachable before you use it. If the link expired, ask Taylor to link it again.
- Before you place work, verify its project registration, toolchain, assets, caches, provider logins, free disk, RAM, and GPU. Record the result in the resource ledger.
- Each machine has its own locks and limits. Capacity on one machine says nothing about the other.
- Until Taylor sets other numbers, the 60 GiB cache budget and 20 GiB reserve also apply to the second Mac. These numbers are limits, not a claim that the capacity exists.
- Balancing does not release a hold. The 77,228 MiB full-world hold, the OpenCode start hold, the MATO and snow exclusions, and the paid and reset limits stay in place. If the second Mac's capacity could change one of these, tell Taylor.
- Record the machine with each validation receipt and human-test package. A receipt from one machine does not prove native behavior on the other.
- GitHub rules do not change. Commits from the second Mac still need the AI trailers.

## Team autonomy

On 2026-11-02, Taylor said: "The team leads cannot be micromanaging. Trust the team members." This section wins over `WORKFLOW.md`, the ledger's grant fields, and any stored supervisor prompt where they conflict.

- The department lead does not issue per-command grants or pin source, environment, or cache keys for a team. Retire those fields from routine work.
- Owners run their own commands and reviews without asking when they stay inside these limits for each machine: two managed commands and two review panels. The M4 Pro has a 96 GiB build cache and a 40 GiB reserve (Taylor raised these on 2026-11-02). The M1 MacBook keeps 60 GiB and 20 GiB and is review-only. They use the existing wrappers and locks.
- A key or environment difference is not a stop. The owner records it and runs the command, unless it puts a limit or a result at real risk.
- A native or GPU run still uses the existing qualified watchdog, and only one runs at a time on each machine. It does not need a lead's grant.
- Owners report results and remaining gates, not each step. The lead steps in only for a broken limit, a time budget overrun, a conflict between teams, or a decision that needs Taylor.
- Taylor's holds and the limits on merges, comments to people, and paid or reset actions do not change.

## Unattended supervision

On 2026-11-02, Taylor told the lead directly: "You need to stop focusing so much on my giving permission to do things. I gave you permission when I said to work on a feature. From there, you should see it through to the moment I can merge it with Main or test as a user." Supervision therefore has no fixed end time. It ends when no requested feature has autonomous work left, or when Taylor stops it.

- Use the existing supervisor task as a `once` schedule. Each successful pass arms the next run about 30 minutes after that pass ends.
- A failed pass arms nothing, so supervision stops by itself. No alert goes out, and this limit is accepted for now. A later message or wakeup can restart the chain.
- `department_health.py --unattended` tests the older watchdog contract. Do not declare a watchdog that does not exist.
- The two-command and two-panel limits apply to each machine, unless actual capacity requires less.

## Preapproved current work

On 2026-11-01, Taylor said: "Consider current work preapproved." These features therefore count as human-requested. Any other new feature needs Taylor. PR numbers are hints, so verify them.

| Team | Feature | Parent | Known PRs |
| --- | --- | --- | --- |
| `animals` | Animal idle animation | #471 | |
| `converter_fidelity` | Converter fidelity | #498 | |
| `cubemaps` | Authored NIF environment and eye cubemaps | #490 | |
| `water` | Authored water consumers | #364 if its scope fits, otherwise a new issue | #499 |
| `local_lights` | Authored local-light parameters | #364 if its scope fits, otherwise a new issue | #488 |
| `interior` | Authored interior lighting | New issue | #480 |
| `materials` | Per-record materials. Snow is out of scope, and #435 belongs to eclepso. | New issue | #484 |
| `geometry` | Virtualized geometry | New issue that references BimingtonBill's #316 | #500 |
| `movement` | Movement defaults | #221 if its scope fits, otherwise a new issue | #246 is by 4cecoder |
| `integration` | Not a feature. It is the cross-PR integration lane. | None | |

Taylor's other open PRs on 2026-11-01 are also preapproved: #214, #253, #259, #260, #421, #460, #461, #479, #481, #482, #483, #485, #486, #487, #489, #492, #493, #494, #496. Group related PRs under one feature when that is clear. A standalone fix stays a task with no feature parent.
