# Technical Architecture: Epic 1 (PureScript Build)

Date: 2025-08-30
Status: Draft

This document consolidates concise ADRs for key technical decisions in Epic 1, plus a simple architecture overview and an NFR checklist. Each ADR includes a "No change" baseline option and leaves the Decision section for approval.

## Architecture Overview

Context Summary
- Build a VS Code extension written in PureScript, bundled for the extension host (Node/Electron), and activated only for `.Rmd` content. Cross-platform build/dev experience is required.

Primary Blocks & Data Flow
```mermaid
flowchart LR
  Dev[PureScript Sources]
  FFI[JS FFI Shims]
  B[Bundler]
  O[Node-target Bundle]
  VS[VS Code Extension Host]

  Dev --> B
  FFI --> B
  B --> O
  O --> VS

  subgraph Activation
    W[workspaceContains **/*.{Rmd,rmd}] --> A[Extension Activation]
  end
  VS --> A
```

Key Notes
- Target the VS Code extension host's Node/Electron runtime; avoid browser/electron-only APIs.
- Keep scripts OS-agnostic; prefer Node/npm scripts with cross-platform tooling.
- Plan to migrate activation to language ID once grammar/ID exists in a later epic.

## NFR Prompt Checklist (Targets TBD)
- Performance: P95 activation latency, bundle size cap.
- Availability/Reliability: Extension load success rate across OSes; error budget.
- Security/Privacy: Dependencies vetted; no network calls in Epic 1 scope.
- Scalability: Dev workflows should handle large workspaces without excessive watch churn.
- Cost/FinOps: Build times/tooling costs; CI minutes (matrix dry-run scope only).
- Operability: Clear build/watch logs; minimal activation log for verification.
- Observability: Minimal logging; add metrics/traces in later epics if needed.
- Compliance: None in scope for Epic 1.

---

# ADR: Bundler for Extension Bundle
Status: Accepted
Date: 2025-08-30

Context
- Need a reliable, fast bundler to produce a Node-target bundle from PureScript + JS FFI for the VS Code extension host.
- Drivers: cross-platform builds, source map support, simple config, fast dev watch.

Options
- A: esbuild (Node target)
- B: Rollup
- C: Webpack
- D: No change (use ad-hoc or Spago-only output without dedicated bundler)

Trade-offs
- A: esbuild
  - Pros: Very fast; simple config; great TS/JS support; solid source maps; good tree-shaking for ESM; easy watch.
  - Cons: Limited code splitting features vs Rollup/Webpack; some advanced plugins less mature.
  - Risks/Impact: Low complexity; aligns with dev velocity goals.
- B: Rollup
  - Pros: Excellent tree-shaking; rich plugin ecosystem; flexible outputs.
  - Cons: Slower than esbuild in dev; more config surface.
  - Risks/Impact: Higher maintenance cost for modest gains in this scope.
- C: Webpack
  - Pros: Mature; powerful; many loaders/plugins; familiar to many teams.
  - Cons: Heavier config; slower builds/watch; complexity not justified for simple extension bundle.
  - Risks/Impact: Overhead may slow iteration.
- D: No change
  - Pros: Minimal setup now; defer choice.
  - Cons: Risk runtime import/require errors; harder source maps/watch; unclear path to reliable bundle.
  - Risks/Impact: High risk of failing Epic 1 success criteria.

Decision Scope
- In: bundler choice and minimal config for Node/Electron target.
- Out: code splitting, advanced optimization, production profiling.

Decision
- Adopt esbuild for Node-target bundling with `platform: node`, `target` aligned to the VS Code host Node version, and `external: ['vscode']`.

Consequences
- Establishes esbuild as the bundler; informs watch setup and Source Maps ADR. Requires minimal config in repo.

Open Questions
- Any bundler-specific corporate preferences or constraints?

References
- Epic 1 stories E1-S01, E1-S02, E1-S04, E1-S05.

---

# ADR: Activation Trigger for .Rmd
Status: Accepted
Date: 2025-08-30

Context
- The extension must activate only when relevant. Grammar/language ID arrives in a later epic.
- Drivers: correctness, UX, and avoiding unnecessary activation.

Options
- A: `workspaceContains:**/*.{Rmd,rmd}` (file-pattern activation)
- B: `onLanguage:rmd` (post-Epic 3 when language ID exists)
- C: Hybrid (use A now; plan switch to B later)
- D: No change (broad activation like `*` or `onStartupFinished`)

Trade-offs
- A: File-pattern
  - Pros: Works now without language ID; avoids global activation.
  - Cons: Doesn't trigger on single-file open outside workspace; case handling needs `{Rmd,rmd}`.
  - Risks/Impact: Low; good interim choice.
- B: onLanguage
  - Pros: Precise activation on file open; best UX long-term.
  - Cons: Requires language contribution; not available in Epic 1.
  - Risks/Impact: Deferred until Epic 3.
- C: Hybrid
  - Pros: Pragmatic path; minimizes churn.
  - Cons: Requires follow-up ADR update/migration.
- D: No change
  - Pros: Simplest setup.
  - Cons: Activates unnecessarily or not at all; violates success criteria.

Decision Scope
- In: activation events in `package.json` for Epic 1.
- Out: language contribution/grammar definition (Epic 3).

Decision
- Adopt Hybrid: configure `workspaceContains:**/*.{Rmd,rmd}` file-pattern activation in Epic 1; plan to migrate to `onLanguage:rmd` when the language contribution lands in Epic 3. Document case expectations and consider `.RMD` if required.

Consequences
- Enables immediate, precise-enough activation without global load; creates a clear follow-up to switch to language-based activation later.

Open Questions
- Should `.RMD` uppercase be considered? Document OS-specific expectations.

References
- Epic 1 stories E1-S02, E1-S06.

---

# ADR: VS Code/Node Engine Pinning
Status: Accepted
Date: 2025-08-30

Context
- Pin `engines.vscode` and Node target to supported ranges to avoid runtime mismatches.
- Drivers: predictable runtime, reduced validation warnings, cross-OS reliability.

Options
- A: Pin to a recent VS Code engine range aligned with dependencies
- B: Pin to minimal required version for specific APIs used
- C: No change (loose or missing pin)

Trade-offs
- A: Recent range
  - Pros: Fewer deprecations; better debugging/source map support; aligns with latest VS Code.
  - Cons: Excludes older VS Code users.
- B: Minimal version
  - Pros: Broader compatibility window.
  - Cons: Higher risk of edge cases; may limit tooling features.
- C: No change
  - Pros: Less upfront research.
  - Cons: Validation warnings; unpredictable behavior across hosts.

Decision Scope
- In: `package.json` engines and Node target for bundler/tsconfig (if applicable).
- Out: Formal support policy across enterprise versions.

Decision
- Pin `engines.vscode` to a recent stable range (exact range TBD pending confirmation of supported baseline) and align the bundler Node `target` to the corresponding VS Code extension host Node version.

Consequences
- Cleaner manifest validation and predictable runtime; follow-up required to finalize the exact engine range and Node target once baseline is confirmed.

Open Questions
- Any mandated enterprise versions to support?

References
- Epic 1 E1-S01, E1-S03.

---

# ADR: Source Maps Policy
Status: Accepted
Date: 2025-08-30

Context
- Enable effective debugging while keeping prod bundles lean as needed.
- Drivers: dev velocity; meaningful stack traces; policy for prod artifacts.

Options
- A: Dev: inline/source maps; Prod: external, no inline sources
- B: Always external maps (dev and prod), no inline sources
- C: No source maps

Trade-offs
- A: Dev inline / Prod external
  - Pros: Best dev UX; controllable prod footprint.
  - Cons: Requires environment toggles and docs.
- B: Always external
  - Pros: Simpler policy; no inline sources in any build.
  - Cons: Slightly worse dev ergonomics vs inline.
- C: None
  - Pros: Simplest builds.
  - Cons: Poor debugging; slows delivery; not aligned with E1-S04.

Decision Scope
- In: bundler/source map configuration and docs.
- Out: sourcemap uploading or privacy tooling.

Decision
- Dev: use inline/source maps to optimize debugging in watch mode.
- Prod: generate external source maps without inline sources; consider excluding maps from published artifacts if policy requires.

Consequences
- Improves developer velocity while controlling production artifact size and information exposure; requires an environment toggle in build scripts.

Open Questions
- Any policy restrictions on embedding sources in maps?

References
- Epic 1 E1-S04.

---

# ADR: Watch Tasks Approach
Status: Accepted
Date: 2025-08-30

Context
- Rapid iteration with incremental rebuilds; resilient to syntax errors; cross-platform.

Options
- A: Use bundler watch plus Spago watch via npm-run-all (parallel)
- B: Node script with chokidar to orchestrate rebuilds
- C: No change (manual rebuild)

Trade-offs
- A: Bundler + Spago watch
  - Pros: Leverages native watch; simple npm scripts; good DX.
  - Cons: Coordination between processes; need debounce.
- B: Custom Node watcher
  - Pros: Full control over debounce/throttle and cross-platform specifics.
  - Cons: Extra code to maintain; potential reliability issues vs mature watchers.
- C: No change
  - Pros: Minimal setup.
  - Cons: Slow feedback; misses E1-S05 goals.

Decision Scope
- In: dev watch scripts and minimal debounce settings.
- Out: IDE task integration beyond npm scripts.

Decision
- Use `spago build --watch` alongside `esbuild --watch`, orchestrated via npm scripts (e.g., npm-run-all). Ensure debounce to avoid duplicate rebuilds and keep errors visible without terminating watch processes.

Consequences
- Fast, cross-platform feedback loop with clear, non-fatal error reporting; minimal bespoke code.

Open Questions
- Any constraints on parallel processes in dev environments?

References
- Epic 1 E1-S05.

---

# ADR: Cross-Platform Scripts Strategy
Status: Accepted
Date: 2025-08-30

Context
- Scripts must run on macOS, Windows, Linux without shell-specific assumptions.

Options
- A: Pure npm scripts with cross-env/shx where needed
- B: Node-based CLI scripts (JS) invoked from npm
- C: Bash/Powershell per-OS scripts
- D: No change

Trade-offs
- A: npm + cross-env/shx
  - Pros: Simple; well-trodden path; minimal code.
  - Cons: Adds small dependencies; some edge cases on older shells.
- B: Node CLI scripts
  - Pros: Maximum portability and control; easy to test.
  - Cons: Slightly more code to write.
- C: Per-OS scripts
  - Pros: Native to each OS.
  - Cons: Maintenance burden; diverging behavior; violates cross-platform goal.
- D: No change
  - Pros: No immediate work.
  - Cons: High risk of OS-specific failures; blocks E1-S03.

Decision Scope
- In: approach for build/test/watch scripts used in Epic 1.
- Out: Full CI implementation (future epic).

Decision
- Use npm scripts with cross-platform helpers (e.g., `cross-env`, `shx`) for build/test/watch orchestration. If dependency policy restricts these, fall back to small Node CLI scripts invoked from npm.

Consequences
- Consistent behavior across OSes with minimal maintenance; keeps commands discoverable via `npm run`.

Open Questions
- Any corporate restrictions on dev dependencies like cross-env/shx?

References
- Epic 1 E1-S03, E1-S05.

---

# ADR: Minimal Activation Logging Policy
Status: Accepted
Date: 2025-08-30

Context
- Provide a minimal activation message for verification; avoid noisy logs.

Options
- A: VS Code OutputChannel message on activation (dev default; prod guarded)
- B: Console-only (visible in Extension Host log)
- C: Disabled by default (enable via setting/flag)
- D: No change

Trade-offs
- A: OutputChannel
  - Pros: Clear and user-visible; easy to verify activation.
  - Cons: Must ensure minimal noise in prod.
- B: Console-only
  - Pros: No UI surface; still traceable.
  - Cons: Harder for users to find; less explicit for verification.
- C: Disabled default
  - Pros: Zero noise in prod by default.
  - Cons: Harder initial verification; adds configuration step.
- D: No change
  - Pros: No work now.
  - Cons: Slower troubleshooting; misses E1-S06 intent.

Decision Scope
- In: where/how to emit one-line activation signal.
- Out: Telemetry or detailed logging policies.

Decision
- Emit a single concise message to a named VS Code OutputChannel on activation in development. In production builds, guard or disable via a user/setting flag (e.g., `rmd.activateLog: true`).

Consequences
- Clear, low-noise verification in dev; production remains quiet unless explicitly enabled.

Open Questions
- Preference for dev-only vs. guarded prod message?

References
- Epic 1 E1-S06.

