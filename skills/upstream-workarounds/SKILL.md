---
name: upstream-workarounds
description: Use when introducing or changing a local workaround for an upstream dependency defect or documentation gap in code, build systems, CI, configuration, or tooling.
---

# Upstream workarounds

Track the responsible upstream issue and explain the local workaround beside
the affected code or setting. A passing local repair, commit message, or memory
doesn't replace upstream tracking or the adjacent explanation.

## Track and annotate

Before calling the repair complete:

1. Identify the responsible component and affected version. Verify the defect
   or gap with a reproducer, diagnostic, or source evidence. Distinguish an
   implementation defect from missing documentation.
2. Search that component's issue tracker for a matching issue, including closed
   issues that might identify a fixed version. Reuse a matching issue rather
   than filing a duplicate.
3. If no match exists, open an actionable issue with the affected version,
   expected and observed behavior, reproduction steps, supporting evidence,
   and local workaround. Respect access, disclosure, and authorization rules;
   don't publish private code or sensitive data.
4. Add an adjacent comment containing the verified issue URL and the specific
   reason for the workaround. For a temporary workaround, state the condition
   for removal or simplification and how to verify it.
5. Validate the repair and its supported configurations. Report the upstream
   reference and any unresolved tracking or validation gap.

If filing is blocked by access, permissions, policy, or unavailable tools,
report the exact limitation and prepare an actionable issue draft. Add the
local explanation and mark the upstream reference as missing; don't invent a
URL or imply an issue was created. Verify creation succeeded before using its
returned reference.

Use the file's supported comment syntax. If comments aren't supported, put
the explanation in the nearest maintained companion document and identify the
exact file and entry. Don't break configuration syntax to add an annotation.

For example, beside a conditional build override:

```cmake
# [knowledge] vendor/tool 2.4.0 discovery selects a Unix archive on Windows.
# Tracked by UPSTREAM_ISSUE_URL. Remove this override after every supported
# tool version selects the Windows library and the reproducer passes without it.
```

Replace `UPSTREAM_ISSUE_URL` with the verified tracker URL, not a guessed link.
State a fixed version only when upstream evidence establishes it.

## Scope and common mistakes

Use these distinctions when deciding what to track:

| Situation | Required action |
| --------- | --------------- |
| Temporary dependency workaround | Track upstream; annotate reason and verified removal condition. |
| Local guidance compensates for upstream documentation | Track the documentation gap, not an invented implementation defect. |
| Correct, permanent dependency usage | Keep the requirement; no issue is needed merely for ordinary usage. |
| Upstream documentation improves | Remove obsolete compensating guidance, not still-required code or includes. |
| Deadline pressure or already-passing tests | Complete tracking and annotation; a link only in the handoff is insufficient. |

Invoke `better-code:writing` (or its installed equivalent) if it isn't already
active when writing the annotation. Build, CI, configuration, and tooling work remains within this
skill even where Better Code's code-semantics guidance doesn't apply.
