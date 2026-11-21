# Better Code skills

Better Code is a collection of skills for GitHub Copilot CLI. It guides coding,
contract drafting, writing, algorithm research, and work tracking. The skills depend on
Superpowers and Technical Documentation; install both companions before using
the Better Code plugin.

## Prerequisites

Install [Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
and Node.js 22.20 or later.

Install [Superpowers](https://github.com/obra/superpowers) using its marketplace:

```bash
copilot plugin marketplace add obra/superpowers-marketplace
copilot plugin install superpowers@superpowers-marketplace
```

Install [Technical Documentation](https://skills.wondel.ai/skills/technical-documentation/)
for Copilot:

```bash
npx skills add wondelai/skills --skill technical-documentation --global --agent github-copilot
```

## Install Better Code

Register the marketplace and install the plugin:

```bash
copilot plugin marketplace add stlab/better-code-skills
copilot plugin install better-code@better-code-marketplace
```

Run `copilot skill list` to check that the plugin's seven skills are available.
Copilot may display the short skill names in this list. Start a new Copilot
session after installation.

## Skills

The plugin provides these namespaced skills:

- `better-code:better-code`: guidance for correct, efficient, reusable code.
- `better-code:contracts`: drafting and formatting declaration contracts.
- `better-code:writing`: clear prose, comments, and documentation.
- `better-code:algorithm-research`: research standard algorithms and libraries
  after implementation planning.
- `better-code:doing`: track started work across repositories and sessions.
- `better-code:upstream-workarounds`: track upstream issues beside local
  workarounds in code, builds, configuration, and tooling.
- `better-code:finish-session`: reconcile session work and capture verified
  learning with the host's available memory facilities.

Better Code invokes Superpowers for development process and its own Writing and
Contracts skills when needed. Writing invokes Technical Documentation for
developer documentation other than declaration contracts. Algorithm Research
complements Superpowers' planning workflow. Install the companions separately;
installing Better Code does not install them.

When you introduce a local workaround, Upstream Workarounds requires a matching
upstream issue and an adjacent explanation. It covers build systems,
configuration, and tooling as well as application code. If upstream filing is
blocked, report the limitation and keep the missing reference explicit.

Invoke `better-code:finish-session` before exiting to reconcile owned tasks,
check for durable learning, and report remaining changes. It doesn't require
a particular memory provider or authorize committing, pushing, or merging.

If you previously installed standalone copies of these skills, remove or disable
the duplicates to avoid ambiguous skill names.

## Track work

Doing keeps a Markdown checklist in `~/.better-code/doing.md`. It records started
work, not a backlog, and keeps completed entries. The file contains task
descriptions, locations, and session details in plaintext on your computer.
Don't include secrets in descriptions.

The plugin's session-start and prompt-submission hooks inject instructions to
track every started task, including single-step work, without invoking another
Better Code skill first. The hooks run on session resume too; continuations
reuse the task's existing entry. Requests only to display the list do not add
entries.

The `Stop` and `SubagentStop` completion hooks invoke the Python helper to read
open tasks owned by the session or subagent. They can block a final response
once to request evidence-based reconciliation, including when the helper fails.
The agent marks verified deliverables done and keeps partial, blocked, failed,
or interrupted work open. A completed review can be done even with findings;
an unfinished repair deliverable keeps its compound task open. The
`stop_hook_active` guard prevents repeated blocking when work must remain open.

Host dispatch and payload compatibility require separate verification. Follow
the [live-host smoke-test procedure](skills/doing/tests/live-host-smoke.md)
before relying on automatic completion blocking in a host/version. Use Doing's
explicit completion checkpoint where live dispatch hasn't been verified.

The hooks use Node.js and return context or checkpoint feedback. They use
session and agent identities, not prompt or transcript contents, and do not
write the ledger or edit your user-level instructions. Automatic
tracking still depends on enabled hooks, the agent following the instructions,
and permission to run the helper and write the ledger. The helper requires
Python 3.9 or later, with no additional Python packages. It supports native
Windows, macOS, and Linux.
On Windows, `~` resolves to your user profile, normally
`%USERPROFILE%\.better-code\doing.md`.

POSIX storage requires directory locking and directory synchronization
(`fsync`). Windows writers use native file locking and keep a persistent
`.doing.lock` file beside the ledger. Don't delete that lock file while agents
are running. Windows files inherit access controls from their parent directory.
Windows `show` reads a snapshot without creating or writing the lock file.
Concurrent updates can appear in the next `show` result.
Keep the ledger on a local filesystem that supports the platform's locking and
replacement operations.

Writes flush temporary file contents before replacement. POSIX then syncs the
ledger directory; Windows requests a write-through replacement with `MoveFileExW`.
These operations do not guarantee recovery from every hardware or filesystem
failure, and Windows does not provide the same parent-directory sync used by the
POSIX implementation.

If the helper reports that an update may have been saved, inspect its
`show all --json` output for the reported task ID before retrying. An error after
replacement does not roll back a visible update.

Delegated ownership stays inside the existing session-metadata field so older
helpers can still read and rewrite the ledger. Older helpers treat this
ownership as opaque; use an updated helper to recover parent/child linkage.

In Copilot, you can invoke the skill explicitly:

```text
/better-code:doing
/better-code:doing show
/better-code:doing show all
/better-code:doing task Investigate the parser
/better-code:doing done
/better-code:doing done TASK_ID
```

Replace `TASK_ID` with the full ID from the `show` table to complete that entry.
Without an ID, `done` completes the identifiable current task.

The slash-command picker shows `show [all] | task <DESCRIPTION> | done [ID]` beside the skill.

To verify automatic tracking, start a task in a new Copilot session and use
`/better-code:doing show all` to inspect its entry after the task finishes.

Invoking the skill without arguments or with `show` lists open work; `show all`
includes completed work. If the current task
can't be identified safely, the agent asks which task to complete. Session and
tmux details are recorded only when available.

## Update

Update the Better Code and Superpowers marketplace catalogs and plugins:

```bash
copilot plugin marketplace update better-code-marketplace
copilot plugin update better-code@better-code-marketplace
copilot plugin marketplace update superpowers-marketplace
copilot plugin update superpowers@superpowers-marketplace
```

Update Technical Documentation with the same tool used to install it:

```bash
npx skills update --global technical-documentation
```

Start a new Copilot session to use the updated skills.

## License

[Boost Software License 1.0](LICENSE).
