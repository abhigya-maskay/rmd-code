# Story 02 - Bundling and `.Rmd` Activation (Must)

## Metadata
- ID: E1-S02
- Epic: 1 - PureScript Architecture and Build
- Priority: Must

## Objective
- Configure bundling and activation by `.Rmd` so the extension loads only when relevant and runs without runtime import/require errors.

## Business Rationale (fail-without-it)
- Without bundling and correct activation events, the extension either does not load or loads unnecessarily, harming UX and performance.

## Acceptance Criteria
- Given the extension is installed on the latest VS Code, when a workspace opens a `.Rmd` file, then the extension activates.
- Given no `.Rmd` files are opened, when VS Code starts, then the extension does not activate.
- Given the bundle is produced, when the extension activates, then no runtime import/require errors occur.

- Given the `package.json` manifest, when validated, then `activationEvents` include the chosen trigger (file pattern for `.Rmd`), and validation reports no warnings.
- Given case-sensitivity considerations, when opening `.Rmd` and `.rmd` (if supported), then activation behavior matches documented expectations across OSes.

## Notes
- Activation may be triggered by language or file pattern; confirm case-sensitivity expectations across OSes.

- If the language ID is delivered in a later epic, prefer file-pattern activation here and plan to switch to language activation in Epic 3.

## Deliverables
- Bundler configuration and entrypoint that loads without runtime import/require errors
- `package.json` with `activationEvents` for `.Rmd` file-pattern activation
- Minimal verification steps documented (open `.Rmd`, confirm activation)

## Depends On
- E1-S01 - Scaffold and Build

## Blockers
- Final decision on activation trigger (file pattern now vs. language ID in Epic 3)
- Clarify case-sensitivity policy for `.Rmd` vs `.rmd`
