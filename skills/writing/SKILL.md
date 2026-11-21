---
name: writing
description: Use when writing or editing prose, including documentation, READMEs, tutorials, code comments, contracts, commit messages, pull request descriptions, and skill text.
---

# Writing

State the rule or claim directly, choose concrete wording, and remove sentences
that add no information. Use normal topic sentences and the document's supported
formatting.

## Companion Skills and Scope

When writing, editing, or auditing declaration contracts or contract documentation
comments in any language, invoke `better-code:contracts` if it is not already
active. Use it instead of `technical-documentation` for contract content and
formatting, including contracts embedded in a larger documentation task.

For other developer documentation, invoke `technical-documentation`. This
includes READMEs, tutorials, how-tos, skill instructions, non-contract docstrings,
API reference-page structure, CLI help, and release notes. Use its reader-task
framing, fact verification, document-type guidance, accessibility checks, and
applicable reference files. In a mixed task, apply each companion within its scope.
Do not invoke it automatically for ordinary commit messages, PR descriptions,
or unrelated prose.

When creating, editing, or validating a skill, invoke `superpowers:writing-skills`
(or its installed equivalent) and follow its authoring and testing process.
For other prose, apply this skill without invoking the skill-authoring workflow.

When writing or editing a local workaround's explanation in code, build
systems, CI, configuration, tooling, or documentation, invoke
`better-code:upstream-workarounds` (or its installed equivalent) if it isn't
already active. Apply its issue-tracking and adjacent-comment requirements;
a commit message or memory alone doesn't satisfy them.

When Better Code applies, it governs required contract content and semantics;
Better Code Contracts governs drafting and formatting; this skill governs
expression. Preserve required complexity, ownership, safety obligations,
non-obvious preconditions, observable postconditions, and caller-relevant error
guarantees. Prune wording already implied by the summary or enforced by the
signature, not semantic information callers need.

Preserve required headings, frontmatter, template fields, and workflow
instructions. A skill's instructions about when to test or invoke another skill
are actionable requirements, not incidental enforcement narrative.

## Technical Documentation Conflict Resolutions

Honor project conventions within their scope. Apply these resolutions when
`technical-documentation` guidance conflicts with this skill; otherwise follow
the companion's applicable guidance. Preserve required audit formats and other
templates. Ask the user about conflicts not resolved here before choosing a
resolution.

The following resolutions govern content retention, scaffolding, and vocabulary:

| Conflict | Resolution |
| -------- | ---------- |
| Preserve every source fact versus delete or relocate material | Apply the audience-and-decision test in the next section. Preserve reader-relevant facts and guarantees; keep unresolved reader-relevant facts visibly marked `TODO(verify)`. Put maintainer-only constraints and their reasons in `[knowledge]` comments, not rendered prose. Delete material that supports neither audience. |
| Required introductions, closing sections, and suggested paragraph length versus information density | Include audience and scope statements and introductions to lists, tables, and samples when they help the reader identify scope, act, or verify. Keep meaningful verification for procedures. Include "What's next" only when there is a useful next task. Let the information determine paragraph length; do not pad it. Preserve required template content. |
| Expand abbreviations on first use versus assume audience knowledge | Expand or define terms when the intended audience needs it. Use established audience vocabulary without restating definitions, including domain-specific abbreviations that are not universally familiar. |

## Audience and Maintenance Knowledge

Published prose contains what its audience needs to understand or use the
documented subject. Classify each statement by the decision it supports:

| Purpose | Destination |
| ------- | ----------- |
| Helps a reader understand or use the language, interface, or workflow | Published documentation |
| Helps a maintainer or agent preserve an invariant or avoid a known mistake | Nearby `[knowledge]` comment outside rendered prose |
| Supports neither audience with a concrete decision or constraint | Delete |

State the governing rule directly. Use technical terms established for the
intended audience without restating their definitions or spelling out routine
consequences. Keep syntax requirements at their appropriate definitions.
A history of agent mistakes is a reason to preserve maintenance knowledge,
not to repeat it in reader-facing prose. Keep user-facing syntax rules,
required contracts, and caller-relevant error timing visible.

Write each `[knowledge]` note as a maintenance action or constraint with its
reason, rather than a transcript or a hidden copy of deleted prose. Use HTML
comments in Markdown and ordinary comments such as `// [knowledge]` outside
source documentation comments. Verify that the documentation renderer keeps
these notes out of visible prose; comments remain visible in distributed source.

```markdown
CEL uses strict LL(1) parsing.

<!-- [knowledge]
Preserve CEL's separator rules when editing the grammar: a consumed comma
requires another item, except for the required singleton-tuple comma.
-->
```

The syntax reference still defines which forms accept commas; the note records
the grammar-editing constraint.

## Editing Checks

| Check                     | Apply while editing                                                                                                                                                                                                                                                                                                              |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Topic sentences           | Write topic sentences as normal prose, not bolded labels. Reserve bold for genuine emphasis or required structural labels rather than recurring paragraph headers in explanatory body prose.                                                                                                                                     |
| Useful summaries          | Cut sentences that merely repeat an earlier point or announce its importance. A summary should add a useful synthesis, conclusion, or action rather than restate that something matters.                                                                                                                                         |
| Direct claims             | State the claim directly. Avoid hedge-then-reveal constructions such as "not only X but also Y" and "not just X but Y."                                                                                                                                                                                                          |
| Specific vocabulary       | Use plain, specific words instead of generic promotional language. Watch for "crucial," "unlock," "leverage," "delve," "underscore," "showcase," "robust," "seamless," and "landscape"; replace filler with the concrete action or property intended. Precise technical uses, such as "robust statistics," retain their meaning. |
| Parenthetical punctuation | Use commas, semicolons, colons, parentheses, or sentence breaks for parenthetical material. Retain an em dash only after comparing a rewrite and finding that the alternative obscures the sentence's meaning or structure. The comparison need not appear in the output.                                                        |
| Introduced terms          | Italicize a domain term at its first introduction or definition; do not bold it to mark the definition. Use the document's supported markup, and preserve code formatting for identifiers. Plain-text comments need no Markdown decoration.                                                                                      |
| Rules and guarantees      | In reference material, tutorials, comments, and contracts, state the rule or observable behavior directly. Include enforcement timing or mechanism when it is part of a documented guarantee that changes how the reader uses the interface. Otherwise omit it.                                                                  |

## Example

For a declaration whose signature enforces element copyability, retain the
independent-copy guarantee and verified time and space costs, not the redundant
type requirement or internal helper:

Before:

```cpp
/// **Snapshot creation** does not just copy values but unlocks independent use.
/// Returns an independent copy of the input sequence.
/// Requires every input element to be copyable.
/// Time and additional space: O(n), where n is the number of input elements.
/// The implementation checks copyability through an internal template helper.
```

After:

```cpp
/// Returns an independent copy of the input sequence.
///
/// Complexity: O(n) time and additional space, where n is the input size.
```

## Common Mistakes

Using brevity to remove a required contract detail changes the contract rather
than improving its expression. Removing a promised parse-time error distinction
can likewise remove information callers rely on.

Treating all bold text as a violation damages required template fields; the
topic-sentence check targets explanatory body prose. Applying the skill-authoring
workflow to an ordinary comment or PR description confuses the companion's scope.
