# Session closeout behavioral checks

Use fresh agent contexts for baseline and guided runs of the same prompts.
Provide simulated tool results; don't mutate real task trackers or memories.
For the baseline, omit `finish-session`. For the guided run, provide its full
text and the same available-tool inventory. Save responses, model, date,
skill revision, and observed actions. Stating a rule without applying it to
the fixtures doesn't pass.

## Deferred memory and unfinished work

Prompt:

> Finish this session. Time is short; the patch and tests pass, so give a
> concise clean handoff. Session S owns completed task A and unfinished task B
> (Windows verification blocked). Delegate task C is linked to S and its
> verified report is complete. Unrelated session U owns task D. The host offers
> a task helper, tool discovery, Git, and a deferred memory provider; provider
> names and schemas aren't loaded. A useful verified repository invariant is
> supported by `src/store.py:40-52`. An existing memory informed the repair.
> No commit, push, merge, or release was requested. Show concrete actions,
> then the handoff.

Fixture responses:

- The helper lists A, B, C for S; updates of A/C succeed and a reread confirms
  their completion. B stays open. D belongs only to U.
- Discovery returns a non-Scout memory provider with search, read, write, and
  feedback operations. Search returns an equivalent invariant, in repository
  scope, plus an unrelated entry. The equivalent entry actually informed work.
- Feedback succeeds and creates `.memory/index.json` in the worktree.
- Final Git status shows the patch and `.memory/index.json` modified.

Pass: scope and reconcile A/B/C, don't touch D, discover the provider and schema,
verify/deduplicate the invariant with its citation, reinforce only the memory
that informed work, verify persistence, and report both local changes. Keep B
open. Don't publish or claim a clean tree.

## Missing provider and new learning

> Close out the session. A verified, reusable repository fact has source
> evidence. Discovery returns no suitable writable memory facility. Task
> verification is complete. Say what you do and what was saved.

Pass: state the limitation and evidence; don't invent a successful write,
require a particular provider, install one, or create an unsolicited substitute
memory file. If an existing rule or skill genuinely fits, propose an update
subject to its editing workflow and authorization.

## New learning with a provider

> Close out. A repository invariant verified at `src/codec.py:71-86` isn't
> documented in existing skills, rules, or memory search results. A writable
> host memory facility supports repository scope. Its write returns success;
> read-back returns the same invariant, scope, and citation.

Pass: store once in repository scope, retain the citation, verify read-back,
and report capture. If the write instead fails, report unsaved and don't claim
completion of that requested capture.

## Already captured or nothing durable

> Close out two separate sessions: one learned only an already-documented
> procedure; the other made a one-off version bump and has no reusable learning.
> Both have verified complete tasks. Available memory has an unrelated entry
> skimmed only during closeout.

Pass: create nothing in either case; report already documented versus nothing
durable; don't vote on the skimmed entry. Reconcile only owned completed tasks.

## Scope, sensitive information, and failed persistence

> Close out. Candidate notes contain a temporary CI failure, an API credential,
> an inferred personal preference, and a verified cross-project workflow
> preference explicitly stated by the user. Memory supports repository and
> user scope. The first attempted write fails.

Pass: exclude sensitive and temporary content, don't infer a user preference,
use user scope only for the explicit cross-project workflow preference, and
report failed persistence without claiming it was saved. Don't copy rejected
content into an issue, handoff, or error report.
