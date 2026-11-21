# Issue drafts

Drafts for GitHub issues. Each file is the body that the [requirement form](../.github/ISSUE_TEMPLATE/requirement.yml) produces. The issue title and label are in the HTML comments at the top of each file.

- [00-REQ-EPIC-001.md](00-REQ-EPIC-001.md) is the epic. Its **Upstream** section lists every child requirement and the decisions that block them.
- Every issue starts in `Draft` with **Author** set to `UNKNOWN`. Set the author when you file the issue.
- File the child issues first, then the epic. If you file through the form, copy each field in. If you file with `gh issue create --body-file`, the body is used as written.
- Once you file an issue, GitHub is the source of truth. Delete the draft here so the two don't drift apart.
