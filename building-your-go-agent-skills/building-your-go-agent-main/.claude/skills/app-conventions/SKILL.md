---
name: app-conventions
description: "Go application/library conventions. Load for changed .go files whose basename does not end in _test.go, within the requested review scope."
---

## Rules

1. [LOW] Use MixedCaps for exported names and mixedCaps for unexported names. Preserve common initialisms such as ID, URL, HTTP, and API. Do not use snake_case identifiers.

2. [LOW] Exported declarations must have a short Go doc comment beginning with the declaration's name. Format Go source code with gofmt.

3. [MEDIUM] Handle or propagate meaningful errors. Do not silently discard errors with `_`. When adding context, preserve the original error using `fmt.Errorf("operation: %w", err)`.

4. [MEDIUM] For request-scoped I/O, pass `context.Context` as the first parameter and propagate it downstream. Do not require context for pure computations.

5. Do not apply Python-specific rules such as type hints, `snake_case`, `bare except`, or mutable default arguments.

## Template

Compliant:

```go
// LoadRide returns a ride by ID.
func LoadRide(ctx context.Context, rideID string) (Ride, error) {
    ride, err := repository.FindRide(ctx, rideID)
    if err != nil {
        return Ride{}, fmt.Errorf("find ride: %w", err)
    }

    return ride, nil
}
```

Non-compliant:

```go
func Load_ride(Ride_id string) Ride {
    ride, _ := repository.FindRide(context.Background(), Ride_id)
    return ride
}
```

The non-compliant example violates Go naming conventions, lacks a Go doc comment, discards a meaningful error, and does not propagate the caller's context.

Naming and documentation issues are normally [LOW]. Meaningful error-handling and context-propagation failures are normally [MEDIUM].