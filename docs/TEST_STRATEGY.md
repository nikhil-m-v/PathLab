# Test strategy and experimental methodology

**State:** specified test matrix; no implementation tests have run.

## Correctness layers

| Layer | Cases and properties | Required evidence |
|---|---|---|
| Validation | Duplicate IDs, dangling endpoints, empty graph, bad source, Boolean/fractional/negative weights, exact-integer encoding | Deterministic adapter tests |
| Baseline | Singleton, chain, branch, self-loop, parallel edges, ties, disconnected graph, zero cycle | Dijkstra versus independent Bellman–Ford |
| Generated graphs | Small directed multigraphs with nonnegative integer weights | Hypothesis oracle agreement, minimized failures persisted |
| Labels | Equal weights, equal hop counts, predecessor updates, zero cycles | Comparison properties and acyclic path reconstruction |
| Transformation | High degree, parallel edges, self-loops, isolated source | Original/transformed oracle distances and mapped path validity |
| AVL/queue | Inserts, decreasing updates, duplicate keys, splits, prepend, pull, empty transitions | AVL invariants and operation-sequence reference model |
| Pivot/base case | Empty pivot set, early pivot return, bounds and ties | Oracle-backed component contracts |
| BMSSP | Successful/partial returns, nested frames, workloads exceeding base case | Instrumented branch coverage plus distance agreement |
| Serialization | Every supported record, large native integers, null infinity, schemas | Round trips and rejected malformed payloads |

Bellman–Ford is independent test code: no BMSSP pivot helpers, queue, transformation, or relaxation routine. Compare distances for every vertex. Validate returned paths independently; identical predecessors are not required.

Oracle-heavy assertions belong in debug/test runs only. Production completion markers must come from algorithm transitions, not knowledge of true distances.

## Browser and trace tests

- Execute shared fixtures in native Python and actual Pyodide; compare all distances and validate paths.
- Check trace sequence continuity, frame ancestry, valid graph references, and deterministic state reduction.
- Replay fully recorded runs and compare their terminal state with the final answer.
- For truncated traces, assert an explicit incomplete state and a separately available final answer.
- Seek via checkpoints and sequential replay to the same state.
- Edit during execution; ignore stale responses and terminate obsolete work.
- Cancel, timeout, runtime failure, retry, import rejection, and production-subpath loading.
- Keyboard operation, reduced motion, labeled controls, logical queue inspection, parallel arrows and self-loop presentation.

Use Vitest for reducers/components and Playwright for real worker/runtime integration. Run the worker smoke test before implementing advanced UI behavior.

## Benchmark protocol

Default scale: 100, 1,000, and 10,000 vertices where the family admits the size. Use five explicit seeds, average degrees 2/4/8 for random families, one seeded source per case, and uniform integer weights 0–1,000 unless a case specifies otherwise. Grid dimensions and actual vertex counts are recorded rather than pretending requested sizes are exact squares.

Families: random sparse, directed grid, chain, layered branching, skewed-degree, and disconnected. Zero/repeated-weight stress cases appear in correctness and selected performance configurations. Larger sizes are opt-in; no automatic large dataset download.

1. Generate once, canonicalize, and hash each graph. Validate answers before accepting timing.
2. Warm each solver twice. Run seven repetitions per solver, alternating which solver runs first.
3. Use controlled sequential child invocations with a 30-second timeout. Startup and parsing are outside timing; solver allocations remain inside.
4. Disable observers; measure using `perf_counter_ns`. Record preprocessing, core, and end-to-end intervals without overlap.
5. Collect counters and Python allocation peaks in separate invocations. Label `tracemalloc` output as traced Python allocations, not process RSS or total machine memory.
6. Save individual records, including warmups, correctness failures, timeouts, exceptions, configuration, dirty state, and runtime/hardware details.
7. Report medians, quartiles, successful sample counts, and censored outcomes. Define speedup as Dijkstra time divided by BMSSP time for matching successful cases.

Do not blend graph seeds into an unexplained single timing. Per-case medians are primary; family summaries state their aggregation. A timeout is not a measured 30-second runtime. No crossover extrapolation belongs in release claims.

## CI and evidence

Fast checks: validation/unit tests, bounded property tests, typing/lint, frontend reducer/component tests, browser smoke integration, and build. Longer randomized campaigns and native benchmarks are explicit/manual tasks. Performance thresholds are not required CI gates on shared runners.

Every roadmap completion entry records command, environment, result, and relevant artifact. Research fidelity has a separate written operation-cost review; tests cannot establish an asymptotic bound.
