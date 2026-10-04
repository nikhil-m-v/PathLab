# Project specification

**State:** specified. See [project state](PROJECT_STATE.md) for implementation evidence.

## Goal and audience

Give GitHub visitors a usable demonstration of algorithm knowledge, research translation, and reproducible engineering. The project succeeds through correct code, meaningful explanation, and inspectable evidence; a BMSSP speedup is not a release requirement.

## First release

- Original Dijkstra and BMSSP implementations over a common Python graph model.
- Native CLI for solving, trace export, benchmarks, and report generation.
- Editable SVG graph with directed edges, weights, source selection, and target-path inspection.
- Actual solver traces with forward/backward playback, recursion inspection, and algorithm-specific state.
- Curated examples, published native benchmark records, and explanatory charts.
- Static deployment and CI covering Python and frontend behavior.

Exclude negative weights, floating weights, arbitrary browser Python execution, accounts, server APIs, automatic solver selection, and mid-execution switching.

## Input policy

Graphs are directed multigraphs. Parallel edges and self-loops are allowed and have distinct IDs. Nonnegative integer weights include zero; Boolean values are not weights. Vertex and edge IDs are unique nonempty strings. A valid graph has at least one vertex and an existing source.

Browser limits: 100 vertices, 500 edges, weights 0–1,000,000 inclusive. Native inputs support arbitrary nonnegative Python integers subject to available resources. The JSON browser adapter rejects integers outside its exact numeric range. Native large-integer results are serialized as decimal strings when necessary, as specified in [interfaces](INTERFACES.md).

Presentation positions do not affect solving. Unreachable vertices remain explicit, with no predecessor. For multiple shortest paths, solvers may return different valid paths; distance equality is mandatory.

## Execution limits

Browser computation runs in a worker with a 15-second per-solver timeout, starting after runtime initialization. Trace recording stops after 50,000 events without stopping the solver. Runtime download/loading has a separate 60-second timeout and a retry action. Native benchmark timing has a 30-second timeout per invocation.

## Portfolio acceptance

1. A visitor can load a curated example, edit it, run both algorithms, and inspect final distances and a path.
2. Playback describes real execution and distinguishes tentative labels from algorithm-certified completion.
3. Every published benchmark has raw records, configuration, environment, commit, and correctness status.
4. The paper-to-code checklist identifies all deviations and review status.
5. A clean checkout can reproduce smoke tests and one small benchmark after documented setup.

## Later selector

Consider whole-run algorithm selection only after stable differences appear. Measure feature-extraction overhead and compare a held-out policy with always choosing Dijkstra. A result showing no benefit is acceptable. Never create an automatic BMSSP threshold solely from the paper's bound.

The 4–6-week estimate is a planning target. Completion gates take precedence over dates.
