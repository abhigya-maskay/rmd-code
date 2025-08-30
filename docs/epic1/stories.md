# Epic 1 - PureScript Architecture and Build

## Objective
- Compile PureScript into a VS Code extension that activates on `.Rmd` files across macOS, Windows, and Linux.

## Success Criteria
- Extension loads on latest VS Code on macOS, Windows, and Linux without runtime errors.
- Build pipeline produces a bundle the extension host can execute.
- Activation occurs only when a workspace contains or opens `.Rmd` files.

## In Scope (from Epic)
- Spago project setup, FFI shims, bundler configuration, watch tasks, source maps, activation by `.Rmd`.

## Non-Goals (deferred to other epics)
- VS Code API typed wrappers and commands (Epic 2).
- Language ID/grammar and syntax highlighting (Epic 3).
- R LSP integration and chunk intelligence (Epic 4).
- Execution engine, knit/preview pipelines, telemetry, packaging, docs (other epics).

## Constraints & Dependencies
- Must target the VS Code extension host (Node/Electron) environment and its supported Node version.
- Uses PureScript as the primary source language with JavaScript FFI where required.
- Cross-platform build scripts must not rely on OS-specific shells or path conventions.

## MoSCoW Backlog

## Must
1) [E1-S01] Story: As a developer, I want a PureScript project scaffolded with a working build and minimal FFI so that the extension can compile into a bundle the VS Code host can run on all target OSes.
   - Business rationale (fail-without-it): No executable bundle -> the extension cannot load.
   - Acceptance criteria
     - Given a clean checkout and documented commands, when the build is executed, then the project compiles without errors on macOS, Windows, and Linux.
     - Given the produced bundle, when inspected, then it includes required FFI stubs and entrypoints for the extension host to load without missing module errors.
     - Given a missing or misconfigured dependency, when the build runs, then a clear, actionable error message is surfaced.

2) [E1-S02] Story: As a developer, I want bundling configured and the extension activated by `.Rmd` so that the extension loads only when relevant files are present.
   - Business rationale (fail-without-it): Without bundling + activation, the extension either won't load or loads unnecessarily.
   - Acceptance criteria
     - Given the extension is installed in latest VS Code, when a workspace opens a `.Rmd` file, then the extension activates.
     - Given no `.Rmd` files are opened, when VS Code starts, then the extension does not activate.
     - Given the bundle is produced, when the extension activates, then no runtime import/require errors occur.

3) [E1-S03] Story: As a developer, I want cross-platform verification of build and load so that the extension is reliable on macOS, Windows, and Linux.
   - Business rationale (fail-without-it): Lack of cross-OS reliability blocks adoption and testing.
   - Acceptance criteria
     - Given the documented build command, when run on macOS, Windows, and Linux, then the build succeeds on each OS.
     - Given the built extension, when installed on latest VS Code on each OS, then it loads without runtime errors.

## Should
4) [E1-S04] Story: As a developer, I want source maps for PureScript output so that I can debug issues in source rather than generated code.
   - Rationale: Faster debugging reduces time-to-fix without affecting runtime.
   - Acceptance criteria
     - Given the extension runs in VS Code, when a breakpoint is set in PureScript source, then it maps to the correct runtime location.
     - Given an exception is thrown, when stack traces are inspected, then they include original source locations when feasible.

5) [E1-S05] Story: As a developer, I want watch tasks for rapid iteration so that changes rebuild automatically during development.
   - Rationale: Improves developer velocity; not strictly required to load the extension.
   - Acceptance criteria
     - Given the watch task is started, when a PureScript file changes, then an incremental rebuild runs and completes successfully.
     - Given a syntax error is introduced, when the watch runs, then it reports the error without exiting the watch process.

## Could
6) [E1-S06] Story: As a developer, I want a minimal activation log (e.g., to the output channel or console) so that I can confirm activation quickly.
   - Rationale: Aids verification and troubleshooting; safe to defer.
   - Acceptance criteria
     - Given the extension activates on `.Rmd`, when activation completes, then a one-line message indicates activation success.

## Won't (this epic)
- Typed VS Code API wrappers, sample commands, status bar items.
- Syntax highlighting, language contributions, or LSP features.
- Knit/preview flows, diagnostics mapping, telemetry, packaging.

## Assumptions & Open Questions
- Assumption: The exact bundler/tooling choice is flexible - Confidence: medium - Impact: low; multiple tools can satisfy requirements - Validation: pick/tool spike in Epic 1 implementation notes.
- Assumption: Activation is based on `.Rmd` language or file pattern - Confidence: medium - Impact: medium; incorrect activation harms UX - Validation: verify activation events in package manifest.
- Open question: Do we require case-insensitive `.rmd` activation on all OSes?
- Open question: Are there mandated Node/VS Code engine ranges we must pin to for the extension host?
- Dependency note: Spago and PureScript versions must be aligned to avoid FFI breakage.

## Quality Gates (for Must stories)
- Scope clear and business value explicit per story.
- Each Must includes a fail-without-it justification.
- Acceptance covers happy path and at least one error/edge (e.g., missing deps, no `.Rmd` open).
- Cross-OS build and load verified against latest VS Code.
- Non-goals listed to prevent scope creep into API wrappers/LSP/preview.
