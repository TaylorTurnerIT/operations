# Mailbox

Holds small department memos outside T3 and sends them as one digest. No LLM runs in the mailbox. Status: pilot, started 2026-11-05. Design and reasons: see the "Mailbox" entry in [proposals](../proposals.md).

## Use

```
python3 -I mailbox/mail.py send --to DEPT --from SENDER [--urgent] --body "text"
python3 -I mailbox/mail.py send --to DEPT --from SENDER --body -     # text from stdin
python3 -I mailbox/mail.py status
python3 -I mailbox/mail.py flush --dry-run                           # show what would go
```

`DEPT` is a department name in [directory.md](../directory.md). The mailbox sends to that row's lead thread.

## When a digest goes out

A digest goes to one lead thread when any of these is true:

1. The department has 5 or more pending memos (`OPS_MAILBOX_N`).
2. One pending memo is urgent. `send --urgent` flushes at once, with every other pending memo for that department.
3. The oldest pending memo is 60 minutes old (`OPS_MAILBOX_AGE`). The launchd job `local.operations-mailbox` checks every 5 minutes. This age cap means no memo waits forever.

## Facts

- Storage: `~/.local/share/operations-mailbox/mailbox.sqlite`. Each memo has an `mb:` id. A sent memo records the T3 `messageId` of its digest.
- Sender: the `t3 thread send` command. T3 marks these messages `createdBy: agent`, so a digest never carries Taylor's authority. Tested 2026-11-05.
- A busy receiver gets the digest as its next turn. An idle receiver starts a turn at once.
- A crash during a send can cause one duplicate digest after 10 minutes. It cannot lose a memo.
- Departments keep using `t3_thread_send` for anything that needs an immediate reply. The mailbox is for memos that can wait.

## Install the flush job

```
cp mailbox/local.operations-mailbox.plist ~/Library/LaunchAgents/
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/local.operations-mailbox.plist
```

Remove it with `launchctl bootout gui/$(id -u)/local.operations-mailbox`.

## Open

- Mail rules say to cite a memo by its T3 `messageId`. A memo in a digest has an `mb:` id instead. This needs a rule change, so it waits for the pilot review and Taylor.
- No receipts yet. `status` shows `sent`. The receiver does not confirm.
