# Epics

Scope locked. Final epics below.

## Epic 1: PureScript Architecture and Build

### Goal
Compile PureScript to a VS Code extension.

### Deliverables
Spago project, FFI shims, bundler config, watch tasks, source maps, activation by `.Rmd`.

### Acceptance
Extension loads on latest VS Code on macOS, Windows, Linux.

## Epic 2: VS Code API FFI Layer

### Goal
Typed wrappers for required VS Code APIs.

### Deliverables
Commands, window, workspace, languages, notebooks (NotebookController, NotebookSerializer, cell outputs), diagnostics, configuration modules.

### Acceptance
Sample command and status bar item work end-to-end.

## Epic 3: Language ID and Grammar

### Goal
First-class `rmd` language.
### Deliverables
`package.json` contributions, TextMate injection over Markdown, YAML front matter, \`\`\`{r} fences, chunk option tokens, Notebook serializer for `.Rmd`: map YAML front matter, Markdown cells, and \`\`\`{r} chunks to notebook cells with round-trip save fidelity.
### Acceptance
Correct highlighting for headers, YAML, chunk headers, R code blocks.

## Epic 4: Embedded R Intelligence

### Goal
LSP features inside R chunks.
### Deliverables
Virtual documents per chunk, range mapping, bridge to R `languageserver`, diagnostics scoping.
### Acceptance
Completion, hover, signatures, and errors appear only within R fences with correct ranges.

## Epic 5: Chunk Execution Engine

### Goal
Execute chunks with shared state.
### Deliverables
NotebookController-based execution: single long-lived R session per notebook, queueing and cancel, capture stdout/stderr and rich outputs via NotebookCellOutput (text/plain, image/png/html), environment reuse across cells.
### Acceptance
Run cell, run above, run all, and cancel behave predictably via notebook UI.

## Epic 6: Knit and Render Pipeline

### Goal
One-click render to HTML and PDF.
### Deliverables
Calls to `rmarkdown::render()`, TinyTeX detection and helper, Pandoc discovery, manual knit and knit-on-save triggers, progress UI, cancellation.
### Acceptance
HTML and PDF artifacts produced or failures surfaced with actionable messages.

## Epic 7: Incremental Preview

### Goal
Low-latency cell output updates.
### Deliverables
Notebook cell outputs with streaming updates where applicable; re-run updates only the affected cell's outputs; attachments for images/HTML.
### Acceptance
Re-running a cell updates only that cell's output; outputs render correctly for text, images, and HTML.

## Epic 8: Diagnostics and Log Mapping

### Goal
Map errors back to source lines.
### Deliverables
Parsers for knitr and rmarkdown logs, file/line extraction, VS Code Diagnostics, quick navigation.
### Acceptance
Clicking a problem focuses the failing line or chunk header.

## Epic 9: Authoring Ergonomics

### Goal
Faster `.Rmd` editing.
### Deliverables
Snippets (YAML front matter, setup chunk, common chunk options), notebook cell toolbar and run controls, outline view for headers and chunks, folding, insert-cell commands (R/Markdown), keybindings. (Optional fallback CodeLens when editing as plain text.)
### Acceptance
Users navigate by outline and run via CodeLens without friction.

## Epic 10: Configuration and Environment Detection

### Goal
Predictable discovery with sane defaults.
### Deliverables
R, rmarkdown, knitr, Pandoc, TinyTeX checks; PATH probing and common install paths; user overrides; status bar indicators; settings for knit triggers.
### Acceptance
Missing deps reported with clear remediation; overrides respected.

## Epic 11: Security and Trust

### Goal
Safe execution and preview.
### Deliverables
Workspace Trust gating for execution and knit, strict CSP, local-resource allowlist, sanitized HTML injection, temp-dir isolation.
### Acceptance
Untrusted workspaces block execution with clear rationale.

## Epic 12: Performance and Telemetry

### Goal
Fast startup and zero-leak telemetry.
### Deliverables
Lazy activation on `.Rmd` and commands, render cache, profiling hooks; telemetry disabled by default with stub plumbing.
### Acceptance
Activation under target threshold; no telemetry emitted unless explicitly enabled.

## Epic 13: Testing and CI

### Goal
Reliable cross-platform behavior.
### Deliverables
Unit tests for parsers and FFI, integration tests with Extension Test Runner, snapshot tests for logs and preview HTML, GitHub Actions matrix for macOS, Windows, Linux.
### Acceptance
Green CI with deterministic snapshots.

## Epic 14: Packaging, Docs, Samples, and License

### Goal
Ship and teach.
### Deliverables
`vsce` packaging, Marketplace assets, quickstart, troubleshooting, dependency guide (TinyTeX and Pandoc), sample `.Rmd` project, MIT License.
### Acceptance
Marketplace validation passes and quickstart reproduces on all OS targets.

## Out of Scope

- Remote dev (SSH, WSL, containers)
- Non-R chunks.
