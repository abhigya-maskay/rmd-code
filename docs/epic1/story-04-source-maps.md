# Story 04 - Source Maps for Debugging (Should)

## Metadata
- ID: E1-S04
- Epic: 1 - PureScript Architecture and Build
- Priority: Should

## Objective
- Provide source maps for PureScript output to enable debugging in source rather than generated code.

## Rationale
- Faster debugging reduces time-to-fix and improves developer productivity without changing runtime behavior.

## Acceptance Criteria
- Given the extension runs in VS Code, when a breakpoint is set in PureScript source, then it maps to the correct runtime location.
- Given an exception is thrown, when stack traces are inspected, then they include original source locations when feasible.

- Given production builds, when source maps are configured, then they exclude inline sources if policy requires, while retaining mapping fidelity for debugging sessions.

## Notes
- Ensure mappings are included in production/development bundles as appropriate.

- Verify source map paths resolve correctly in the VS Code extension host environment.

## Deliverables
- Source map generation integrated into build/bundle
- Minimal debugging guide for setting breakpoints in PureScript
- Config toggle for dev vs. prod source map behavior

## Depends On
- E1-S01 - Scaffold and Build
- E1-S02 - Bundling and Activation

## Blockers
- Choice of bundler and its source map capabilities/policies
