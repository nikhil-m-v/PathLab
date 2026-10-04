# PathLab

An open research project connecting shortest-path algorithms, reproducible experiments, and an interactive explanation.

**Status — 2026-10-04:** documentation created. Algorithms, tests, dependency locks, benchmarks, website, and deployment are not implemented. No performance results are claimed.

## Research question

How can an algorithm have a better theoretical bound yet run slower, and what does its execution look like?

The first release will pair an original Python implementation of the 2025 directed shortest-path algorithm with a strong Python Dijkstra baseline. Visitors will edit small directed graphs and explore execution traces in a TypeScript interface. Native Python experiments will investigate performance independently of browser animation.

## Start here

| Document | Purpose |
|---|---|
| [Project specification](docs/PROJECT_SPEC.md) | Goals, features, limits, acceptance |
| [Research specification](docs/RESEARCH_SPEC.md) | Source revision, fidelity and proof obligations |
| [System design](docs/SYSTEM_DESIGN.md) | Execution, failure handling, boundaries |
| [Architecture](docs/ARCHITECTURE.md) | Modules, packaging and deployment |
| [Element design](docs/ELEMENT_DESIGN.md) | UI components and interactions |
| [Class design](docs/CLASS_DESIGN.md) | Classes, protocols and diagrams |
| [Interfaces](docs/INTERFACES.md) | Python, JSON, workers, CLI and records |
| [Dependencies](docs/DEPENDENCIES.md) | Setup requirements and future commands |
| [Test strategy](docs/TEST_STRATEGY.md) | Correctness and experimental controls |
| [Roadmap](docs/ROADMAP.md) | Ordered tasks and completion gates |
| [Project state](docs/PROJECT_STATE.md) | Resume point and session handoff |
| [Decisions](docs/DECISIONS.md) | Design rationale and change history |
| [Agent instructions](AGENTS.md) | Rules for implementation sessions |

## Planned usage

These commands become available after the corresponding roadmap tasks pass. They do not run against the current documentation-only package.

```powershell
uv sync --locked --group dev
uv run pathlab solve examples/branching.json --algorithm dijkstra
uv run pathlab solve examples/branching.json --algorithm bmssp --trace work/branching.trace.json
uv run pathlab benchmark benchmarks/configs/smoke.json --output results/smoke
Set-Location frontend
npm ci
npm run dev
```

## Release evidence

The release will provide a live static demo, a paper-to-code fidelity checklist, raw benchmark records, a generated report, CI results, and reproducible setup instructions. Passing tests means tested correctness, not formal verification. Any complexity claim requires a separate implementation audit.

## Research sources

- [Duan et al., paper revision v2, July 30, 2025](https://arxiv.org/html/2504.17033v2).
- [Castro et al., experimental study revision v3](https://arxiv.org/abs/2511.03007v3): context for the practical performance question, not PathLab results.

MIT is the selected license for original project code. Add the actual license with the repository bootstrap. Research attribution and any external asset licenses must be retained.
