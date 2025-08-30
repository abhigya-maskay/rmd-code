# Story 01 - Scaffold and Build (Must)

## Metadata
- ID: E1-S01
- Epic: 1 - PureScript Architecture and Build
- Priority: Must

## Objective
- Scaffold a PureScript project with a working build and minimal FFI so the VS Code extension host can execute the produced bundle on macOS, Windows, and Linux.

## Business Rationale (fail-without-it)
- No executable bundle means the extension cannot load at all.

## Acceptance Criteria
- Given a clean checkout and documented commands, when the build is executed, then the project compiles without errors on macOS, Windows, and Linux.
- Given the produced bundle, when loaded by the VS Code extension host, then all required FFI stubs and entrypoints resolve with no missing module errors.
- Given a missing or misconfigured dependency, when the build runs, then a clear, actionable error message is surfaced.

- Given the extension manifest, when validated, then `engines.vscode` is pinned to a supported range and targets the VS Code host Node version without warnings.
- Given the project metadata, when packaging is dry-run (e.g., via `vsce package --no-yarn`), then no manifest validation errors occur.

## Notes
- Targets Node/Electron environment supported by the VS Code extension host.

## Deliverables
- Spago project scaffold with PureScript sources
- Minimal JS FFI stubs and entrypoints
- Bundler config and build scripts for Node/Electron target
- Developer docs: build and run commands (docs/build.md or README section)

## Depends On
- None

## Blockers
- Decide bundler/tooling (e.g., esbuild/webpack/rollup)
- Confirm supported VS Code/Node engine versions
