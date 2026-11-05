# GitHub Projects board

Use this reference when the department board is a GitHub Project (Projects v2). Discover live IDs before you write. The matching file in `projects/` records known IDs, but live data wins when they differ.

## Access

`gh` needs the `project` scope for writes and `read:project` for reads. Check `gh auth status`. If a scope is missing, ask the user to run `gh auth refresh -h github.com -s project`. Do not work around a missing scope.

## Read state and time

- **Transition history:** issue and PR timelines record each `ProjectV2ItemStatusChangedEvent` with `createdAt`, `actor`, `previousStatus`, `status`, and `wasAutomated`. Use these events for task age and for review-round counts.
- **Time in the current column:** use `updatedAt` on the item's Status value (`ProjectV2ItemFieldSingleSelectValue`).
- **Server-side filters:** `ProjectV2.items(query: ...)` accepts the board filter syntax, for example `status:"In progress"` or `type:Feature`. An unknown qualifier returns no items, not an error. Test a new filter against a known case.
- **Read after write:** filtered results can lag for a short time after a write. Confirm each write by reading the item directly, for example through `issueOrPullRequest(number:) { projectItems }`.

## Write state

- **Move a task:** `updateProjectV2ItemFieldValue` with the Status field ID and the option ID. In the same pass, set the `Since` date field. `Since` is a display aid. The timeline events stay authoritative, because automation moves cards without updating `Since`.
- **Add an item:** `addProjectV2ItemById`. The "Auto-add sub-issues to project" workflow adds sub-issues of a parent that is already on the board.
- **Hierarchy:** link a task issue to its feature with `addSubIssue`, or create it with `createIssue` and `parentIssueId`. A PR cannot be a sub-issue, so record its feature in the board's `Feature` field. Do not use a closing keyword that points at a feature parent, because merging one PR would close the whole feature. Record dependencies with `addBlockedBy`.
- **Feature parents:** set the issue type `Feature` (`issueTypeId` on `createIssue` or `updateIssue`), so that the shared board's Features view shows the parent.
- **Bulk changes:** first save a snapshot of every item and its field values. Then run a dry run against fresh state, apply the change, and read back each item.

## Limits, views, and workflows

- The API does not expose column limits. Enforce limits by refusing to pull into a full column.
- The API can create views and set their name, layout, filter, and visible fields (`createProjectV2View`, `updateProjectV2View`). Sorting, grouping, column limits, and workflow settings need the GitHub UI.
- Built-in workflows move cards too. Check the project's `workflows` before you rely on a column. The API can only delete a workflow, not disable one.
- Never let a board move close a public issue as a side effect. A department board must not have the "Auto-close issue" workflow.

## Outward effects

Each status change adds a timeline entry for everyone who can see the project. On a shared or public board, move cards only for real state changes, and change only items in your own scope. Follow each repository's AI and contribution policy for issues, PRs, comments, and pushes, together with any override recorded in `projects/`.
