# Research specification

**State:** specified; fidelity review and algorithm validation pending.

## Primary source

[Duan et al., v2, July 30, 2025](https://arxiv.org/html/2504.17033v2). Implement Algorithms 1–3 and the Lemma 3.3 data structure. Review the source directly during component tasks.

The result concerns nonnegative real-weight directed SSSP in the comparison-addition model, with bound `O(m log^(2/3) n)`. Its degree reduction uses zero-weight directed cycles. BMSSP returns a completed set and a boundary, including partial returns. Parameters are `k=floor(log(n)^(1/3))`, `t=floor(log(n)^(2/3))`, with top level `ceil(log(n)/t)`. Path order compares weight, vertex count, then reversed vertex sequence. Pivot discovery reduces the frontier; its visited set is not necessarily entirely complete. [Source](https://arxiv.org/html/2504.17033v2)

## Project notation and defaults

| Symbol | Meaning |
|---|---|
| `n`, `m` | Working graph vertex and edge counts |
| `d(v)` | True shortest distance, known only to an oracle in tests |
| `label[v]` | Current path label; numeric component is a distance upper estimate |
| `S`, `B`, `level` | Frontier, exclusive path-order bound, recursion level |
| `P`, `W`, `U` | Pivots, explored vertices, returned completed set |

Use base-2 logarithms and integer-safe parameter computation. Handle a singleton reachable graph directly; clamp `k,t` to at least one for tiny graphs. Calculate parameters on the reachable, transformed working graph and record both original and working counts. Parameter overrides are experimental settings, never silently applied to the paper configuration.

## Adaptation obligations

- Restrict the wrapper to source-reachable vertices; restore unreachable results afterward. Include reachability in end-to-end preprocessing time.
- Create a deterministic cycle node for each edge incidence, with mappings for original vertices and edges; handle isolated singleton graphs separately. Preserve parallel-edge identities and source representatives.
- Implement path-order comparison centrally. Do not approximate ties with floating epsilons. Test tie propagation and predecessor acyclicity before integration.
- Use an original deterministic AVL boundary index and block queue; median selection must not silently become full sorting. Review actual amortized costs independently of functional tests.
- Keep a sorted reference queue solely for oracle tests. Label any alternative production queue as a variant with a disclosed complexity status.

## Invariant review checklist

| ID | Obligation | Evidence required |
|---|---|---|
| R1 | Label updates represent actual source paths | Generated and adversarial fixtures |
| R2 | Completion/bounds match recursive contracts | Oracle-backed debug assertions |
| R3 | Tie handling keeps predecessor structure acyclic | Zero-cycle and equal-path tests |
| R4 | Frontier and pivot conditions hold | Component fixtures and lemma review |
| R5 | Queue operations preserve separators and unique best keys | Reference-model operation sequences |
| R6 | Partial and successful returns are correct | Explicit branch-exercising tests |
| R7 | Transformation preserves original answers | Distances and mapped-path checks |
| R8 | Production operations support claimed costs | Written operation-cost audit |

Status begins **pending** for every item. Correct answers alone do not discharge R8. A fidelity report must name deviations, reviewers, test evidence, and the exact source revision before release.
