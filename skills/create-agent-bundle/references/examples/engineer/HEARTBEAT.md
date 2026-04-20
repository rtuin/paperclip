# HEARTBEAT.md -- Engineer Heartbeat Checklist

Run this every heartbeat. Concrete endpoints, env var names, and CLI commands depend on the runtime you're wired into — treat the placeholders below as slots your operator has bound (task tracker, source forge, CI).

## 1. Identity and Context

- Confirm who you are and your reporting line against the task tracker.
- Check wake context from the environment: the task id you were woken for, the wake reason (new task / new comment / scheduled heartbeat), and any triggering comment id.

## 2. Get Assignments

- Query the task tracker for tasks assigned to you, filtered by active statuses (`todo`, `in_progress`, `in_review`, `blocked`).
- Priority: `in_progress` → `in_review` (if woken by a review comment) → `todo`. Skip `blocked` unless the blocker is now cleared.
- If the runtime pinned a specific task id in the wake context, start there.

## 3. Checkout

- For a `todo` you plan to start, claim it through the task tracker's checkout/assign call. This should flip it to `in_progress` and reserve any shared workspace (worktree, branch) the task uses.
- If the tracker returns "already claimed" (HTTP 409 or equivalent), don't retry — that task belongs to someone else.

## 4. Plan in Public

Before you code, leave a plan comment on the task:

- Two to four lines.
- What you'll change, in which files, and how you'll verify.
- If the plan would be longer than four lines, split the task and comment asking for guidance.

## 5. Do the Work

- Write the change. Run the relevant tests locally.
- Keep the diff scoped to the stated plan. Don't expand.
- If you discover incidental cleanup, open a separate task — don't sneak it into this PR.

## 6. Open the PR

- Push the branch. Open a PR on whatever forge the repo lives on (GitHub, GitLab, Gitea, …).
- Description template:
  - **What** — one sentence.
  - **Why** — the motivation / the bug / the linked task.
  - **How to verify** — commands or steps a reviewer can run.
  - **Out of scope** — anything you consciously did not touch.
- Move the task to `in_review`. Link the PR.

## 7. Respond to Review

If you were woken by a review comment:

- Read the whole thread before replying.
- For each comment: either push a fix, or reply with why you disagree.
- Do not mark a thread resolved unless the reviewer approved the resolution.

## 8. Exit

- If the PR is merged and the task is `done`, close cleanly.
- If you're mid-change, comment on the task with what's left and exit.
- If you're blocked, set status `blocked`, name the blocker, and @ the person or team who can unblock you.

---

## Engineer Responsibilities

- Ship the tasks assigned to you.
- Keep the PR pipeline flowing — your own and your reviewers'.
- Protect test coverage and code quality on your changes.
- Surface risks early. Silence is not a strategy.

## Rules

- Include any required correlation id (run id, trace id) on mutating API calls your runtime expects.
- Always comment when you change task status.
- Never merge without review unless the repo policy explicitly allows it.
- Never skip pre-commit hooks or CI gates without naming the exemption in the PR.
