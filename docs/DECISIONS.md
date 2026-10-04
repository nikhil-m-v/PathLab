# Decision log

All decisions below are **accepted specifications**, dated 2026-10-04. Acceptance does not imply implementation or validation. New entries supersede old entries explicitly; do not rewrite history to imply an earlier choice was never made.

## ADR-001 — Benchmark plus educational demo

Combine reproducible native experiments with a browser explanation using actual traces. A benchmark alone is less accessible to visitors; a visualizer alone offers less research evidence. An automatic selector is deferred until held-out measurements support it.

## ADR-002 — Original Python implementation

The user chose Python + TypeScript and ownership of the research core. Use pure Python rather than adapting an external solver. Consequence: performance conclusions apply to tested Python implementations, not to all implementations of the mathematical algorithms.

## ADR-003 — Shared core through Pyodide

Use a module worker to run the same core in the browser. Alternatives were curated-only playback or a hosted Python service; the user chose editable graphs with execution. Consequences: initial runtime download, explicit limits, cancellation by worker termination, and native/browser parity testing. No backend is required.

## ADR-004 — Integer first-release domain

Use nonnegative integer weights to support exact validation. Reject floats and negative weights. Native large integers use exact decimal-string JSON when needed; browser weights are bounded numeric integers. This is narrower than the paper's real-weight setting and must be disclosed.

## ADR-005 — Complete answers independent of traces

Trace recording is capped but solving continues. Replay exposes only recorded state, while final answers remain separately available. Alternatives such as fabricating a terminal trace event would confuse incomplete playback with actual execution.

## ADR-006 — Strong baseline and separate instrumentation

Use `heapq` Dijkstra with lazy stale-entry removal. Time without observers; collect counters and allocation peaks separately. Report BMSSP preprocessing and core timings as well as end-to-end time. Do not equate fewer heap operations with a proved runtime bound.

## ADR-007 — Research queue and source audit

Target the paper's block queue with a deterministic AVL boundary index and deterministic selection. A sorted-list implementation is reserved for test reference behavior. Complexity fidelity remains a written review obligation independent of answer tests.

## ADR-008 — Stable IDs and original-edge paths

Preserve multigraph edge identities, canonicalize internal indexing, and map transformed results back to original IDs. Solvers may choose different shortest paths; validate distances and each returned path rather than demanding identical trees.

## ADR-009 — Static release and minimal UI dependencies

Use React reducers/contexts and SVG; no state manager or graph rendering package is initially required. npm and uv own separate locks. Static GitHub Pages deployment is the chosen first release target. Exact dependency versions are fixed only after bootstrap compatibility checks.

## ADR-010 — Stage gates and portable handoff

Documentation is the current deliverable. Future sessions follow roadmap task IDs and update project state. Organize a dedicated project after preserving the package; do not assume the application supports automatically migrating all existing chats. The 4–6-week estimate is flexible.

## Future entry template

```text
ADR identifier and title:
Date:
Status: proposed / accepted / superseded
Context and evidence:
Decision:
Alternatives considered:
Consequences and validation required:
Documents/tasks affected:
Supersedes (if applicable):
```
