# Interfaces

**State:** v1 contract specification; adapters and schema tests pending. All examples below are illustrative fixtures, not outputs from implemented code.

## Python API

```python
def solve(
    graph: Graph,
    source: str,
    algorithm: Literal["dijkstra", "bmssp"],
    *,
    trace: bool = False,
) -> SolveResult: ...

# Internal protocol: normalized dense index and an optional observer.
class Solver(Protocol):
    def solve(self, graph: Graph, source: int,
              observer: ExecutionObserver | None = None) -> SolveResult: ...
```

The public adapter validates and normalizes input; classes receive normalized immutable graphs. Trace collection is attached by the adapter. Counter collection is a separate runner option. The returned result includes an optional trace envelope when requested. Default diagnostics contain configuration and counts of original/working vertices, not instrumentation-dependent operation totals.

## Graph document v1

```json
{
  "schemaVersion": 1,
  "vertices": [
    {"id": "A", "position": {"x": 100, "y": 100}},
    {"id": "B", "position": {"x": 260, "y": 100}},
    {"id": "C", "position": {"x": 260, "y": 240}}
  ],
  "edges": [{"id": "e1", "source": "A", "target": "B", "weight": 7}],
  "source": "A"
}
```

Required fields: schema version, vertices, edges, source. Positions are optional finite coordinates. Unknown fields are rejected except an optional `metadata` object, which solvers ignore. IDs are case-sensitive. Normalize vertices and edges by lexicographic ID order for reproducibility; adjacency ordering follows normalized edge order. Input arrays remain presentation order only.

For JSON exact integers, use numbers only within `0..9007199254740991`. Larger native weights/distances use canonical decimal strings with no sign or leading zeros. Browser inputs reject strings and apply tighter product limits. Native adapters accept both representations. Negative, fractional, Boolean, null, and noncanonical values are invalid weights. Reject unsupported schema versions with a clear message.

## Result document v1

```json
{
  "schemaVersion": 1,
  "algorithm": "dijkstra",
  "source": "A",
  "distances": {"A": 0, "B": 7, "C": null},
  "predecessorEdges": {"A": null, "B": "e1", "C": null},
  "diagnostics": {"originalVertices": 3, "workingVertices": 3},
  "trace": null
}
```

Distance records contain every original vertex. Source predecessor and unreachable predecessors are null. Predecessors identify original edges, not only predecessor vertices. Path reconstruction validates endpoint continuity and total weight; repeated vertices indicate an error. JSON emits no Infinity or NaN. Additional native integers follow the same exact-integer encoding.

## Trace envelope v1

Fields: `schemaVersion`, `algorithm`, `graph`, `bundleId`, `events`, `truncated`, `recordedEvents`, `totalEvents`, `lastSequence`. `totalEvents` is null after recording stops; it must not be guessed. Final results are transmitted independently of trace completeness.

Events use monotonically increasing zero-based `sequence`, `kind`, nullable `frameId`, and `payload`. Bounds are path-order labels, not scalar distances: `{distance, vertexCount, endpointIndex}`; null means positive infinity. Trace-visible internal IDs are dense indices with a separately supplied original/working mapping in `run_started`.

| Event kind | Payload and replay effect |
|---|---|
| `run_started` | Initial labels, ID maps, configuration; initialize replay |
| `edge_examined` | Edge and endpoints; update current-edge highlight |
| `label_updated` | Vertex, old/new label, predecessor edge; replace label and predecessor |
| `queue_upserted` | Queue ID, key, label/version; replace logical queue entry |
| `queue_removed` | Queue ID, key/version, reason including stale; remove only matching logical version |
| `frame_entered` | ID, parent, level, bound, frontier; add and activate frame |
| `pivots_selected` | Frame, pivot IDs, explored IDs; replace frame pivot/exploration display |
| `batch_prepended` | Queue ID and inserted items; apply queue upserts |
| `batch_pulled` | Queue ID, removed keys, separator; remove keys and update boundary |
| `vertices_completed` | Vertex IDs and certifying frame; mark algorithm-certified completion |
| `frame_returned` | Frame, boundary, completed set, partial/success flag; close frame |
| `run_finished` | Completion indicator; do not substitute final distances for missing events |

Emit state deltas from actual code transitions. Queue display is logical contents, not a physical heap memory dump. Frame creation precedes references to that frame. Record transformed state for expert inspection and map original vertices explicitly; never infer original completion from a single transformed representative without a justified mapping rule.

Recording stops at 50,000 events. No extra terminal event is forced into a truncated trace. Checkpoints every 250 recorded events are derived by the frontend and are not part of the solver event contract. Browser trace imports require matching supported schema, valid IDs/sequences, a browser-sized embedded graph, and at most 50,000 events.

## Worker protocol

Main-to-worker messages:

```json
{"type":"solve","requestId":"run-1","revision":4,"algorithm":"dijkstra","graph":{"schemaVersion":1,"vertices":[{"id":"A"}],"edges":[],"source":"A"},"trace":true}
```

Worker responses are `ready` (runtime version and bundle ID), `result` (request ID, revision, result), or `error` (request ID, revision, code, message). Error codes: `INVALID_GRAPH`, `UNSUPPORTED_SCHEMA`, `SOLVER_ERROR`, `RUNTIME_ERROR`. Main-thread cancellation/timeouts are local outcomes: terminate the worker rather than sending an interrupt that Python might never process. No arbitrary code field is accepted.

## CLI contract

```text
pathlab solve GRAPH --algorithm {dijkstra,bmssp} [--output FILE] [--trace FILE]
pathlab benchmark CONFIG --output DIRECTORY
pathlab report RESULTS_DIRECTORY --output DIRECTORY
```

When omitted, solve output goes to stdout; diagnostics go to stderr. Exit codes: 0 success, 2 invalid input/configuration, 1 execution/validation failure. A benchmark with any mismatch or timeout saves its records and exits 1. Report generation never rewrites raw records.

## Benchmark contract

Configuration fields: version, families, sizes, degree settings, weight distribution, seeds, sources, algorithms, warmup count (2), repetitions (7), timeout seconds (30), parameter mode. Save expanded configuration before execution.

One JSONL record per invocation includes run ID, case ID, phase (`correctness`, `warmup`, `timing`, `counters`, `memory`), algorithm, family, original/working counts, seed, source, repetition, status, duration, preprocessing/core/end-to-end durations when measured, correctness, graph hash, configuration hash, commit/dirty state, Python/runtime version, OS/CPU, and diagnostic message. Missing measurements are null.

Statuses: `ok`, `mismatch`, `timeout`, `error`, `skipped`. Counter and memory values occur only in their own phases. Timing summaries group by identical case/config/environment; report medians, quartiles, successful sample count, and all censored outcomes. Never combine native and browser measurements or silently aggregate multiple hardware environments.
