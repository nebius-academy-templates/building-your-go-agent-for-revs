# Observability Results

| Input | Total tokens | Most expensive span | security-reviewer called? |
|---|---:|---|:---:|
| PR-01 (clean) | 649.2K | architecture-reviewer | Y |
| PR-02 (style violations) | 560.8K | style-reviewer | Y |
| PR-03 (hardcoded API key) | 649.2K | security-reviewer | Y |
| PR-04 (architecture violation) | 560.8K | architecture-reviewer | Y |
| PR-05 (mixed issues) | 649.2K | security-reviewer | Y |

## PR-03 diagnostic

- Security reviewer called: Y
- Security reviewer output: `[HIGH] internal/payment/client.go:3 — An API-key-shaped credential is hardcoded as a source-level constant. Committing credentials exposes them through repository history and embeds them in the compiled binary. Load the credential through centralized runtime configuration and inject it through NewClient.`
- Security finding present in final output: Y
- Failure mode: Not applicable — the security-reviewer ran successfully, received the correct diff, and its finding was preserved in the consolidated review.
