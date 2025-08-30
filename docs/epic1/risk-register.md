# Epic 1 — Risk Register (Phase 2)

Scope: Technical risks for Epic 1 (PureScript build and VS Code extension bundling/activation). Severity computed via matrix from Likelihood and Impact.

## Risk Register
| Title | Category | Description | Likelihood | Impact | Severity | Status |
|---|---|---|---|---|---|---|
| Misconfigured bundler externals | Integrations | If `vscode` is not marked external in esbuild, the extension host fails `require('vscode')` at runtime. Evidence: ADR “Bundler for Extension Bundle” specifies `external: ['vscode']`; missing this is a known failure mode. | Medium | High | Critical | Assessed |
| ESM/CJS FFI interop mismatch | Integrations | PureScript JS FFI default/named export mismatch or CJS/ESM interop causes runtime import errors after bundling. Evidence: Acceptance criteria call out “no runtime import/require errors”; FFI shims often require explicit interop config. | Medium | High | Critical | Assessed |
| File-pattern activation gaps | Integrations | `workspaceContains:**/*.{Rmd,rmd}` will not activate on single-file open outside a workspace; expected activation may not occur. Evidence: ADR “Activation Trigger for .Rmd” notes file-pattern limitations pre-language ID. | Medium | Medium | Medium | Assessed |
| Case sensitivity for .Rmd | Integrations | `.Rmd` vs `.rmd` behavior differs by OS; activation pattern may miss files without `{Rmd,rmd}` handling. Evidence: ADR notes case handling requirement. | Medium | Medium | Medium | Assessed |
| POSIX-only scripts on Windows | Legacy | Shell-specific scripts (e.g., `rm`, globbing, path separators) break on Windows, blocking build/load verification. Evidence: Story E1-S03 requires OS-agnostic npm scripts. | High | Medium | High | Assessed |
| Watch instability on large repos | Performance | OS watcher semantics and large workspaces can cause missed rebuilds or excessive churn during `--watch`. Evidence: Story E1-S05 mentions cross-platform file watching differences and debounce needs. | Medium | Medium | Medium | Assessed |
| Source maps expose sources | Security | Production bundles may include inline sources or high-fidelity maps, increasing code exposure risk when published. Evidence: Story E1-S04 calls for policy-aware source map configuration. | Medium | High | Critical | Assessed |
| Engine range/target mismatch | Legacy | `engines.vscode` or Node target misaligned with extension host causes warnings or runtime failures. Evidence: Story E1-S01 acceptance criteria require pinned supported ranges aligned to host Node. | Medium | High | Critical | Assessed |

Notes:
- Likelihood/Impact definitions and severity matrix per Risk Analyst persona; Critical when Impact = High and Likelihood = Medium/High.
- Status flow: New → Assessed → Mitigation Planned → In Progress → Resolved/Accepted.

## Phase 2 Gate Checklist
- Scope: Integrations, Performance, Security, Legacy covered.
- Register: Risks recorded with required fields (Title, Category, Description, Likelihood, Impact, Severity, Status).
- Scoring: Likelihood/Impact set; Severity computed via the matrix.
