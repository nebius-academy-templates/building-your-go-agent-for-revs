# Trace the coding agent
Trace the reviewer running in your coding tool, not the Go code being reviewed.
For Claude Code follow [LangSmith's integration guide](https://docs.langchain.com/langsmith/trace-claude-code).
Install inside Claude Code:

```text
/plugin marketplace add langchain-ai/langsmith-claude-code-plugins
/plugin install langsmith-tracing@langsmith-claude-code-plugins
/reload-plugins
```
Configure your local environment with TRACE_TO_LANGSMITH=true, CC_LANGSMITH_API_KEY,
and CC_LANGSMITH_PROJECT=go-pr-review-agent. Set CC_LANGSMITH_METADATA to a JSON object
such as {"pr_number":"1"}. Match endpoint/region using the installed integration's documented
settings; do not assume generic LANGSMITH_* settings control every Claude Code plugin.
The current guide documents explicit replica destinations when region routing is needed.
Keep secrets in your environment or ignored .claude/settings.local.json.

Restart or reload as required; changing a parent shell after the tool starts will not change
that process's environment. Verify one completed trace before the remaining runs. Record
plugin version, trace IDs, token availability, and the PR/case mapping. Use genuine child spans
and outputs. Do not substitute example totals. Other coding tools need their own integration.
