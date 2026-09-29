# PR-05 output

Source: live `/review-pr 5` run in this session (three-subagent sequential delegation —
style-reviewer, security-reviewer, architecture-reviewer — followed by consolidation).

**Repository:** ispaniuktripleten/go-pr-review-practice
**PR:** [#5](https://github.com/ispaniuktripleten/go-pr-review-practice/pull/5) ("Add checkout service")
**Head SHA:** `51606e97b6f13ab3dbe3c660fe82a42454d748bf`
**Base:** main @ `ae2cfe8b499427b367a653bf6981dba6de364b70`

## internal/service/checkout_service.go

**[HIGH]** Line 9 — Hardcoded credential, embedded outside centralized configuration.
```go
const clientSecret = "tr***MASKED***28ac"
```
- **Security:** A credential-shaped secret is hardcoded as a source-level constant and returned verbatim (prefixed `"Bearer "`) by the exported `Authorization()` method (line 17). Any caller receives the raw bearer token as a plain string, which could be logged or forwarded; the secret is committed to version control (permanently present in git history even if later removed) and cannot be rotated without a code change/redeploy.
- **Architecture (Configuration guideline, ADR-002):** Runtime configuration and secrets must be loaded centrally (`internal/config` or the composition root) and passed through constructors — services must not embed credentials directly. This line also violates that guideline; not counted as a separate architecture finding.
- **Suggested change:** Load the secret at runtime (e.g. `os.Getenv("CHECKOUT_CLIENT_SECRET")` backed by a secrets manager) in `internal/config` or the composition root, and inject it into `CheckoutService` via `NewCheckoutService` rather than compiling it into the package. Never log it.

**[MEDIUM]** Lines 12, 21–27 — Repository-layer bypass.
```go
type CheckoutService struct{ client *api.Client }
...
func (s *CheckoutService) Submit_order(ctx context.Context) (int, error) {
	rides, err := s.client.RecentRides(ctx)
	...
```
`CheckoutService` holds a concrete `*api.Client` and calls `s.client.RecentRides(ctx)` directly instead of depending on a repository interface, bypassing the repository layer. Constructor injection of the concrete client satisfies dependency injection but not the repository-pattern requirement — supported by **ADR-003 (Repository Pattern)**: "Services must not import or call API clients or SQL directly, even when the client is injected... Report this boundary violation as MEDIUM."
- **Suggested change:** Define a small repository interface near the consumer (e.g. `type RideRepository interface { RecentRides(ctx context.Context) ([]Ride, error) }`), inject that interface into `NewCheckoutService`, and move the `*api.Client` usage into a concrete adapter under `internal/repository`.

**[LOW]** Lines 24–25 — Non-idiomatic naming.
```go
func (s *CheckoutService) Submit_order(ctx context.Context) (int, error) {
```
The exported method `Submit_order` uses snake_case with an underscore instead of MixedCaps.
- **Rule:** app-conventions Rule 1 (naming).
- **Suggested change:** Rename to `SubmitOrder` (update the doc comment and the test call site accordingly).

## internal/service/checkout_service_test.go

**[MEDIUM]** Lines 19–21 — Test depends on a real external endpoint.
```go
client := api.NewClient(&http.Client{Timeout: 2 * time.Second}, "https://api.example.com")
svc := NewCheckoutService(client)
count, err := svc.Submit_order(context.Background())
```
`TestSubmitOrderReturnsRides` constructs a real `api.Client` pointed at `https://api.example.com` and calls `Submit_order` against it. The `RUN_LIVE_TESTS` env-var gate (lines 16–18) only skips the test by default — it doesn't remove the dependency; when the flag is set, the test performs a live outbound call to a third-party endpoint instead of using a fake or `httptest.NewServer`, violating the test-isolation requirement.
- **Rule:** test-conventions rule 3 (unit tests must not depend on real external services).
- **Suggested change:** Replace the real `api.Client`/live endpoint with a fake implementation or a local `httptest.NewServer` returning a canned response, so the test is self-contained regardless of environment variables.

## Summary

**4 findings total — 1 HIGH, 2 MEDIUM, 1 LOW.**

The PR adds a `CheckoutService` that: (1) hardcodes a client secret returned by `Authorization()` (HIGH — combined security + Configuration/ADR-002 finding); (2) depends directly on the concrete `*api.Client` instead of a repository interface (MEDIUM — ADR-003); (3) names its exported method `Submit_order` with snake_case instead of MixedCaps (LOW); and (4) has a test that depends on a real external endpoint, gated only by an opt-in env var rather than isolated with a fake or `httptest.NewServer` (MEDIUM — test-conventions rule 3).
