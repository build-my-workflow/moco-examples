# moco-examples

The guided tour of the Moco YAML DSL itself. One small wfspec per language feature,
each with an integration test, ordered as a learning path.

This package teaches the *language*. The sibling packages teach *scenarios* — see
[Related demos](#related-demos) when you want a realistic end-to-end build rather than a
feature reference.

## Quick start

```bash
moco validate src/transform-demo.yaml     # check a spec against the schema
moco run src/transform-demo.yaml          # execute it
moco run src/transform-demo.yaml --debug  # ...and print every debug trace
moco test tests/ --in-memory              # run the whole suite, no Temporal needed
```

A variable whose name ends in `#` has its value streamed to the CLI as it is evaluated —
the primary debugging tool here, and used throughout:

```yaml
- total#: '{{ sum(prices) }}'    # logged when it runs
```

Traces from child workflows are collected automatically.

## Requirement badges

Most demos run from source with nothing else set up. These do not:

| Badge | Meaning |
|---|---|
| — | runs anywhere, including `moco test --in-memory` |
| `temporal` | needs the Temporal runtime; the in-memory runtime ignores or diverges |
| `network` | can make outbound HTTPS calls (opt-in, off by default) |
| `state` | needs the persistent state store |
| `publish` | needs `moco publish .` first — resolves specs from the registry |

## Catalog

### 1. Basics

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`single-activity-demo`](src/single-activity-demo.yaml) | the minimal workflow: a bare `activity`, `builtin.delay`, `output_name` | — | [test](tests/single-activity-demo.test.yaml) |
| [`transform-demo`](src/transform-demo.yaml) | a bare `transform`, `__user_info__`, `now()`, chaining within one step | — | [test](tests/transform-demo.test.yaml) |
| [`sequence-demo`](src/sequence-demo.yaml) | `sequence`, carrying a variable across steps | — | [test](tests/sequence-demo.test.yaml) |

### 2. Data flow and expressions

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`data-flow-demo`](src/data-flow-demo.yaml) | `context` vs `input_data` vs statement `input_data`; `output_name` vs `output_data`; `_raw_output`; **the child-output shadowing gotcha** | — | [test](tests/data-flow-demo.test.yaml) |
| [`expression-demo`](src/expression-demo.yaml) | the reference sheet: whitelisted builtins, comprehensions, and every name modifier (`#literal`, `#jinja`, `#python_glom`, `@`, `_`, trailing `#`) | — | [test](tests/expression-demo.test.yaml) |
| [`variable-modifier-demo`](src/variable-modifier-demo.yaml) | the same modifiers as a narrative, over a parallel iteration | — | [test](tests/variable-modifier-demo.test.yaml) |
| [`container-scope-variable-demo`](src/container-scope-variable-demo.yaml) | `@` container scope in depth, via an order-discount pipeline | — | [test](tests/container-scope-variable-demo.test.yaml) |

### 3. Control flow

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`condition-demo`](src/condition-demo.yaml) | `condition:` on primitives and composites; if/elif/else; the structured `{and:}` / `{or:}` / `{not:}` forms | — | [test](tests/condition-demo.test.yaml) |
| [`iteration-demo`](src/iteration-demo.yaml) | `iteration` on its own: `sequence` vs `parallel`, `iter_item` vs `iter_items`, nesting, collecting results | — | [test](tests/iteration-demo.test.yaml) |
| [`parallel-demo`](src/parallel-demo.yaml) | `parallel` with named branches and `join_type: and` | — | [test](tests/parallel-demo.test.yaml) |
| [`abort-demo`](src/abort-demo.yaml) | all five `abort` types, and how `break` differs from `break_iteration` | — | [test](tests/abort-demo.test.yaml) |

### 4. Composition and reuse

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`functions-demo`](src/functions-demo.yaml) | `functions:` + the `call` statement; calling a sibling function | — | [test](tests/functions-demo.test.yaml) |
| [`child-workflow-modes-demo`](src/child-workflow-modes-demo.yaml) | all four `child_mode` values and both reference styles; **what `inline` does and does not share** | — | [test](tests/child-workflow-modes-demo.test.yaml) |
| [`child-workflow-demo`](src/child-workflow-demo.yaml) | a worked pipeline using inline + sync + async children together | — | [test](tests/child-workflow-demo.test.yaml) |
| [`data-resolver-demo`](src/data-resolver-demo.yaml) | the child-workflow-as-data-resolver pattern; picking a resolver spec at runtime | — | [test](tests/data-resolver-demo.test.yaml) |
| [`wfspec-imports-demo`](src/wfspec-imports-demo.yaml) | `wfspec_imports`: sharing constants between specs, with renaming | `publish` | [test](tests/wfspec-imports-demo.test.yaml) |
| [`shared-config-demo`](src/shared-config-demo.yaml) | *(support file)* a context-only wfspec — no `body` — for the import above | — | — |

### 5. Events

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`events-demo`](src/events-demo.yaml) | `emit_event` + `wait_for`; `match_expression`; the timeout-returns-`None` path; an `async_mode` activity's completion arriving as an event | — | [test](tests/events-demo.test.yaml) |

### 6. Activity behaviour

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`activity-options-demo`](src/activity-options-demo.yaml) | `retry_policy`, `enable_cache` + `cache_policy`, `execute_locally`, `builtin.now`, `http.request` | `network` `temporal` | [test](tests/activity-options-demo.test.yaml) |
| [`state-store-demo`](src/state-store-demo.yaml) | `builtin.state.*`: state that outlives the run — run it twice and watch the counter climb | `state` | [test](tests/state-store-demo.test.yaml) |

### 7. Engines

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`state-machine-basics-demo`](src/state-machine-basics-demo.yaml) | the smallest useful FSM: `on_enter`/`on_exit`, terminal states, automatic and conditional transitions | — | [test](tests/state-machine-basics-demo.test.yaml) |
| [`rules-engine-basics-demo`](src/rules-engine-basics-demo.yaml) | `rules_engine`: `with_facts` + `expression`, `set_facts`, fact chaining, forward vs backward | — | [test](tests/rules-engine-basics-demo.test.yaml) |

### 8. Durability

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`continue-as-new-demo`](src/continue-as-new-demo.yaml) | `continue_as_new_checkpoint` wired by hand, with the sentinel that stops it looping | `temporal` | [test](tests/continue-as-new-demo.test.yaml) |
| [`state-machine-continue-as-new-demo`](src/state-machine-continue-as-new-demo.yaml) | `checkpoint_policy` with `event_count` — the same thing, declared instead of wired | `temporal` | [test](tests/state-machine-continue-as-new-demo.test.yaml) |
| [`state-machine-continue-as-new-timer-demo`](src/state-machine-continue-as-new-timer-demo.yaml) | `checkpoint_policy` with `timeout_sec`, plus named state `timers` | `temporal` | [test](tests/state-machine-continue-as-new-timer-demo.test.yaml) |

### 9. Running them all

| Example | Teaches | Needs | Test |
|---|---|---|---|
| [`moco-examples`](src/moco-examples.yaml) | a data-driven smoke runner: one `iteration` over a list of demo names, `catch_exception` per child, pass/fail summary | `publish` | [test](tests/moco-examples.test.yaml) |

Plus [`runner-enforcement-control`](tests/runner-enforcement-control.test.yaml) — not a demo, a
test of the test runner. See [Trusting a green run](#trusting-a-green-run).

## Feature coverage

Where to look for a given feature.

| Feature | Primary example | Also in |
|---|---|---|
| `transform` | transform-demo | nearly all |
| `activity` | single-activity-demo | activity-options-demo, state-store-demo, iteration-demo |
| `sequence` | sequence-demo | nearly all |
| `parallel` | parallel-demo | — |
| `iteration` | iteration-demo | container-scope-, variable-modifier-, data-resolver-, moco-examples |
| `condition` | condition-demo | abort-demo, child-workflow-modes-demo, continue-as-new-demo |
| `abort` (5 types) | abort-demo | iteration-demo |
| `workflow` / `child_mode` | child-workflow-modes-demo | child-workflow-demo, data-resolver-, data-flow-, moco-examples |
| `call` + `functions` | functions-demo | — |
| `wait_for` / `emit_event` | events-demo | child-workflow-demo, state-machine-continue-as-new-timer-demo |
| `state_machine` | state-machine-basics-demo | both continue-as-new state machine demos |
| `rules_engine` | rules-engine-basics-demo | — |
| `continue_as_new_checkpoint` | continue-as-new-demo | both state-machine checkpoint demos |
| `checkpoint_policy` | state-machine-continue-as-new-demo | ...-timer-demo |
| `context` / `input_data` / `output_data` | data-flow-demo | all |
| `wfspec_imports` | wfspec-imports-demo | (shared-config-demo is the source) |
| `functions` | functions-demo | — |
| `tags` | every spec | — |
| Modifiers `@ _ # #literal #jinja #python_glom` | expression-demo | variable-modifier-, container-scope-variable-demo |
| `retry_policy` / `cache_policy` / `execute_locally` | activity-options-demo | — |
| `async_mode` | events-demo | ../async-activity-demo |
| `builtin.state.*` | state-store-demo | — |
| `http.request` | activity-options-demo | — |
| `run_as_user_id` | *not demonstrated* | — |

## Notes

### Runtime differences

`moco test` uses Temporal by default; `--in-memory` uses the in-process runtime. They are not
equivalent, and three differences show up in this package:

- **`retry_policy` and timeouts.** The schema is explicit: *"Only the Temporalio runtime honors
  these settings; the in-memory runtime ignores them."* An in-memory pass proves the spec parses,
  not that anything retried.
- **`sync` child workflow ids.** On Temporal a `sync` child is a real child workflow with its own
  derived id; in-memory it is not, and reuses the parent's. Data isolation holds on both.
  `child-workflow-modes-demo` asserts this loosely for that reason.
- **`join_type: or`.** On Temporal a surviving item's value comes back; in-memory the result is
  `None`. `iteration-demo` deliberately does not demonstrate it — prefer `and` plus explicit
  per-item error handling, which behaves the same everywhere.

### Two things that surprise people

Both are demonstrated, with the proof in the workflow output rather than just a comment.

- **A child's output keys shadow your variables.** Inside a `workflow` statement's `output_data`,
  the child's returned top-level keys are bound as names. If the child returns `currency` and you
  also have a `currency`, the name refers to the child's — and mutating it is discarded when the
  statement ends. Bind the whole result with `output_name` and unpack it in the next step.
  See `data-flow-demo`.
- **`inline` does not share the parent's variables.** Every child, inline included, starts with a
  fresh data context and sees only `input_data`. What `inline` shares is the *execution* — same
  `workflow_id`, no separate history or retries. See `child-workflow-modes-demo`.

### Dynamic spec names need publishing

A literal `wfspec.name` resolves from the local package bundle, so `moco run` works from source.
A computed one (`name: '{{ iter_item }}'`) does not — it goes to the registry. That is why
`moco-examples` and `wfspec-imports-demo` carry the `publish` badge, and why
`data-resolver-demo` supplies `content` alongside its dynamic name.

### Tests live in `tests/`, never `src/`

For an integration test the CLI bundles *every* `*.yaml` in the spec's directory as wfspec
content. A `.test.yaml` sitting in `src/` is swept in as a spec with no `wfspec_name` and breaks
the whole package parse.

### Trusting a green run

`tests/runner-enforcement-control.test.yaml` exists to prove the runner actually fails tests that
should fail. It holds one passing case and three deliberately-wrong ones kept `skip: true`, so the
suite stays green in normal use. To re-verify:

```bash
# remove the three `skip: true` lines, then
moco test tests/runner-enforcement-control.test.yaml --in-memory
# expect exactly THREE failures and one pass; restore the flags afterwards
```

Any deliberately-wrong case that reports *pass* means that expectation type is not being enforced,
and every green in this package for that type is meaningless. This control caught two real bugs.

### Asserting on the output

A spec with top-level `output_name` returns a **bare value**, so there is no key to match:

```yaml
output_name: greeting          # workflow returns the string itself
```

```yaml
expect:
  assert:
    - "{{ __OUTPUT__.startswith('Hello') }}"   # correct
    - "{{ greeting.startswith('Hello') }}"     # wrong: no such variable
```

With top-level `output_data` the result is a dict, and `expect.output` can match its top-level
keys — flat keys only, no dotted paths. Also note `None` is capitalised: `is not none` is a
`NameError`, which fails the assertion for the wrong reason.

## Run

```bash
# one at a time
moco run src/condition-demo.yaml --debug
moco run src/abort-demo.yaml -i '{"mode":"break_iteration"}'
moco run src/state-machine-basics-demo.yaml -i '{"quality_score":50}'

# opt into the networked path
moco run src/activity-options-demo.yaml -i '{"offline":false}'

# the whole suite, offline
moco test tests/ --in-memory

# everything at once, after publishing
moco publish .
moco run src/moco-examples.yaml --debug
```

## Related demos

| Package | What it covers |
|---|---|
| [`../state-machine-demo`](../state-machine-demo) | order fulfilment and purchase approval state machines |
| [`../rules-engine-demo`](../rules-engine-demo) | loan approval: forward and backward chaining with actions and an audit trail |
| [`../entity-workflow-demo`](../entity-workflow-demo) | long-lived addressable workflows, signal-with-start |
| [`../async-activity-demo`](../async-activity-demo) | `async_mode` in full: tokens, completion events, failure handling |
| [`../client-server-demo`](../client-server-demo) | request/response between two workflows |
| [`../task-manager`](../task-manager) | a CRUD service built as workflows |
| [`../web-crawler-demo`](../web-crawler-demo) | browser automation |
| [`../openai-demo`](../openai-demo), [`../ai-agent-demo`](../ai-agent-demo) | LLM activities and agent loops |
| [`../rmq-demo`](../rmq-demo) | RabbitMQ publish/receive |
