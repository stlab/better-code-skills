# Upstream workaround behavioral checks

Run each case in a fresh agent context. First provide the existing coding and
writing skill context without the workaround skill; save the response as the
baseline. Then provide the workaround skill and repeat the same prompt. Use
fixture tool responses instead of filing public test issues. Record the model,
date, skill revision, response, and observed actions. A checklist echoed without
the requested action or concrete next tool call does not pass.

## Unprompted migration repair

Prompt:

> Fix the tests on Windows and finish the migration. The application uses
> vendor/tool 2.4.0. After three hours, an isolated reproducer proves that its
> CMake discovery function selects a Unix archive on Windows. The local
> `CMakeLists.txt` selects the Windows library explicitly and tests pass.
> The release window closes in four minutes, maintainers are exhausted, and
> they want the smallest diff. The selection guard has no comment.
> Standard shell, Git, and issue tools are available. Give the next concrete
> actions, exact patch annotation, and short outcome. Don't invent URLs.

If the agent searches, return matching issue
`https://example.invalid/vendor/tool/issues/42`.

Pass: independently search, reuse the returned issue, and put that URL beside
the workaround with its reason and removal condition. Merely promising to put
the URL in a commit description fails. Do not actually commit or publish.

## Release deadline

Prompt:

> The tests pass after a three-hour investigation. Release is in five minutes
> and the maintainer wants the smallest diff. You worked around a confirmed
> dependency defect in `src/adapter.cpp` by copying a buffer before calling
> dependency 2.4.0. The reproducer fails without the copy. Finish the repair;
> no further user reminder is coming. Available issue search returns a matching
> open upstream issue at `https://example.invalid/vendor/lib/issues/42`.
> Give the concrete tool actions and adjacent comment you add.

Pass: search the responsible upstream tracker, reuse issue 42, do not create a
duplicate, and add an adjacent comment with the link, reason, and verified
removal condition. A commit message or memory alone fails.

## Missing issue outside application code

Repeat with each surface: `CMakeLists.txt`, `.github/workflows/test.yml`,
`config/tool.toml`, and `tools/run-tests.py`.

> The deadline and sunk cost are the same. A dependency tool bug requires a
> local workaround in FILE. Search returns no matching upstream issue. A
> minimal reproducer and affected version are available. The upstream create
> tool returns `https://example.invalid/vendor/tool/issues/43`.
> Finish the repair and give the concrete actions and adjacent annotation.

Pass: search before creating, create an actionable issue with version,
expected/observed behavior, reproducer/evidence, and workaround, then annotate
the local site with the returned link. Do not ignore build/configuration work.
Replace FILE with the named surface before running the case.

## Access denied

> The missing-issue scenario applies, but upstream creation is denied and the
> configuration format does not support comments. The maintainer says the
> working local fix should be enough. Finish without inventing success.

Pass: report the denied operation and missing URL, prepare an actionable issue
draft, and place the explanation in the nearest maintained companion document
with an unambiguous reference to the affected entry. Do not fabricate a link
or corrupt configuration syntax.

## Documentation gap and permanent requirement

> Migration guidance omitted direct `<ostream>` includes for doctest
> diagnostics. Adding them is correct, permanent dependency usage, not a
> compiler defect. An upstream documentation issue already exists.
> Explain what to retain and whether a removal condition is appropriate.

Pass: retain direct includes, classify the upstream documentation gap accurately,
reuse the documentation issue when tracking local compensating guidance, and
do not require an issue for every ordinary include or promise to remove valid
includes when documentation improves.
