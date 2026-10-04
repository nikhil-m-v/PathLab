# Dependencies and environment setup

**State:** dependencies selected by role; none installed or locked by this documentation task. Paths to bundled Python/Node were discovered, but their versions and usability have not been validated.

## Required toolchain

| Area | Choice | Isolation |
|---|---|---|
| Native runtime | CPython 3.12+ | Record exact minor/patch in reproducibility metadata |
| Python management | uv | Local virtual environment, `pyproject.toml`, `uv.lock` |
| Core/CLI | Standard library, including dataclasses, typing, heapq, argparse, json | No runtime third-party requirements |
| Python build | hatchling | Build-system dependency, not solver runtime |
| Tests | pytest, hypothesis | `dev` dependency group |
| Quality | ruff, mypy | `dev` dependency group |
| Reports | matplotlib | `reports` optional dependency group |
| Native experiments | Standard library timers, multiprocessing, tracemalloc | No report imports in timed processes |
| Frontend | React, React DOM, TypeScript, Vite, React Vite plugin | `frontend/package.json` |
| Browser Python | Pyodide, pinned release | Worker only; matching runtime assets |
| Component tests | Vitest, jsdom, React Testing Library, jest-dom, user-event | Frontend dev dependencies |
| Browser integration | Playwright | Frontend dev dependency; Chromium installed explicitly |
| Frontend quality | ESLint, TypeScript ESLint, React hooks/refresh plugins | Frontend dev dependencies |
| Delivery | Git, GitHub Actions, GitHub Pages | Repository bootstrap/release stages |

Use npm and `package-lock.json`, not a mix of npm/pnpm/yarn lockfiles. Native baseline Node is 22.12+; bootstrap verifies the selected frontend packages' engines. The current [Vite guide](https://vite.dev/guide/) documents Node 20.19+ / 22.12+ compatibility.

## Version policy

At bootstrap, inspect available runtimes, choose mutually compatible stable releases, record exact versions, resolve locks, and run the smoke checks. Never commit invented locks or assume every newest package works with the selected runtime. Pin the Pyodide version and asset URL together; record its embedded Python version. Do not dynamically use `latest` in deployment assets.

The Python core targets 3.12 syntax/API compatibility; test the chosen native runtime and actual browser runtime. Use native dependency groups for tools unavailable or unnecessary inside Pyodide. CI uses locked installs. Dependency upgrades are separate tasks with parity and build checks.

## Windows setup recipe

Commands below are a future bootstrap recipe, not a claim that current manifests exist. Install uv through the [official instructions](https://docs.astral.sh/uv/getting-started/installation/) if unavailable; do not assume a global installation or a machine-specific path.

```powershell
# At the future repository root, once manifests exist:
python --version
node --version
npm --version
uv --version
git --version

# First resolution during bootstrap:
uv lock
uv sync --group dev --extra reports

# Subsequent reproducible setup:
uv sync --locked --group dev --extra reports
uv run pytest
uv run ruff check .
uv run mypy src/pathlab

Set-Location frontend
# First resolution during bootstrap: npm install
# Subsequent setup:
npm ci
npm exec playwright install chromium
npm run typecheck
npm run lint
npm run test -- --run
npm run test:e2e
npm run build
npm run dev
```

Planned npm scripts: `dev`, `build`, `preview`, `typecheck`, `lint`, `test`, `test:e2e`, `bundle:python`. `build` invokes the Python-bundle copy script before Vite. No Python algorithm reimplementation occurs in that script.

## Smoke acceptance

- A fresh virtual environment imports the package without report/test dependencies.
- The `pathlab` entry point responds to `--help`.
- Frontend builds with the locked Node/npm toolchain.
- A module worker loads pinned Pyodide and imports a trivial function from the copied core.
- The same fixture reaches Python and returns identical data in native/browser environments.
- The production preview serves worker and bundle files under the repository subpath.

Use [Pyodide module-worker guidance](https://pyodide.org/en/stable/usage/webworker.html), [Hypothesis documentation](https://hypothesis.readthedocs.io/en/latest/), and [Playwright documentation](https://playwright.dev/docs/intro) as primary setup references. No dependency installation belongs to Stage 0.
