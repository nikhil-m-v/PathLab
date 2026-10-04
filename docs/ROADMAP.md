# Implementation roadmap

**State:** Stage 0 complete for documentation consistency; verification is recorded in [project state](PROJECT_STATE.md). Mermaid rendering is deferred to bootstrap. All software tasks are pending.

## Operating model

Work by acceptance gates, not by a fixed number of prompts. A session normally finishes one task; split a difficult task into recorded subtasks. Stop at a reviewable boundary with evidence and a handoff. The original 4–6-week estimate may expand for research validation.

Task status values: pending, active, implemented, validated, blocked. `Implemented` is not equivalent to `validated`. Mark a stage complete only after its gate passes. Dependency arrows below refer to prerequisite task IDs.

## Tasks and gates

| ID | Task | Prerequisite | Deliverable / acceptance |
|---|---|---|---|
| D00 | Documentation package | None | All 14 Markdown files, diagrams and contracts present |
| D01 | Documentation consistency | D00 | Relative links, examples, fences, limits and status reviewed |
| P00 | Dedicated project organization | D01 | Select persistent local home; preserve package/history; record project context without assuming chat migration |
| B01 | Runtime and repository bootstrap | D01 | Inspect runtimes; create manifests, locks, Git hygiene, license; imports and CLI help pass |
| B02 | Frontend/worker feasibility | B01 | TypeScript shell loads the shared Python bundle in Pyodide under the deployment base path |
| B03 | CI baseline | B02 | Locked install, smoke tests, typing/lint and production build pass |
| A01 | Graph model and serializers | B01 | Exact-ID normalization, validation, JSON fixtures and integer round trips |
| A02 | Independent oracle | A01 | Bellman–Ford test oracle validated on manually solved cases |
| A03 | Dijkstra and solve CLI | A02 | Heap baseline, valid predecessors, full-vertex oracle agreement |
| R01 | Detailed source audit | A03 | Procedure annotations, research invariants, tie and parameter test plan reviewed |
| R02 | Labels and tie handling | R01 | Path order, predecessor propagation, zero-cycle tests pass |
| R03 | Reachability and degree transform | R02 | Oracle preservation and original-edge path mapping pass |
| R04 | AVL boundary index and selection | R02 | Balance/search/update invariants; deterministic selection tests and operation-cost notes |
| R05 | Partial-order queue | R04 | Model-based insert/prepend/pull tests plus amortized-operation review |
| R06 | Pivot discovery and bounded base case | R03 | Oracle-backed bound/frontier cases and early returns pass |
| A04 | BMSSP recursive integration | R05,R06 | Recursion and partial returns exercised; generated oracle agreement |
| A05 | Trace and observer contract | A04 | Event replay fixtures, cap behavior, result independence, observer-off path |
| E01 | Generators and benchmark harness | A04 | Canonical seeds, verification, timeouts, raw environment/config records |
| E02 | Diagnostics and report | E01,A05 | Separate counter/memory runs; reproducible plots with censored outcomes |
| U01 | Graph editor and import/export | B03,A01 | Browser input limits, keyboard forms, parallel edges and loops |
| U02 | Run controller | U01,A05 | Sequential comparison, cancellation, stale-result rejection, native/browser parity |
| U03 | Playback and inspectors | U02 | Seek/back checkpoints, frame tree, queue view, honest truncation display |
| U04 | Examples and benchmark viewer | U03,E02 | Guided cases and charts linked to actual records |
| L01 | Release audit | U04 | Fidelity report, CI, clean-checkout smoke reproduction, README and demo recording |
| L02 | Static deployment | L01 | Repository-subpath preview and deployed browser smoke pass |
| H01 | Selector feasibility | L02 | Held-out evaluation includes feature overhead; proceed only if useful |

P00 organizes work but need not block local B01 implementation if a project home is not yet selected. Do not create a new chat merely because P00 is listed; wait for the user's project-organization request.

## Stage gates

| Stage | Tasks | Completion gate |
|---|---|---|
| 0 Documentation | D00–D01 | Consistent package, honest status, resume point |
| 1 Bootstrap | B01–B03 | Reproducible environment and browser Python import |
| 2 Baseline | A01–A03 | Independent oracle and tested Dijkstra CLI |
| 3 Research components | R01–R06 | Each component passes contract tests and source review |
| 4 Integration | A04–A05 | BMSSP correctness and actual bounded traces |
| 5 Experiments | E01–E02 | Raw records, failures/timeouts, explanatory report |
| 6 Demo | U01–U04 | Editable graph, parity, replay and guided explanation |
| 7 Release | L01–L02 | Reproducible portfolio evidence and working static site |
| 8 Optional selector | H01 | Demonstrated value on held-out cases or documented no-go |

## Session-sized work and uncertainty

Bootstrap/baseline tasks often fit one or two sessions; queue/tie/recursion work may require several. Do not promise component completion by calendar date. If progress is slow, reduce UI polish or benchmark breadth, not oracle coverage or fidelity disclosure.

At the end of every session record: task ID; what changed; checks and outcomes; source references reviewed; open issues; next unblocked task. Research disagreements get a decision entry with evidence rather than silent edits.

Project migration must preserve the documentation folder and any future uncommitted artifacts. Set up a project around the chosen local repository; use project-state handoffs to connect future chats. Preserve this conversation even if a product does not support moving it.
