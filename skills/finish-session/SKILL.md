---
name: finish-session
description: Use when asked to finish or close out a session, prepare an end-of-session handoff, or check for incomplete tasks and durable learnings before exiting.
---

# Finish a session

Reconcile the current session's work and capture verified, reusable learning
in the facilities the host provides. Closeout doesn't authorize merging,
releasing, deleting work, committing, or pushing. Honor existing explicit
authorizations and workflow rules.

## Reconcile work

Invoke `better-code:doing` (or its installed equivalent) if it isn't active.
Use its completion checkpoint and inspect other available task trackers for
this session's requested work, blockers, and follow-ups. Recover owned task
IDs and linked delegates; don't reconcile unrelated sessions.

Compare each requested deliverable with verification evidence. Mark only
verified complete work done, then confirm the update. Keep partial, blocked,
failed, and unfinished work open, including unfinished parts of compound tasks.
Report unavailable trackers or failed updates rather than implying reconciliation.

## Classify learning

Review the session's evidence, not just its last response. Choose the destination
by what the learning helps someone do:

| Learning | Destination |
| -------- | ----------- |
| Reusable procedure | Existing relevant skill, or a new skill if needed. |
| Enduring guidance | Existing applicable rule, avoiding conflicting duplicates. |
| Verified scoped fact | The host's suitable memory facility. |
| Already documented or no durable learning | No new artifact; say which applies. |
| Temporary task status, secrets, sensitive data, or unsupported inference | Exclude from durable learning. |

Preserve repository `file:line` citations or exact source evidence. Use
repository scope for contributor-wide facts and user scope only for explicit
cross-repository workflow preferences. Clarify ambiguous scope before storing.
Don't infer personal preferences or copy sensitive material into metadata,
citations, or the handoff.

## Discover and persist

Discover the host's available memory facilities, including deferred tools
through its tool-discovery interface. Read the actual operation schemas.
Don't assume Scout or another provider is installed, required, or writable.
Search and inspect relevant existing skills, rules, and memories before
creating anything. Update or reuse matching material instead of duplicating it.

Use the applicable skill-authoring or rule-editing workflow for those changes;
invoke `better-code:writing` when editing prose. An unavailable memory provider
isn't permission to install one or create a substitute memory file.

Where supported, reinforce or correct memories that actually informed the work,
using verified evidence. Don't vote on entries merely skimmed during closeout.
Verify writes and feedback from provider results; read back content, scope,
citations, or feedback state where supported. Resolve uncertain persistence
before retrying. Report failures or unavailable facilities without claiming
anything was saved.

## Verify the handoff

After all capture operations, inspect local changes again, including untracked
files and metadata created by memory tools. Don't rely on an earlier clean status.
Verify requested captures persisted, then perform Doing's completion checkpoint
for this closeout task too. A requested capture that remains incomplete keeps
its task open.

Give a concise handoff with these fields, omitting empty detail:

- Unfinished work: task IDs, blockers, and requested follow-ups.
- Learning: captured and verified, already documented, nothing durable, or unsaved
  with the reason.
- Local changes: code, documentation, and memory metadata; publication status.

For example: "Windows verification remains open (task B). Reused the existing
repository invariant; feedback confirmed. `.memory/index.json` is modified;
no commit or push."
