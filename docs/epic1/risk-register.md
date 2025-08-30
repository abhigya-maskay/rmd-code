# Epic 1 - Risk Register (Phase 2)

Scope: Technical risks for Epic 1 (PureScript build and VS Code extension bundling/activation). Severity computed via matrix from Likelihood and Impact.

## Risk Register
| Title | Category | Description | Likelihood | Impact | Severity | Status |
|---|---|---|---|---|---|---|
| Misconfigured bundler externals | Integrations | If `vscode` is not marked external in esbuild, the extension host fails `require('vscode')` at runtime. Evidence: ADR "Bundler for Extension Bundle" specifies `external: ['vscode']`; missing this is a known failure mode. | Medium | High | Critical | Mitigation Planned |
| ESM/CJS FFI interop mismatch | Integrations | PureScript JS FFI default/named export mismatch or CJS/ESM interop causes runtime import errors after bundling. Evidence: Acceptance criteria call out "no runtime import/require errors"; FFI shims often require explicit interop config. | Medium | High | Critical | Mitigation Planned |
| File-pattern activation gaps | Integrations | `workspaceContains:**/*.{Rmd,rmd}` will not activate on single-file open outside a workspace; expected activation may not occur. Evidence: ADR "Activation Trigger for .Rmd" notes file-pattern limitations pre-language ID. | Medium | Medium | Medium | Mitigation Planned |
| Case sensitivity for .Rmd | Integrations | `.Rmd` vs `.rmd` behavior differs by OS; activation pattern may miss files without `{Rmd,rmd}` handling. Evidence: ADR notes case handling requirement. | Medium | Medium | Medium | Mitigation Planned |
| POSIX-only scripts on Windows | Legacy | Shell-specific scripts (e.g., `rm`, globbing, path separators) break on Windows, blocking build/load verification. Evidence: Story E1-S03 requires OS-agnostic npm scripts. | High | Medium | High | Mitigation Planned |
| Source maps expose sources | Security | Production bundles may include inline sources or high-fidelity maps, increasing code exposure risk when published. Evidence: Story E1-S04 calls for policy-aware source map configuration. | Medium | High | Critical | Mitigation Planned |
| Engine range/target mismatch | Legacy | `engines.vscode` or Node target misaligned with extension host causes warnings or runtime failures. Evidence: Story E1-S01 acceptance criteria require pinned supported ranges aligned to host Node. | Medium | High | Critical | Mitigation Planned |

Notes:
- Likelihood/Impact definitions and severity matrix per Risk Analyst persona; Critical when Impact = High and Likelihood = Medium/High.
- Status flow: New -> Assessed -> Mitigation Planned -> In Progress -> Resolved/Accepted.

## Phase 2 Gate Checklist
- Scope: Integrations, Performance, Security, Legacy covered.
- Register: Risks recorded with required fields (Title, Category, Description, Likelihood, Impact, Severity, Status).
- Scoring: Likelihood/Impact set; Severity computed via the matrix.

## Mitigations - Integrations

- Misconfigured bundler externals:
  - Pin esbuild config `external: ['vscode']` in a shared build file.
  - CI guard: analyze bundle to ensure `vscode` is excluded (e.g., dependency graph check or `esbuild --metafile` review).
  - Smoke test in extension host to verify `require('vscode')` resolves at runtime.

- ESM/CJS FFI interop mismatch:
  - Standardize module format for the extension host (prefer CJS): set esbuild `platform: 'node'`, `format: 'cjs'`; ensure `package.json` `type` aligns.
  - Add FFI shims for default/named export mapping; enable `esModuleInterop` if using TypeScript.
  - Add import contract tests in CI (Node runtime + bundled artifact) to catch regressions.

- File-pattern activation gaps:
  - Provide `onCommand` activation fallback (e.g., "R Markdown: Activate Extension") for single-file scenarios.
  - Temporary `onStartupFinished` activation with cheap, guarded init; migrate to `onLanguage:rmd` when a language ID is available.
  - Document manual activation in README and ensure idle/no-op behavior until an `.Rmd` file is in focus.

- Case sensitivity for .Rmd:
  - Use case-insensitive matching in globs `**/*.{Rmd,rmd}` and code-level checks `path.toLowerCase().endsWith('.rmd')`.
  - Add cross-OS activation tests (Windows/macOS/Linux) to validate patterns.
  - Normalize extension handling in all file checks and activation paths.

## Mitigations - Security

- Source maps expose sources:
  - Production builds: disable source maps or emit external maps only for debugging (`sourcemap: false` or `sourcemap: external` in prod builds).
  - Packaging filters: exclude maps and sources from published artifacts. For VS Code, add `.vscodeignore` rules (e.g., `**/*.map`, `src/**`, test/dev files). For npm, restrict `files` to the compiled output and exclude `*.map`.
  - CI guard: fail if any `.map` files or `sourceMappingURL` references appear in the `.vsix` or npm tarball. Validate using `vsce package` and `npm pack --dry-run` plus a contents check.
  - Controlled retention: store maps as internal CI artifacts or in error monitoring; do not publish publicly.
  - Policy toggle: document per-environment policy; require explicit approval to enable maps outside dev.
  - Automation: enforce production mode during bundling and gate on a "no sourcemaps in prod" check.

## Mitigations - Legacy

- POSIX-only scripts on Windows:
  - Replace POSIX commands in npm scripts with cross-platform tools (`rimraf`/`del-cli` for delete, `shx`/`cpy-cli` for copy, `cross-env` for env vars).
  - Move complex shell logic into Node scripts (`node scripts/<task>.js`) to avoid shell/glob quirks and path separator issues.
  - CI: add a Windows job to run build/test/pack to catch script incompatibilities early.
  - Characterization: add a smoke script that runs key npm tasks across OS to assert portability.

- Engine range/target mismatch:
  - Pin `engines.vscode` to the supported VS Code range and document the mapped extension host Node major.
  - Align build targets to the host Node (esbuild `platform: 'node'`, `target: ['node<host-major>']`; TS `target` accordingly).
  - CI: run activation smoke tests against the minimal and latest supported VS Code versions (e.g., via `@vscode/test-electron`).
  - Activation guard: check `process.versions.node` at startup and surface a clear error when unsupported.
