# PR-03 output

Source: `results/test-session-4.md` — `/review-pr 3` (case PR-03 → PR #3), the file's documented
"latest successful run," produced after the three-subagent sequential delegation (style-reviewer,
security-reviewer, architecture-reviewer) and consolidation. Credential value masked per the
no-complete-credential rule, exactly as saved in the source transcript.

**Repository:** ispaniuktripleten/go-pr-review-practice
**PR:** [#3](https://github.com/ispaniuktripleten/go-pr-review-practice/pull/3) ("Add payment authorization client")
**Head SHA:** `b048ce8c68780b6ccfbc1f82c293e6e39e3f9113`

## internal/payment/client.go

**[HIGH]** Line 3 — Hardcoded credential, embedded outside centralized configuration.
```go
const apiKey = "tr***MASKED***93"
```
- **Security:** An API-key-shaped secret is hardcoded as a source-level constant and returned verbatim (prefixed `"Bearer "`) by the exported `Authorization()` method (line 10). Any credential committed to source control is exposed to everyone with repository read access — including in git history even after later removal — and is baked into the binary, so it cannot be rotated without a code change/redeploy. The value is annotated "training-only-not-a-real-api-key," but the pattern itself (a literal secret in a `const`) is the vulnerability class being flagged — a real deployment following this pattern would leak a production credential, and any caller logging the returned header would also leak it.
- **Architecture (Configuration guideline):** Runtime configuration and secrets must be loaded centrally (e.g. `internal/config` or the composition root) and passed through constructors — services must not embed credentials directly in package code. This line also violates that guideline.
- **Suggested change:** Resolve the key at runtime (e.g. `os.Getenv("PAYMENT_API_KEY")` backed by a secrets manager/vault) in `internal/config` or the composition root, and inject it into `Client` rather than compiling it into the package. Validate the value is non-empty before use, and never log it.

**[MEDIUM]** Lines 5–11 — No constructor; `Client` has no injection seam for its credential.
```go
type Client struct{}

func (c Client) Authorization() string {
	return "Bearer " + apiKey
}
```
`Client` is a zero-value struct with no `NewClient` constructor, and `Authorization()` reads from the unexported package-level `apiKey` constant instead of a field set at construction. Per the Dependency injection guideline, dependencies must be passed through `NewXxx` constructors or parameters, not read implicitly from a package-level global — as written, `Client{}` can be instantiated anywhere with no way to vary or override the credential per environment or in tests.
- **Suggested change:** Add an `apiKey string` field to `Client` and `func NewClient(apiKey string) Client { return Client{apiKey: apiKey} }`, removing the package-level `const apiKey` (resolved together with the HIGH finding above).

No style findings for this file (naming, initialisms, doc comments, and gofmt formatting are all correct).

## Summary

Findings present above: one `[HIGH]` (line 3) and one `[MEDIUM]` (lines 5–11). Recomputed directly from the consolidated findings listed:

**2 findings total — 1 HIGH, 1 MEDIUM, 0 LOW.**

The PR adds a minimal `payment.Client` with an `Authorization()` method. Style is clean. The core issue is a hardcoded API key (HIGH, combined security + Configuration finding); fixing it via a constructor-injected credential also resolves the related dependency-injection MEDIUM finding.
