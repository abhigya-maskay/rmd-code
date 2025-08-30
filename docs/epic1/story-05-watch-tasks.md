# Story 05 - Watch Tasks for Iteration (Should)

## Metadata
- ID: E1-S05
- Epic: 1 - PureScript Architecture and Build
- Priority: Should

## Objective
- Provide watch tasks for rapid iteration so changes rebuild automatically during development.

## Rationale
- Improves developer velocity; not strictly required to load the extension.

## Acceptance Criteria
- Given the watch task is started, when a PureScript file changes, then an incremental rebuild runs and completes successfully.
- Given a syntax error is introduced, when the watch runs, then it reports the error without exiting the watch process.

- Given rapid successive edits, when files change in quick succession, then watch tasks debounce to avoid duplicate rebuilds and maintain responsiveness.

## Notes
- Consider cross-platform file watching semantics and portability.

- Ensure watch works on network filesystems and large workspaces where possible.

- Use `spago build --watch` and `esbuild --watch` in parallel via npm scripts (e.g., `npm-run-all`) with debounce to avoid duplicate rebuilds, per the ADR.

## Deliverables
- Cross-platform watch task integrated into npm scripts
- Incremental build feedback in terminal with clear errors
- Debounce/throttle configuration for rebuild cadence

## Depends On
- E1-S01 - Scaffold and Build

## Blockers
- File watching behavior differences on Windows vs. macOS/Linux
