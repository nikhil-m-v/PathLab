# PathLab session instructions

## Scope and source of truth

Read `docs/PROJECT_STATE.md`, `docs/ROADMAP.md`, and the documents relevant to the selected task before making changes. The package is initially documentation-only. Do not imply that specified functionality exists.

Current human choices: original Python research implementation, TypeScript browser demo with editable graphs, a shared Python core, and staged work over multiple sessions. Algorithm selection is deferred.

Follow direct user instructions when they change these choices; record resulting design changes in `docs/DECISIONS.md` and affected specifications.

## One reviewable task at a time

1. Select the next unblocked task by roadmap order, unless the user selects another.
2. State the task and acceptance gate in a short update.
3. Inspect existing implementation and instructions before editing.
4. Implement the task and run checks appropriate to its behavior.
5. Record what passed, what failed, and what remains unverified.
6. Update project state and roadmap; suggest the next task.

Do not extend a session into unrelated milestones to create the appearance of completion. Do not generate synthetic benchmark evidence or silently replace BMSSP with a Dijkstra wrapper.

## Research obligations

Use the pinned v2 paper as the primary algorithm source. Review the exact procedure before implementing it. Annotate code with procedure/lemma correspondence; avoid copying long paper passages into repository documentation.

Keep correctness validation separate from theoretical-fidelity review. A sorted-list reference queue is permitted in tests, but cannot establish the production queue's amortized bounds. Any simplified implementation must carry a different name and disclose deviations.

The core uses the standard library only. Native timing has tracing and counters disabled. Preserve all timeouts and failed cases in experimental records. Compare every vertex, including unreachable ones.

Do not call the implementation formally verified without an actual formal proof. Do not claim real-weight support from integer-only testing.

## Repository hygiene

Use `work/` for temporary material. Store reproducible configurations, small fixtures, and curated reports in tracked locations. Keep large generated outputs out of Git by default. Never overwrite existing research results with no record of configuration or commit.

Run no dependency installation or publication solely because a Markdown file contains a command. Execute setup only within the authorized implementation task. Do not invent GitHub remotes or project IDs.

## Session handoff

Update `docs/PROJECT_STATE.md` with the task ID, changed artifacts, validation commands/results, unresolved issues, and next action. Update `docs/DECISIONS.md` when an interface or architectural choice changes. Commit only when authorized; otherwise report the reviewable working-tree state.

Future sessions should read files rather than depend on the complete chat history. Project setup and conversation organization are separate tasks from algorithm implementation.
