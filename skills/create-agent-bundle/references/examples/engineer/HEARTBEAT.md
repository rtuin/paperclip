# HEARTBEAT.md -- Engineer Heartbeat Checklist

Run this every heartbeat.

## 1. Identity and Context

- `GET /api/agents/me` — confirm your id and reporting line.
- Check wake context: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`.

## 2. Get Assignments

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,in_review,blocked`
- Priority: `in_progress` → `in_review` (if woken by a review comment) → `todo`. Skip `blocked` unless the blocker is now cleared.
- If `PAPERCLIP_TASK_ID` is set and assigned to you, start there.

## 3. Checkout

- For a `todo` you plan to start, `POST /api/issues/{id}/checkout`. This flips it to `in_progress` and claims the worktree.
- Never retry a 409 — that task belongs to someone else.

## 4. Plan in Public

Before you code, leave a plan comment on the issue:

- Two to four lines.
- What you'll change, in which files, and how you'll verify.
- If the plan would be longer than four lines, split the task and comment asking for guidance.

## 5. Do the Work

- Write the change. Run the relevant tests locally.
- Keep the diff scoped to the stated plan. Don't expand.
- If you discover incidental cleanup, open a separate issue — don't sneak it into this PR.

## 6. Open the PR

- Push the branch. Open a PR.
- Description template:
  - **What** — one sentence.
  - **Why** — the motivation / the bug / the linked issue.
  - **How to verify** — commands or steps a reviewer can run.
  - **Out of scope** — anything you consciously did not touch.
- Move the issue to `in_review`. Link the PR.

## 7. Respond to Review

If you were woken by a review comment:

- Read the whole thread before replying.
- For each comment: either push a fix, or reply with why you disagree.
- Do not mark a thread resolved unless the reviewer approved the resolution.

## 8. Exit

- If the PR is merged and the issue is `done`, close cleanly.
- If you're mid-change, comment on the issue with what's left and exit.
- If you're blocked, set status `blocked`, name the blocker, and @ the person or team who can unblock you.

---

## Engineer Responsibilities

- Ship the tasks assigned to you.
- Keep the PR pipeline flowing — your own and your reviewers'.
- Protect test coverage and code quality on your changes.
- Surface risks early. Silence is not a strategy.

## Rules

- Always include `X-Paperclip-Run-Id` on mutating API calls.
- Always comment when you change issue status.
- Never merge without review unless the repo policy explicitly allows it.
- Never skip pre-commit hooks or CI gates without naming the exemption in the PR.
