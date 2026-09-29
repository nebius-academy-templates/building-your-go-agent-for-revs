---
name: test-conventions
description: "Go test conventions. Load for changed files whose basename ends in _test.go, including colocated tests under internal/, within the requested review scope."
---

## Rules

1. [LOW] Tests must use `func TestXxx(t *testing.T)` with a descriptive name that explains the expected behavior. Use `t.Run` for multiple related cases, but do not require table-driven tests for one simple case.

2. [MEDIUM] Assert observable results and check errors before using returned values. Use `t.Fatal` or `t.Fatalf` when a setup operation fails. Do not silently discard errors.

3. [MEDIUM] Unit tests must not depend on real external services, shared databases, execution order, or side effects from other tests. Use fakes, `httptest.NewServer`, or `t.TempDir` where appropriate.

4. [LOW] Reuse repeated setup with helpers marked using `t.Helper`. Release resources using `t.Cleanup` or `defer`.

5. [MEDIUM] Do not combine `t.Parallel` with conflicting shared mutable state or process-global changes such as `t.Setenv`. Report only a concrete conflict, not the mere use of parallel tests.

6. Do not require exported API comments on test functions or unexported test fakes. A local `httptest.NewServer` is an isolated test dependency, not a real external service.

## Template

Compliant:

```go
func TestParseCountReturnsNumber(t *testing.T) {
    got, err := ParseCount("42")
    if err != nil {
        t.Fatalf("ParseCount() error = %v", err)
    }

    if got != 42 {
        t.Fatalf("ParseCount() = %d, want 42", got)
    }
}
```

Non-compliant:

```go
func Test1(t *testing.T) {
    got, _ := ParseCount("42")
    t.Log(got)
}
```

The non-compliant example has an unclear name, discards an error, and does not assert the observable result.

Unclear test names and setup conventions are normally [LOW]. Ignored errors, missing assertions, isolation failures, and concrete shared-state conflicts are normally [MEDIUM].