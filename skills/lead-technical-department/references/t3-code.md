# T3 Code operating notes

Use this reference only for T3-backed department leadership. Discover the live tool schemas and follow current host instructions. Do not rely on copied signatures or hardcoded provider settings.

## Map existing teams

- `t3_thread_list`: list each in-scope project, or use `allProjects` and filter the results. Paginate. Start with active, unsettled teams. Do not reopen settled, archived, or snoozed work because of its age. Results can include subagent threads. Use `parentThreadId` to separate top-level owners from their descendants.
- `t3_thread_read`: read a thread before you act on it or report its state. A default read starts at position 0. `itemCount` counts all timeline items, not only messages. Use observed `position` and `nextPosition` values as cursors. If an empty response returns a null cursor, keep the old cursor. For truncated text, use `itemId` with `textOffset`.
- **First adoption:** page through the `messages` view from position 0 with a small `maxCharsPerItem`. Find every user message. Read the full text of each message that sets scope, a hold, or an approval. After this baseline, read only from stored cursors.
- **Thread state:** metadata includes `status`, `runtimeMode`, `settled`, `pendingRequestCount`, and recent runs. The statuses `interrupted`, `cancelled`, `rolled_back`, and `failed` do not show who stopped the run. Check the run timeline, recent user messages, and `settled` before you classify the cause.
- A running status needs no routine steering. If progress is unclear, inspect a small activity tail for actual commands or blocking events. Avoid repeated full-history reads. Do not expose unnecessary internal activity in reports.
- Keep each thread's `link` for user-facing references. A title or raw ID is not a substitute.

## Handle pending questions

`t3_pending_request` can list, read, and answer a team's pending questions. Its list omits permission approvals, and it cannot approve them. A thread can therefore wait on an approval that this tool does not show. Answer a question only when the user's instructions already decide the answer, and cite that instruction. Route other questions and all permission approvals to the user's decision queue with the thread link.

## Continue through the right owner

Before you send, check the team's current run and its queued messages with `t3_queue` `list`. A queued user message can already contain the next instruction. Do not edit, reorder, cancel, or promote the user's queued messages. To change your own queued message, edit it. Do not send another one.

Use `t3_thread_send` for authorized continuation or necessary coordination of an existing top-level team:

- `auto` starts an idle thread, steers a running turn, or queues a message.
- `steer` updates a running turn.
- `queue` creates another turn.
- `restart` interrupts the running turn and restarts it.

Choose the mode from the current state, so that a harmless status message does not start duplicate work. Treat `restart` and `t3_thread_interrupt` as interruptions. Use them only when interruption is necessary and authorized. When delivery is uncertain, use a stable `clientRequestId` and verify delivery. Do not generate another request.

The send contract says that the target cannot run with broader modes than the sender. Before you send, compare your `runtimeMode` and `interactionMode` with the team's values. If yours are narrower, do not send. Report the mismatch to the user, because the send could narrow the team's next turn or fail.

A team can have its own delegated tasks. Let its owner recover and manage them. Use the host's delegate completion notifications and `next` instructions. Do not turn recurring supervision into forbidden delegate polling. A new independent delegate review follows the host's delegate workflow. It is not a message to an old reviewer. Ask the accountable owner to complete the appropriate bot-review stage within its existing authority. Do not route every completed implementation to the user, and do not create a competing department review team. Check the actual review content and exact source coverage before you mark automated acceptance.

To wait for one team, `t3_thread_wait` with `untilDelegatedWorkSettled` shows when its run and its delegated work settle. Follow its `next` instruction on timeout. A settled run does not prove that a command released a resource.

Do not use `t3_thread_launch` or `create_threads` unless the user asked for new top-level threads. Do not change provider, model, options, runtime permissions, or worktree as a side effect of supervision. Local identifiers do not identify remote threads.

## Other machines

`t3_environment_links` lists linked machines and shows whether each one answers. Only the user can link a machine, and the user chooses its access. Do not work around an unlinked machine or a narrow link. To act on a linked machine, pass its `environmentId` to a T3 tool. For a delegate, pass `target.environmentId`. Identifiers that come back belong to that machine, so also pass `threadId`.

Before you place work, read the machine with `t3_environment_read` and check its projects with `t3_project_list`. If the repository is missing and the work needs it, use `t3_project_clone` and then `t3_project_create`, both with the same `environmentId`. Start new work on the target machine. Do not assume that an existing thread or its worktree can move there.

## Resource handoff example

Suppose capacity is full. Geometry has three short checks waiting, and an animal team has a longer suite waiting.

Tell both owners the order: Geometry goes first, then Animals. Geometry tells the animal owner when it finishes. The animal owner checks capacity and starts.

This is an order for one contested resource, not a standing approval process. If capacity is not full, send no message, because each owner starts on its own. This messaging is not an atomic scheduler.

## Durable supervision

When the user requests it, `schedule_task` can bind recurring work to the supervisor thread. Its schedule is structured data. During setup or recovery, inspect existing schedules to avoid duplicates. `list_scheduled_tasks` defaults to the current thread's project, so pass `projectId` when the schedule belongs to another project. Use a stable `clientRequestId` when you create a schedule. During a scheduled run, use the supplied exact schedule ID. Do not list tasks to find it.

Store the schedule ID, cadence, next run, scope, acceptance routes, known completed teams, human-test packages, unresolved blockers, and stop condition in the continuation instructions.

Fifteen minutes can suit long builds. It is not a fixed requirement. A scheduled prompt tells the supervisor to inspect current state, resume only meaningful authorized work, preserve resource queues and human holds, and stop cleanly. Reading a provider error does not prove that its subprocess stopped.

T3 schedules have no end-time field. An `interval` task keeps firing when the provider fails, even after a deadline in its prompt. Mudcrab's supervisor made 34 failed attempts this way, 28 of them after its deadline. For a finite window, use a `{type:'once',runAt}` task, and let each successful pass arm the next run before the deadline. A failed pass then arms nothing. Check `nextRunAt` in the result. This re-arm worked across a 12-hour Mudcrab window on 2026-11-02. Tell the user that this stops without an alert.

When supervision ends, disable the owned task with `schedule_task` and `enabled:false`. This pauses the task. Verify the returned disabled state and next-run value. No replacement schedule or thread is needed. Do not use sleeping loops for durable supervision.

This workflow does not replace PR-specific host rules. Use required PR links and watches when authorized PR work requires them. Follow completion-wakeup instructions, and remove owned watches at final handback. Scheduled thread supervision is not a workaround for a prohibition on polling delegates or PR checks.

## OpenCode children and permission prompts

Measured 2026-11-13 by the Mudcrab lead, and confirmed in T3's own code. T3 builds OpenCode permission rules from the thread's runtime mode. Only `full-access` allows everything. In every other mode (`auto`, `auto-accept-edits`, `approval-required`), T3 sets OpenCode to "ask" for these actions: `bash`, `edit`, `webfetch`, `websearch`, `codesearch`, `external_directory`, and `doom_loop`. `auto-accept-edits` allows edits only. The actions `read`, `glob`, `grep`, `lsp`, `todowrite`, `task`, `skill`, and `question` never prompt, as long as the path is inside the worker's worktree.

- A project `opencode.json` cannot lift the prompts. T3 writes its rules into the session, and they win.
- A parent cannot approve a child's prompt. `t3_pending_request` answers questions only. A person approves in the T3 UI.
- A child cannot have a broader mode than its parent, so a lead in `auto` cannot start a `full-access` OpenCode child. Only Taylor can change a thread's mode, in T3.
- Codex and Claude children do not prompt in `auto`.

What to do:

1. **Read-only research needs no shell.** Give the OpenCode worker a bundle inside its worktree: copy the files it must read, and write command output it needs (for example `git log`) into files first. Tell it to use only read, glob, and grep and to return findings in its final message. Tested on 2026-11-13: a DeepSeek V4.1 Flash child in `auto` mode ran this way with no prompt, and its answers were correct.
2. **Work that needs a shell** (tests, builds, edits with checks) goes to a Codex or Claude child in `auto`, unless Taylor grants `full-access` to a runner thread. A `full-access` OpenCode worker has the host user's full authority, and the brief is its only guard.
