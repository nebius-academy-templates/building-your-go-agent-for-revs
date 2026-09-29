# Building Your Go Agent
Build a PR Review Agent for Go through seven cumulative practices. The agent runs in your
coding tool; this repository is its configuration, not a Go implementation of an LLM agent.
Follow practice-description.md. Complete placeholders only for the current stage.

## Practices
1. System prompt and format, publish-gate, scope-refusal evidence.
2. GitHub MCP connection and live PR review.
3. Selective convention skills and /review-pr workflow.
4. Three sequential reviewer subagents.
5. Episodic history and ADR-backed semantic memory.
6. LangSmith tracing and measured observability.
7. Five-input evaluation, injection testing, and an evidence-backed improvement.

All evidence lives under results/. Never submit illustrative reviewer examples as real runs.
The .claude/ layout targets Claude Code; equivalent tool-native layouts are acceptable when
supported by the course's validator. Do not assume another tool understands these files.

## Inputs
samples/ contains all five standalone diffs. PR-01 is clean; PR-02 has style issues;
PR-03 hardcodes a synthetic credential; PR-04 bypasses a repository; PR-05 mixes categories.
Read fixture-policy.md. Supporting Go source is in the separately supplied fixture repository.

## Target setup
review-target.json starts in local mode. The instructor provides the real GitHub owner and
actual PR-number mapping. Set mode to github for Practice 2 and live workflow runs.
No hosted Go practice repository is assumed to exist. Case numbers 1–5 are stable exercise
IDs; GitHub PR numbers may differ. For example, /review-pr 4 resolves the mapping for case 4.
No credential belongs in review-target.json. See setup/github-mcp.md and setup/tracing.md.
