---
name: better-code
description: Use when writing or modifying code in any language, including implementing features, fixing bugs, refactoring, defining interfaces or data structures, and writing unit tests.
---

# Better Code

Write correct, efficient, and abstract code:

- **Correct:** Satisfy explicit contracts, preserve invariants, and report runtime
  failures.
- **Efficient:** Choose appropriate algorithms and data structures; avoid
  unnecessary allocation, copying, synchronization, and indirection.
- **Abstract:** Follow [Elements of Programming][eop]: require only the operations,
  relationships, and semantic laws needed to deliver the promised result and
  complexity. Minimize assumptions about concrete types, representations, callers,
  and frameworks so components remain independently reusable.

For example, a random-access comparison sort requires a mutable random-access
sequence, suitable element rearrangement operations, and a comparison satisfying
strict weak ordering, rather than an array of integers. Generalize by weakening
requirements while preserving the contract, not by adding virtualization layers,
speculative extension points, or reimplementing library facilities. Minimal
semantic requirements are distinct from minimizing package dependencies.

Preserve correctness first; pursue efficiency and reuse through the simplest
design that satisfies the contract. Keep contracts simple and ownership explicit.
Apply these rules to public and private code, including framework glue. Build
systems, CI, dependency management, and Git workflows are outside this skill.
General prose style belongs to the Writing companion below.

## Required Companion: Upstream Workarounds

**REQUIRED SUB-SKILL:** Invoke `better-code:upstream-workarounds` (or its
installed equivalent) when introducing or changing a local workaround for an
upstream defect or gap. Track the upstream issue and add its reference and
reason beside the workaround before calling the repair complete.

## Required Companion: Superpowers

**REQUIRED SUB-SKILL:** Invoke `superpowers:using-superpowers` (or the equivalent
installed name in the current runtime) if it is not already active, then follow
the applicable Superpowers skills. Superpowers governs development process;
Better Code governs code contracts, semantics, ownership, and test expectations.
Ensure delegated implementers follow both. If a required skill is unavailable,
report the missing prerequisite rather than silently substituting a workflow.

When guidance differs, Better Code takes precedence on minimum unit-test coverage
and precondition/error semantics, including defensive validation. Even trivial
components require a unit test. For explicitly user-approved throwaway prototypes,
follow Superpowers' spike guidance, including reduced or no tests. Apply the normal
contract and testing requirements before retaining prototype code for normal use.

## Required Companion: Writing

**REQUIRED SUB-SKILL:** Invoke `better-code:writing` (or the equivalent installed
name in the current runtime) when writing or editing code comments or contracts. Ensure
delegated implementers follow it too.

Writing governs expression; Better Code governs required contract content and
semantics. Preserve required contract information when applying prose guidance.
Brevity does not waive contract requirements.

## Required Companion: Contracts

**REQUIRED SUB-SKILL:** Invoke `better-code:contracts` (or the equivalent installed
name in the current runtime) when writing, editing, or reviewing declaration
contracts in any language, if it is not already active. Ensure delegated
implementers follow it too.

Better Code governs semantics; Better Code Contracts governs drafting and
formatting. Follow its subtractive process without removing required ownership,
safety, invariant, complexity, example, or caller-relevant failure information.
Use it instead of Technical Documentation's declaration-contract templates.

## Contracts

Every type and function must have an adjacent contract in the language's
documentation or comment syntax. Use a concise, present-tense summary of its
purpose or successful result. Include applicable type invariants, non-obvious
preconditions, and observable postconditions not already implied by the summary.
Document non-constant time and space complexity; O(1) is the documentation
convention when no complexity clause is present, not evidence about the code.
Verify the bound before omitting a clause.
Public APIs need examples illustrating their contracts, or references to
applicable shared examples.

Document runtime error details only when callers need them, separately from
successful postconditions. Do not enumerate incidental propagated errors or expose
dependency exception types merely because they can occur.

Do not document behavior following a precondition violation.
In C++, violations of any precondition are unsafe, including conventional
reference-lifetime and mutable-reference non-aliasing obligations.

For other languages, behavior following a precondition violation is unspecified
for a safe operation and undefined for an unsafe operation. A safe interface must
remain safe even when a precondition is violated: checks needed to prevent
undefined behavior must remain effective in release builds.

If a simple contract is difficult to write, improve the design rather than
compensating with a complicated explanation.

## Operation Semantics and Regular Types

Operation and operator names must reflect their semantics. When semantics match
a language or library facility, use its established name rather than inventing a
synonym. Follow the regular-type guidance in [Elements of Programming][eop],
particularly sections 1.5, 1.6, and 4.2.

Treat related operations as one coherent contract, not independent conveniences:

- Copying creates a new object equal to the source and logically disjoint from
  it: mutating either object must not mutate the other. Immutable subobjects may
  be shared when their lifetime covers every owner's use. Logical disjointness
  concerns the represented value, not separate allocations: shared immutable code
  or metadata does not make a value reference-semantic. A new wrapper around
  shared mutable represented state is not a value copy.
- Value equality is an equivalence relation: reflexive, symmetric, and transitive.
  Inequality is its negation. Distinguish value equality from object identity.
- Copy assignment for a regular value type makes the destination equal to the source
  without changing the source, independent of the destination's previous value.
  Preserve logical disjointness for distinct objects and handle self-assignment.
- If less-than provides a total ordering, it must be transitive and obey
  trichotomy: exactly one of `a < b`, `b < a`, or `a == b` holds. Consequently,
  `b = copy(a)` implies `a == b`, `!(a < b)`, and `!(b < a)`.

Do not advertise a partial or strict weak ordering as a total ordering. For
example, comparing only one field may make distinct values ordering-equivalent
without making them equal. Derive related comparison operations consistently.
Operations presented as regular must produce equal results when equal inputs
are substituted.

Derive tests from these laws, including cross-operation consistency,
self-assignment where provided, and mutation independence of copies.

## Complexity and Delegated Costs

For value operations, measure complexity against the affected value's area:
its stored contents and nested owned values, including logical elements when
zero-byte representations still require visits. A wrapper's `sizeof` is not its
value's area. Account for the old and new values in replacement, the selected
child in extraction, and type descriptions when an operation examines them.

Verify repeated work as well as individual steps. Repeated root-to-component
searches or type comparisons can make a traversal superlinear even when each
step looks simple. For shared graphs, account for traversed edges, compared
node pairs, and repeated visits; distinct node count alone is not a bound.
Report a costly algorithm or representation as a design issue rather than
hiding it behind a variable defined as the work performed.

Compose costs using the held type's or callback's contract. Destroying a held
object includes that type's destruction cost; shared ownership can make the
last release pay for retained objects. Verify first-use initialization separately
from steady-state work before promising allocation-free or nonthrowing execution.

## Contract-Derived Unit Tests

Before selecting test instrumentation, inspect repository testing policies,
harness conventions, and relevant intentional removals in recent history.
Reuse supported mechanisms; a passing local test does not establish platform
compatibility. If policy excludes a technique, obtain explicit approval for a
policy change before using it.

Derive unit tests and expected outcomes from contracts and public interfaces.
Avoid inspecting the implementation to derive tests: doing so risks encoding
undocumented assumptions instead of validating the contract.

There is no blanket prohibition on code inspection. Inspect implementations when
debugging, diagnosing a failed test, investigating dependencies, or clarifying
behavior. Either the implementation or the test may be wrong; judge both against
the contract rather than making the test match whatever the code currently does.
If the contract is unclear, clarify it before asserting the behavior.

- Assert postconditions and promised error behavior, including documented failure
  guarantees. Cover interface-implied edge cases such as empty inputs, single
  elements, and boundary values.
- Every component, including trivial components and passthroughs, must have at
  least one unit test. This is a minimum, not a substitute for covering its
  contract, subject to the approved throwaway-prototype exception above.
- Do not test precondition violations or assert a particular outcome for them.
  Malformed untrusted input is a runtime-error case to test, not a caller
  precondition violation to ignore.
- Extract branching, combining, or suppressing logic from framework callbacks,
  event handlers, and UI glue into small functions with adjacent contracts and
  independent unit tests. Do not use "just glue" or triviality to waive the
  component's minimum unit test.

Testing the postconditions must cover changed behavior; screenshots and smoke
tests do not replace unit tests for decision logic.

For a complexity regression, record its counted work, input-derived bound,
and controlled code change that makes the assertion fail. Count work across
the complete operation, including repeated and shared inputs, rather than
using a timing threshold. Temporarily restore the costly path, verify the
expected failure, then restore the implementation and rerun the tests.
Counts of comparisons, visits, or retained metadata do not establish
allocation-free execution; allocation evidence must cover the promised paths.

## Allocation, Ownership, and Dispatch

Avoid unnecessary heap allocation, copying, and runtime polymorphism. Prefer direct
value/member storage, borrowed references or views, slices, iterators, and static
dispatch when they satisfy ownership, lifetime, and API contracts.

Do not clone or materialize a collection merely to inspect existing data or work
around a borrow that can be scoped more narrowly. Allocate, copy, or use dynamic
dispatch when the actual ownership or extensibility requirements call for it.
Correctness and lifetime safety take precedence over removing an allocation.

Use the [operation-cost reference](references/operation-costs.md) to inform choices
about locality, allocation, indirection, and synchronization. Its estimates are
not performance guarantees; measure consequential choices on the target system.

## Mutable References in Reference-Semantic Languages

In reference-semantic languages such as JavaScript, Java, and Python, avoid
sharing mutable objects. Prefer references to immutable objects wherever
possible. Immutability must include reachable state, not just a read-only binding
or a shallowly frozen wrapper.

Use naming conventions such as `new`, `make`, `create`, `copy`, `clone`, or
`allocate` to signal that an operation returns a uniquely owned mutable value.
Its name and contract must agree: allocating a new outer object does not provide
independence if nested mutable objects remain shared.

When storing a mutable object reference, such as in a member, copy the object
unless unique ownership is established. Copy mutable subobjects as needed to
eliminate aliases; a shallow copy is insufficient when it retains mutable sharing.
Share immutable subobjects rather than copying them unnecessarily.

Do not return a shared reference to mutable internal state. A shared mutable
reference may be returned only when its source is owned by the caller: `f(p)`
may return `q` that is part of caller-owned `p`, provided the contract explicitly
states that relationship. Otherwise return an independent mutable copy under an
ownership-signaling name, or an immutable value.

Test mutation isolation in both directions, including nested mutable state, and
test documented caller-rooted references as such. These boundaries uphold local
reasoning and prevent incidental data structures formed by uncontrolled aliases.

## Concurrency and Exclusivity

Prefer structured concurrency and higher-level coordination through async/await,
serial queues, and actors. Where partially structured concurrency is appropriate,
use promises/futures and channels.

Avoid low-level thread synchronization primitives such as mutexes, condition
variables, and atomics in ordinary component code. Strongly avoid shared mutable
state. Confine necessary sharing and low-level synchronization to implementations
of general concurrency mechanisms, such as a future. Actor- or queue-owned mutable
state must remain confined to that owner.

Follow Swift's [exclusivity principle][exclusivity]: permit multiple readers or
one exclusive writer, with no overlapping access during mutation. This applies to
single-threaded aliasing and reentrant callbacks as well as concurrent access.

Make task ownership, lifetime, cancellation, and error propagation explicit:

- In structured concurrency, tie child tasks to their owning scope.
- In partially structured systems, tie task lifetime to object lifetime.
  Destroying the last interested future or channel consumer cooperatively cancels
  upstream tasks no longer needed for its result or next value. Do not cancel
  work still required by another consumer.
- Cancellation is not forced interruption or a synchronous join. Already-running
  work cooperates and releases its resources; do not leave unowned background
  tasks when demand disappears.
- If destruction is not deterministic, use an explicit scope-bound close/dispose
  mechanism to provide the lifetime boundary. Do not assume arbitrary future or
  channel libraries provide these semantics.
- Async syntax, actors, and queues alone do not establish structured task
  lifetimes or preserve invariants across suspension. Restore valid state before
  suspension and re-establish assumptions after resumption or reentrant calls.

Use [STLab concurrency][stlab-concurrency] as a model for lifetime-based
cancellation, not as a mandatory dependency.

## General Solutions and Design Quality

Solve the component's stated problem, not just the narrow case exercised by one
example or caller. Avoid special cases that silently produce incorrect behavior
outside that example.

When a general solution cannot be completed, delineate its boundary with an
appropriate precondition or a clear runtime error/diagnostic, and explicitly
record what remains unsupported. Untrusted-input boundaries require safe
validation, not validity assumptions imposed on external data.

Prefer clean redesign over patches and compatibility layers when the project's
compatibility requirements permit it. Do not assume every project is unreleased
or has no existing clients.

## Error Reporting and Failure Guarantees

A runtime error occurs when an operation cannot satisfy its postconditions even
though its preconditions hold. Prefer expressing requirements through types;
otherwise prefer stronger preconditions when callers can easily and reliably
guarantee them. Conditions callers cannot ensure, such as successful I/O, belong
in runtime error handling.

Validate untrusted input at its boundary. Within trusted code, prefer
caller-established preconditions over automatically adding runtime validation at
every layer. This does not remove checks needed to keep safe interfaces safe.

Precondition violations are programming errors, not runtime errors. In safe code,
their unspecified consequences may include panic or termination; in unsafe code,
they may include undefined behavior. These are general distinctions, not outcomes
to promise in an individual operation's documentation.

On a runtime error, an object under mutation may have unspecified state, but it
must remain safely discardable without leaking resources. Do not assume the
object remains usable or unchanged.

An operation may document stronger guarantees, such as the strong exception
guarantee that leaves the original state unchanged. Do not provide stronger
guarantees unless their cost is insignificant or they are necessary for safety.
In particular, do not add expensive rollback copies by default.

Code dealing with untrusted input must be safe and must not panic or terminate
because that input violates a precondition. Strive for no input-validity
preconditions and always detect and report malformed input. Any remaining
unspecified behavior must stay contained; it must not compromise memory safety
or escape as process failure.

For languages with exception handling, such as C++, prefer exceptions for runtime
error reporting. Otherwise use the language's supported fallible mechanism.
Normally propagate errors until a boundary can meaningfully handle them; do not
convert failure into a success-shaped default. Optional absence can be an
intentional successful postcondition, not an error.

See [Better Code: Errors][errors] for the underlying model.

## Algorithms and Data Structures

Choose algorithms in this order:

1. Use an applicable standard-library algorithm.
2. If none exists, search for a well-supported library providing one.
3. If none is suitable, search the literature for a known applicable algorithm,
   then implement it in a general form under an appropriate established name.

Avoid special-purpose algorithms. When one is necessary, encapsulate it in a
well-named function, or a type if it requires persistent state, with an adjacent
contract. Do not put an algorithmic loop in the middle of a larger function.

A very simple for-each may remain when using a standard-library algorithm would
make the code more complex. Add a brief comment explaining what that loop does.

Similarly, prefer existing library data structures over inventing new ones.
Avoid *incidental structures*: objects referring to other objects by pointer,
index, ID, or similar references without an owning object controlling those
structural relationships. Relationships must be confined between parts of a
whole; the whole type owns responsibility for maintaining structural invariants.
An ID instead of a pointer does not remove that responsibility.

## Common Mistakes

| Temptation | Required correction |
|---|---|
| "It's just glue; a smoke test is enough." | Extract decisions, document contracts, and unit-test them. |
| "Any failure must leave the object reusable." | Require safe disposal without leaks; justify stronger guarantees. |
| "It's malformed input, so use a result wrapper in C++." | Prefer exceptions for runtime errors, including malformed input. |
| "This for-each is obvious." | Keep the simpler loop and briefly comment what it does. |

Stop and rethink when a contract is hard to state, a loop hides an algorithm,
relationships have no invariant-maintaining owner, or a task can outlive its
last interested owner without a cancellation boundary.

[exclusivity]: https://docs.swift.org/swift-book/documentation/the-swift-programming-language/memorysafety/
[stlab-concurrency]: https://github.com/stlab/stlab/blob/main/include/stlab/concurrency/future.hpp
[errors]: https://github.com/stlab/better-code/blob/main/better-code/src/chapter-3-errors.md
[eop]: https://elementsofprogramming.com/eop.pdf
