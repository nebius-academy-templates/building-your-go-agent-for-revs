# PR-02 output

Source: live `/review-pr 2` run in this session (three-subagent sequential delegation —
style-reviewer, security-reviewer, architecture-reviewer — followed by consolidation).

**Repository:** ispaniuktripleten/go-pr-review-practice
**PR:** [#2](https://github.com/ispaniuktripleten/go-pr-review-practice/pull/2) ("Add notification scheduling manager")
**Head SHA:** `3c00000305f36d1dcb220f1514f2932fa8c93bdf`
**Base:** main @ `ae2cfe8b499427b367a653bf6981dba6de364b70`

## internal/notifications/manager.go

**[LOW]** Line 3 — `type notification_manager struct` uses snake_case; unexported type names should use mixedCaps.
- Suggested change: rename to `notificationManager`.
- Rule: app-conventions Rule 1 (naming).

**[LOW]** Line 4 — `pending_notifications []string` field uses snake_case instead of mixedCaps.
- Suggested change: rename to `pendingNotifications`.
- Rule: app-conventions Rule 1 (naming).

**[LOW]** Line 7 — `func (m *notification_manager) Schedule_notification(Title string)`: the exported method name uses snake_case instead of MixedCaps, and the parameter `Title` is capitalized like an exported identifier when local parameters should use mixedCaps.
- Suggested change: `func (m *notificationManager) ScheduleNotification(title string) { ... }`
- Rule: app-conventions Rule 1 (naming).

**[LOW]** Line 7 — `Schedule_notification` is exported (capitalized) but has no Go doc comment beginning with the declared name.
- Suggested change: add `// ScheduleNotification appends title to the pending notification queue.` above the method.
- Rule: app-conventions Rule 2 (exported doc comments).

**[LOW]** Line 11 — `func (m *notification_manager) Get_pending() []string` uses snake_case instead of MixedCaps.
- Suggested change: rename to `GetPending`.
- Rule: app-conventions Rule 1 (naming).

**[LOW]** Line 11 — `Get_pending` is exported but has no Go doc comment beginning with the declared name.
- Suggested change: add `// GetPending returns a copy of the pending notifications.` above the method.
- Rule: app-conventions Rule 2 (exported doc comments).

No security findings (no credentials, injection, TLS, validation, or exposure concerns — the file has no I/O or external input).
No architecture findings (single-file, self-contained package; no layering, repository, configuration, or dependency-injection concerns apply).

## Summary

**6 findings total — 0 HIGH, 0 MEDIUM, 6 LOW.**

The PR adds a small, self-contained `notifications` package. All findings are style-only: every identifier in the file (`notification_manager`, `pending_notifications`, `Schedule_notification`, `Get_pending`, and the `Title` parameter) uses snake_case instead of Go's MixedCaps/mixedCaps convention, and the two exported methods lack Go doc comments. No security or architecture issues were found.
