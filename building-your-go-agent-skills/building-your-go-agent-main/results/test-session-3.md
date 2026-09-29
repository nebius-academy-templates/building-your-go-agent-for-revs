# Test Session 3 — Skills

Repository under review: `nebius-academy-templates/go-pr-review-practice`

> Captured from Claude Code Desktop. Runs 1 and 2 preserve the visible transcript exactly:
> PR #2 explicitly reports that `app-conventions` was applied, while the PR #4 interface
> shows the collapsed `Used 3 tools` event and the test-only scope. Run 3 contains the
> expanded tool and skill events.

## Run 1 — PR #2 application-file selective loading

### User

```text
Review the application files in PR #2 from https://github.com/nebius-academy-templates/go-pr-review-practice for style issues.

Use GitHub MCP tools only. Do not use Bash, PowerShell, gh, curl, or direct web requests.
```

### Visible routing evidence

```text
Files in scope: internal/notifications/manager.go (new file, not a _test.go file → app-conventions applied)
```

The visible transcript contains no `test-conventions` load or application event in this run.

### Assistant

```text
Review: nebius-academy-templates/go-pr-review-practice PR #2 — "Add notification scheduling manager"
Repository: nebius-academy-templates/go-pr-review-practice
PR: #2
Head SHA: 3c00000305f36d1dcb220f1514f2932fa8c93bdf
Files in scope: internal/notifications/manager.go (new file, not a _test.go file → app-conventions applied)

internal/notifications/manager.go

[LOW] Line 3: type notification_manager struct uses snake_case. Unexported Go identifiers should use mixedCaps.
→ type notificationManager struct

[LOW] Line 4: field pending_notifications uses snake_case.
→ pendingNotifications []string

[LOW] Line 7: func (m *notification_manager) Schedule_notification(Title string) — this method is exported (capitalized) but named with snake_case, and has no Go doc comment. Also, the parameter Title uses MixedCaps though it's a local parameter, which should be mixedCaps.

[LOW] Line 11: func (m *notification_manager) Get_pending() []string — exported method named with snake_case and missing a Go doc comment.

No error-handling, context-propagation, hardcoded-credential, injection, or unsafe-TLS issues apply — the file has no I/O, no errors returned, and no external input.

Summary

The PR adds a small, self-contained notifications package (1 file, 13 lines) with a manager type, a scheduling method, and a getter that correctly returns a defensive copy of the internal slice. The only issues found are naming-convention violations (snake_case used throughout instead of Go's MixedCaps/mixedCaps) and missing Go doc comments on the two exported methods.

Findings by severity: HIGH: 0, MEDIUM: 0, LOW: 5

No structural, correctness, or security issues were found.

Post this as a comment? Reply yes or post.
```

### User

```text
no
```

### Assistant

```text
Understood — no comment will be posted to PR #2. Let me know if you'd like anything else reviewed or adjusted.
```

## Run 2 — PR #4 test-file selective loading

### User

```text
Review only internal/service/trip_service_test.go in PR #4 from https://github.com/ispaniuktripleten/go-pr-review-practice for style issues.

Use GitHub MCP tools only. Do not use Bash, PowerShell, gh, curl, or direct web requests. Review only the requested test file and do not review production files from this PR.
```

### Visible routing evidence

```text
Used 3 tools

Files in scope (per your request): internal/service/trip_service_test.go only

(Note: the PR also adds internal/service/trip_service.go, but that file is out of the requested scope and was not reviewed.)
```

The visible transcript contains no `app-conventions` load or application event in this run.

### Assistant

```text
Review: nebius-academy-templates/go-pr-review-practice PR #4 — "Add trip loading service"
Repository: nebius-academy-templates/go-pr-review-practice
PR: #4
Head SHA: 0f65e1730ca6b4cd39e0c2d26c0dcc21a533b933
Files in scope (per your request): internal/service/trip_service_test.go only

(Note: the PR also adds internal/service/trip_service.go, but that file is out of the requested scope and was not reviewed.)

internal/service/trip_service_test.go
No findings.

The test (TestLoadTripsReturnsRecentTrips) uses a descriptive TestXxx name, isolates itself with a local httptest.NewServer (not a real external dependency), releases the server via t.Cleanup, checks the setup error from LoadTrips with t.Fatalf before using the result, and safely short-circuits the len(rides) != 1 || rides[0].ID != ... check to avoid an out-of-range access. No shared/global state, t.Parallel, or ordering dependencies are present.

Summary

Reviewing only trip_service_test.go as scoped: the test follows the required conventions for naming, error handling, isolation, and cleanup. No style, correctness, or security issues were found.

Findings by severity: HIGH: 0, MEDIUM: 0, LOW: 0

Post this as a comment? Reply yes or post.
```

### User

```text
no
```

## Run 3 — Explicit `/review-pr 1` workflow

### User

```text
/review-pr 1
```

### Tool and skill events

```text
Read review-target.json
D:\Tripleten\dev\building-your-go-agent\review-target.json

Mode is github, case 1 maps to PR #1 in ispaniuktripleten/go-pr-review-practice.

Loaded tools
query: select:mcp__github__pull_request_read

Used github: Get details for a single pull request
method: get
owner: nebius-academy-templates
repo: go-pr-review-practice
pullNumber: 1
head.ref: pr-01
head.sha: 2f473ae1cf98f10b5cade35510a6b72e2e32671a
base.ref: main
changed_files: 2

Used github: Get details for a single pull request
method: get_diff
owner: nebius-academy-templates
repo: go-pr-review-practice
pullNumber: 1

Diff coverage:
- internal/service/ride_history_service.go
- internal/service/ride_history_service_test.go

Used github: Get details for a single pull request
method: get_files
owner: ispaniuktripleten
repo: go-pr-review-practice
pullNumber: 1

Ran skill /app-conventions
skill: app-conventions
Launching skill: app-conventions

Ran skill /test-conventions
skill: test-conventions
Launching skill: test-conventions

Ran skill /architecture-guidelines
skill: architecture-guidelines
Launching skill: architecture-guidelines

Searched docs/adr/*
docs/adr/ADR-001-layered-architecture.md
docs/adr/ADR-002-configuration.md
docs/adr/ADR-003-repository-pattern.md
docs/adr/ADR-004-dependency-injection.md

Read ADR-003-repository-pattern.md
Read ADR-004-dependency-injection.md
Read ADR-001-layered-architecture.md
Read ADR-002-configuration.md
```

### Assistant

```text
PR Review — nebius-academy-templates/go-pr-review-practice PR #1 ("Add ride history service")
Head SHA: 2f473ae1cf98f10b5cade35510a6b72e2e32671a
Base: main @ ae2cfe8b499427b367a653bf6981dba6de364b70
Files changed: 2 (both new files, 68 additions total — full diff coverage confirmed against PR file stats)

internal/service/ride_history_service.go
No findings.

Checked against app-conventions and the relevant ADRs:

Naming/casing: RideRepository, RideHistoryService, NewRideHistoryService, LoadRides all use correct MixedCaps; no initialism-casing issues present in this diff.
All exported declarations (RideRepository, RideHistoryService, NewRideHistoryService, LoadRides) have Go doc comments starting with the declared name.
context.Context is the first parameter and is propagated through to repository.RecentRides(ctx).
The repository error is not discarded — it's wrapped with fmt.Errorf("load rides: %w", err), preserving the original error.
[ADR-003, repository pattern] The service depends only on the small RideRepository interface declared alongside it; it does not import internal/api or database/sql directly. Compliant.
[ADR-004, dependency injection] The repository is supplied via the NewRideHistoryService constructor; no global/mutable service instances. Compliant.
No hardcoded credentials, SQL/shell injection, TLS configuration, or externally-controlled input requiring additional validation appear in this diff.

internal/service/ride_history_service_test.go
No findings.

Checked against test-conventions:

Test names (TestLoadRidesReturnsRepositoryRides, TestLoadRidesPreservesRepositoryError) are descriptive of the behavior under test.
Errors are checked and asserted with t.Fatalf before use; the success path asserts the observable result via reflect.DeepEqual, and the error path asserts via errors.Is against the wrapped error.
The test depends only on an in-package fake (fakeRideRepository) — no real external services, shared state, or execution-order dependency.
No t.Parallel usage, so no shared-mutable-state conflict applies.
Test fake fakeRideRepository correctly has no doc comment requirement (per test-conventions rule 6).

Summary

This PR adds a RideHistoryService with constructor-injected RideRepository dependency and unit tests for both the success and error paths. It follows the team's naming, documentation, error-wrapping, and context-propagation conventions, and is compliant with the layered-architecture, repository-pattern, and dependency-injection ADRs. No security issues (credentials, injection, TLS) or concrete unvalidated-input paths were found in the changed lines.

Findings: 0 HIGH, 0 MEDIUM, 0 LOW.

Post this as a comment? Reply yes or post.
```

### User

```text
no
```

### Assistant

```text
Understood — no comment will be posted. Let me know if you'd like me to review another case (2–5) or take any other action on this PR.
```

No GitHub write tool was called after the declined publish gate.
