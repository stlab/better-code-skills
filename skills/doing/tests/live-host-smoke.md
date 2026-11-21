# Verify completion hooks in a live host

Use this procedure to verify Doing hook dispatch in Copilot CLI or Claude Code.
It supplements command-level tests; passing unit tests or replaying synthetic
JSON doesn't establish that a host dispatches an event or honors its response.
Run each host separately and retain its observed versions and payload fields.

## Before you begin

Use a disposable local workspace and plugin copy, Python 3.9 or later, Node.js
22.20 or later, and an authenticated host. Don't run these cases against your
normal task ledger or change your global hook configuration.

In the test terminal, point both `HOME` and `USERPROFILE` at the same disposable
home before starting the host or helper. Set `COPILOT_HOME` to a separate
disposable directory for Copilot configuration and state. Authentication
must use the host's supported mechanism; don't copy credentials into test
artifacts. If profile isolation prevents authentication, record that blocker
rather than reverting to the real home.

Load the disposable plugin through the host's supported local-plugin mechanism.
Copilot CLI 1.0.114 documents `--plugin-dir PLUGIN_COPY` for this purpose.
Replace `PLUGIN_COPY` with the absolute path to the disposable copy. For Claude
Code, consult the installed version's documentation rather than assuming
Copilot flags apply.

Record the repository revision, OS, filesystem, host version, Python and Node
versions, plugin path, and isolated profile paths. Verify that the helper's
`show --json` reads the disposable ledger before creating fixtures.

## Establish command-level behavior

From the repository root, run:

```bash
python3 -m unittest discover -s skills/doing/tests -v
```

Expect the suite to pass; native Windows storage tests skip on POSIX. These
tests execute registered commands and cover task scoping, guard behavior,
interpreter selection, helper failure, and ledger immutability.

## Observe real dispatch

Use the installed host's supported hook tracing, or instrument only the
disposable plugin's registered completion commands. A transparent recorder
must pass the original stdin to `hooks/doing-stop.cjs`, preserve its exit code,
stdout, and stderr, and record its response. Don't change production hooks.

Record only the fields needed for this test: `hook_event_name`, `session_id`,
`agent_id`, and `stop_hook_active`, including each value's type and whether
it is absent. Don't save prompts, responses, transcripts, credentials, or
unfiltered payloads. Record host receipt and handling separately from the
command's output: a JSON block decision alone isn't proof of enforcement.

If neither tracing nor transparent instrumentation is available, mark payload
and dispatch verification blocked. Don't fill absent fields with synthetic
values. This is a manual live-host test, not a supported automated host harness.

For each callback, compare disposable-ledger bytes immediately before and
after the hook command, before the agent can run a helper mutation. They must
match. Separately record agent-issued `task` and `done` commands so they aren't
mistaken for hook writes.

## Run the cases

Use actual observed host IDs, not the literal placeholders in this command
example. From the disposable plugin directory, create fixtures with:

```bash
python3 skills/doing/scripts/doing.py task "Smoke open work" \
  --session-id SESSION_ID
python3 skills/doing/scripts/doing.py show --json --session-id SESSION_ID
```

Replace `SESSION_ID` with the ID from the host callback. Save each returned
task ID. On native Windows, use `py -3` instead of `python3`.

Run these checks in order:

1. **Empty scope:** End a live turn without owned open work. Confirm the
   completion callback returns `{}` and doesn't write the ledger.
2. **Owned unfinished work:** Create an owned task and an unrelated session's
   task. Ask the agent to stop with the owned deliverable explicitly blocked.
   Confirm the callback identifies only owned work, the host surfaces
   reconciliation feedback, and the agent leaves the unfinished task open.
3. **One-block guard:** Let that same stop attempt continue after reconciliation.
   Confirm the real subsequent payload supplies boolean `stop_hook_active`
   as `true`, the hook returns `{}`, and the host permits a final response with
   work still open. Record a failure if it loops, ignores the decision, or
   doesn't supply the required guard; don't fake the field to get a pass.
4. **Verified completion:** Complete a separate small deliverable. Confirm the
   agent verifies it, explicitly runs `done TASK_ID`, and a scoped helper read
   confirms completion before the final success report.
5. **Delegated handoff:** Start a real child and sibling through the host.
   Create parent-owned, child-owned, and sibling-owned fixture tasks; link
   delegates with `--parent-session-id PARENT_ID`. Confirm child stop feedback
   uses its actual `agent_id` and excludes parent/sibling work. Keep one child
   task legitimately open for the resume check.
6. **Coordinator resume:** Resume that actual coordinator through the host.
   Confirm `show --json --session-id PARENT_ID` recovers parent work and linked
   open child work, excluding unrelated sessions. Don't mark the unfinished
   compound task complete merely because the child delivered a report.
7. **Helper failure:** In a separate disposable profile, create a malformed
   test ledger. Trigger a real completion callback. Confirm visible
   helper-failure feedback, unchanged ledger bytes, and guard-based escape
   rather than an empty-task success or blocking loop.

For `TASK_ID` and `PARENT_ID`, substitute saved IDs. If a host doesn't dispatch
a required event or lacks a field, record that case as unsupported or failed
with observed evidence. Use the explicit Doing completion checkpoint before
final responses and handoffs on that host; don't silently assume another event
has equivalent payload or block semantics.

## Record results

Keep a record per host and case with these fields:

| Field | Evidence |
| ----- | -------- |
| Environment | Exact versions, revision, filesystem, disposable profile. |
| Registration | Loaded plugin and actual event spelling. |
| Input | Allowlisted fields, types, and absent fields. |
| Output | Hook decision, exit code, and sanitized helper diagnostic. |
| Host behavior | Feedback visibility, bounded retry, final response or loop. |
| Ownership | Expected versus observed task IDs, including resumed delegates. |
| Read-only check | Before/after ledger-byte comparison for each callback. |
| Outcome | Pass, failure, unsupported, or blocked; manual checkpoint needed. |

As of the 2026-11-21 investigation, no live-host result was obtained for either
host. Copilot CLI 1.0.114 documents Claude-style `Stop` mapping and separately
lists native `subagentStop`; the retrieved documentation doesn't establish
Claude-style `SubagentStop` payload compatibility. Claude Code wasn't installed
on the investigation host. [Issue #8](https://github.com/stlab/better-code-skills/issues/8)
remains open until actual live evidence covers its acceptance criteria.

Remove only the specifically identified disposable fixtures after preserving
sanitized results. Never delete or complete tasks in the normal profile.
