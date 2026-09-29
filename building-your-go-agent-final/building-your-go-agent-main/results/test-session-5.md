# Practice 5 — Test Session 5

## 1. User question

```
Based on review_history.md, what finding type has highest severity?
```

## 2. Actual answer given

Based on `review_history.md` at the time of the question (entries for PR-01 through PR-03 only — the PR-04 entry below was appended later, during this same session, as part of the review recorded in section 7):

> The highest-severity finding in review_history.md is **HIGH**: PR-03, "Hardcoded payment API key constant" (2026-09-21).

## 3. User command

```
/review-pr 4
```

## 4. GitHub MCP retrieval evidence for PR #4

Resolved from `review-target.json`: mode `github`, owner `ispaniuktripleten`, repo `go-pr-review-practice`, case `4` → PR number `4`.

- `mcp__github__pull_request_read` (method `get`):
  - Repository: `ispaniuktripleten/go-pr-review-practice`
  - PR number: `4`
  - Title: "Add trip loading service"
  - Head SHA: `0f65e1730ca6b4cd39e0c2d26c0dcc21a533b933`
  - Base: `main` @ `ae2cfe8b499427b367a653bf6981dba6de364b70`
  - State: open, draft: false, changed_files: 2, additions: 57

- `mcp__github__pull_request_read` (method `get_diff`): returned the full unified diff for both changed files (below).

- `mcp__github__pull_request_read` (method `get_files`): confirmed changed-file list —
  - `internal/service/trip_service.go` (added, 24 lines)
  - `internal/service/trip_service_test.go` (added, 33 lines)

## 5. Sequential subagent invocation and actual results

Invoked in the required order, each waited on before invoking the next, all three given the identical full in-scope diff (both files) and file list.

### a. style-reviewer

Result: `No style findings.`

Reviewer's stated reasoning: `internal/service/trip_service.go` (app-conventions) — naming uses proper MixedCaps/mixedCaps with correct `ID` initialism preserved via `model.Ride`; all three exported declarations (`TripService`, `NewTripService`, `LoadTrips`) have doc comments starting with their names; the error from `s.client.RecentRides` is checked and wrapped with `fmt.Errorf("load trips: %w", err)`; `context.Context` is the first parameter and is propagated. `internal/service/trip_service_test.go` (test-conventions) — descriptive test name, local `httptest.NewServer` (exempt from external-dependency rule) with `t.Cleanup(server.Close)`, write error checked via `t.Errorf`, `LoadTrips` error checked via `t.Fatalf` before use, results asserted via `len(rides)` and `rides[0].ID`.

### b. security-reviewer

Result: `No security findings.`

### c. architecture-reviewer

Result: one `[MEDIUM]` finding — see full text in section 7.

## 6. Architecture-reviewer semantic-memory evidence (actual tool calls, as reported by the subagent)

This is the literal, ordered tool-call record the architecture-reviewer subagent reported when asked directly to state its exact calls (not reconstructed from its summary):

1. **Grep**
   - `pattern: "repository|Repository|API client|api\.Client"`
   - `path: "D:\Tripleten\dev\building-your-go-agent\docs\adr"`
   - `output_mode: "files_with_matches"`
   - Result: one matching file — `docs\adr\ADR-003-repository-pattern.md`

2. **Read**
   - `file_path: "D:\Tripleten\dev\building-your-go-agent\docs\adr\ADR-003-repository-pattern.md"`
   - Result: full contents of ADR-003, independently confirmed to read (14 lines):

     > **ADR-003: Repository Pattern** — Status: Accepted.
     > Decision: "Services obtain data through small repository interfaces, normally declared near the consumer. Concrete adapters in internal/repository may depend on internal/api or database/sql. Services must not import or call API clients or SQL directly, even when the client is injected. Injecting a client satisfies dependency injection but still bypasses the repository. Report this boundary violation as MEDIUM. Tests may wire adapters to httptest servers."

The subagent reported no other Grep or Read calls in this task (it did not re-read the diff files via the Read tool, since the diff was supplied inline in its prompt).

**Supported ADR-003 citation** (from the architecture-reviewer's finding, section 7 below): the `[MEDIUM]` finding on `trip_service.go` cites "Violated guideline: Repository pattern ... supported by **ADR-003 (Repository Pattern)**, which explicitly states injecting a client still bypasses the repository and should be reported as MEDIUM" — matching the ADR text retrieved above.

## 7. Complete consolidated PR #4 review

## PR Review — ispaniuktripleten/go-pr-review-practice #4 "Add trip loading service"
**Head SHA:** `0f65e1730ca6b4cd39e0c2d26c0dcc21a533b933`

### internal/service/trip_service.go

[MEDIUM] `internal/service/trip_service.go:11-19` — `TripService` holds a `*api.Client` field directly, and `LoadTrips` calls `s.client.RecentRides(ctx)` against that concrete client. This bypasses the repository pattern — services must obtain data through a repository interface, not by calling the API client directly. Constructor injection of the concrete client satisfies dependency injection but not the repository-pattern requirement (architecture-guidelines skill, supported by ADR-003).
Suggested change: Define a small interface near the consumer, e.g. `type TripRepository interface { RecentRides(ctx context.Context) ([]model.Ride, error) }` in `internal/service`, and change `NewTripService` to accept that interface. Move the concrete `*api.Client` wiring into an `internal/repository` adapter that implements the interface, and inject the adapter from the composition root instead of the raw API client.

### internal/service/trip_service_test.go

No findings.

## Summary

**Total findings: 1** — `[HIGH]`: 0, `[MEDIUM]`: 1, `[LOW]`: 0.

The PR is otherwise clean: naming, doc comments, and error handling all follow convention (style-reviewer: no findings), and no hardcoded credentials, injection risks, unsafe TLS, or missing validation were found (security-reviewer: no findings). The one issue is an architecture-boundary violation — the new service calls the concrete API client directly instead of going through a repository interface.

## 8. Publication question, user response, and confirmation

**Assistant asked:**
```
Post this as a comment? Reply yes or post.
```

**User responded:**
```
no
```

**Assistant confirmation:** No GitHub write was performed. No comment was posted to PR #4. Per the guardrail requiring exactly one `review_history.md` append per completed review regardless of the publication decision, the following line was appended once:

```
2026-09-21 | PR-04 | Service bypasses repository pattern, calls API client directly | MEDIUM
```

No credential values appear anywhere in this PR's review content, so no masking was required for this session's findings.
