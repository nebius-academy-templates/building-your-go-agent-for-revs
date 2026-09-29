---
name: review-pr
description: "Run a complete Go PR review explicitly with /review-pr <case-number>."
disable-model-invocation: true
---

## Steps

1. Parse `$ARGUMENTS` as a case number from 1 to 5 and read `review-target.json`.

2. If the configured mode is `github`, resolve the case number to the actual pull request number and repository. Use the GitHub MCP `pull_request_read` tool with method `get_diff` to retrieve the complete diff. Use additional read methods and pagination when necessary to verify the repository, pull request number, head SHA, and complete changed-file coverage.

3. Do not use Bash, `gh`, `curl`, or a local sample as a silent fallback when the GitHub MCP request fails.

4. If the explicitly configured mode is `local`, read the corresponding `samples/pr-0N-*.diff`, clearly label the run as a local fixture, and simulate the publish gate without performing a remote write.

5. Respect the file scope requested by the user. Do not review files outside that scope. Assemble the complete in-scope PR diff and the complete in-scope changed-file list, keeping both application and test files together — do not drop test files when the scope contains both.

6. Delegate the review to the three review subagents, in this exact order, waiting for each subagent to finish before calling the next one. Pass the identical complete in-scope PR diff and the identical complete in-scope changed-file list to every subagent.

   a. `style-reviewer` — require it to check changed `.go` files (`app-conventions` for files not ending in `_test.go`, `test-conventions` for files ending in `_test.go`) and return its style findings, or return exactly `No style findings.`

   b. `security-reviewer` — require it to check the diff for hardcoded credentials, SQL and shell injection, unsafe TLS configuration, concrete missing validation, secret exposure, and production debug endpoints, and return its security findings, or return exactly `No security findings.`

   c. `architecture-reviewer` — require it to check the diff against the `architecture-guidelines` skill (layered architecture, repository pattern, configuration, dependency injection) and return its architecture findings, or return exactly `No architecture findings.`

7. If any subagent cannot run or does not return a result, do not invent a response for it. Clearly report that the review is incomplete and identify which subagent failed. Do not proceed to consolidation or publication for an incomplete review.

8. Once all three subagent results are available, consolidate them into one review:

   - Preserve every supported finding returned by the subagents.
   - Organize findings by file.
   - Every finding must begin with `[HIGH]`, `[MEDIUM]`, or `[LOW]`.
   - Preserve file and line evidence, rule or guideline names, and suggested changes.
   - Do not duplicate the same issue.
   - If a hardcoded credential is reported by `security-reviewer` and also violates the Configuration guideline reported by `architecture-reviewer`, combine them into a single `[HIGH]` finding containing both the security explanation and the Configuration guideline reference — do not list it twice.
   - Mask credential values in the final review; never reproduce a complete credential.
   - For clean files, write `No findings.`
   - After deduplication and merging are complete, recompute all severity totals from scratch by counting only the findings actually present in the final consolidated review body. Do not carry over or sum the per-subagent counts. A finding that was merged into another finding must be counted once, under the severity of the merged finding, and must not also be counted separately. `No findings.`, `No style findings.`, `No security findings.`, and `No architecture findings.` lines are not findings and must not be counted.
   - Finish with a `## Summary` section giving the total count of `HIGH`, `MEDIUM`, and `LOW` findings, and verify that count against the findings listed above it before presenting the review.

9. Present the complete consolidated review and identify the reviewed repository, PR number, and head SHA.

10. Ask: `Post this as a comment? Reply yes or post.` Do not publish during the same turn.

11. Only after a subsequent explicit `yes` or `post`, confirm that the PR head SHA has not changed and use GitHub MCP `add_issue_comment` to post an ordinary PR conversation comment, if the server permissions allow it.

12. If the user answers `no`, perform no remote write.

13. Subagents are read-only reviewers: they must never post comments or perform any GitHub write operation. Only the main agent may offer publication, and only after presenting the complete consolidated review.
