# Story 06 - Minimal Activation Log (Could)

## Metadata
- ID: E1-S06
- Epic: 1 - PureScript Architecture and Build
- Priority: Could

## Objective
- Log a minimal one-line message on activation to confirm the extension has loaded successfully.

## Rationale
- Aids verification and troubleshooting with minimal effort; safe to defer.

## Acceptance Criteria
- Given the extension activates on `.Rmd`, when activation completes, then a one-line message indicates activation success in the output or console.

- Given a development build, when the extension activates, then a single line is written to a named VS Code OutputChannel.
- Given a production build, when the extension activates, then no message is logged by default unless a user setting (e.g., `rmd.activateLog: true`) is enabled.

## Notes
- Keep logging minimal to avoid noise; consider dev-only vs. production behavior.

- Prefer a named VS Code Output channel for visibility; guard verbose logs behind a dev setting/flag.

## Deliverables
- Lightweight logging helper with environment/setting guard
- Named Output channel (or console) message on activation
- Brief verification steps documenting where to see the message

- User setting to enable/disable the activation message in production

## Depends On
- E1-S02 - Bundling and Activation

## Blockers
- None
