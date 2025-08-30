# Story 03 - Cross-Platform Build/Load Verification (Must)

## Metadata
- ID: E1-S03
- Epic: 1 - PureScript Architecture and Build
- Priority: Must

## Objective
- Verify the build and extension load succeed on macOS, Windows, and Linux using documented commands.

## Business Rationale (fail-without-it)
- Lack of cross-OS reliability blocks adoption and testing across target environments.

## Acceptance Criteria
- Given the documented build command, when run on macOS, Windows, and Linux, then the build succeeds on each OS.
- Given the built extension, when installed on the latest VS Code on each OS, then it loads without runtime errors.

- Given cross-platform scripts, when executed via Node/npm scripts (not shell-specific constructs), then they succeed on all target OSes without requiring Git Bash or WSL on Windows.
- Given CI configuration (draft), when a matrix job is validated, then build and activation steps resolve without path or shell incompatibilities on macOS, Windows, and Linux.
- Given the minimal and latest supported VS Code versions, when activation smoke tests run via `@vscode/test-electron`, then activation succeeds on macOS, Windows, and Linux.
- Given engine pinning, when builds run, then the bundler Node `target` matches the extension host Node version without manifest warnings.
 - Given the activation smoke tests, when dependencies are inspected, then `@vscode/test-electron` is pinned to `2.5.2`.
 - Note: Reuse the engines/Node target pins from Story 01 for alignment.

## Notes
- Keep scripts and paths OS-agnostic where possible to avoid shell-specific issues.

## Deliverables
- OS-agnostic npm scripts for build and load verification
- Brief verification guide covering macOS, Windows, and Linux steps
- Draft CI matrix configuration outline (not full Epic 13 CI), including a Windows job to build/test/pack

## Depends On
- E1-S01 - Scaffold and Build
- E1-S02 - Bundling and Activation

## Blockers
- Availability of test environments or runners for each OS
- Confirmation of Node/VS Code engine ranges for each OS
