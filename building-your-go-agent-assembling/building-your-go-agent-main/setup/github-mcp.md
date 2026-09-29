# GitHub MCP setup
Use your host's supported GitHub MCP configuration. Follow the
[official server setup](https://github.com/github/github-mcp-server).
For Claude Code with Docker, set GITHUB_PERSONAL_ACCESS_TOKEN in your local environment
and keep .mcp.json ignored. An example (no literal token):

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"]
    }
  }
}
```
Pin the server image/version used by your course when reproducing an assessment.
Grant the practice repository Contents: read and Pull requests: read for fetching.
Read-only credentials are sufficient for the required decline-to-post exercise.
Posting later requires matching write permissions for the chosen operation, plus user
confirmation. Do not confuse tool availability with permission to execute it.

Verify pull_request_read supports get_diff; use get_files for file coverage when needed.
The workflow uses add_issue_comment for one ordinary PR comment, not a pending review.
Keep actual credentials and local MCP configuration out of Git. Supply only redacted
connection evidence in results/test-session-2.md. Set review-target.json to the instructor's
actual owner, repository, and case-to-PR mapping before the live run.
