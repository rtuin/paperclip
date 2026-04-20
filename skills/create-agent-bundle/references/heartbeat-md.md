# HEARTBEAT.md — Execution Loop Reference

`HEARTBEAT.md` is the **checklist the agent runs every time it wakes up**. It
is procedural, observable, and in a fixed order. A reader should be able to
watch the agent work and tick each step off.

Where `SOUL.md` is "how you think" and `AGENTS.md` is "what you own,"
`HEARTBEAT.md` is "what you do, in order, right now."

## Required shape

1. Heading: `# HEARTBEAT.md -- <Role> Heartbeat Checklist`
2. One-line opener describing when to run this checklist ("Run on every
   heartbeat.").
3. Numbered sections, each corresponding to a phase of the loop. Typical
   phases:
   1. **Identity / Context** — confirm who you are and what you were woken
      for (task id, wake reason, caller).
   2. **Planning check** — read today's plan / memory files if the agent has
      them.
   3. **Approval / escalation follow-up** — only if the role hits governance
      gates.
   4. **Get assignments** — how the agent discovers what to work on.
   5. **Checkout and work** — claim a task, do the work, update status.
   6. **Delegate** (managers only) — how to create subtasks and pick assignees.
   7. **Fact extraction / memory write** — if the agent persists knowledge.
   8. **Exit** — how to cleanly stop. Comment before exiting, etc.
4. (Optional) A short "Responsibilities" or "Rules" epilogue after the
   checklist, separated by a horizontal rule. Keep it to a handful of bullets.

Each numbered step should have sub-bullets or a short snippet showing HOW to
run it — either a concrete API call, CLI command, or file read. Vague
instructions ("reflect on priorities") are useless to a runtime.

## Writing steps well

- **Observable.** "Read `./memory/YYYY-MM-DD.md` under '## Today's Plan'" is
  observable. "Think about your day" is not.
- **Ordered.** The heartbeat is a total order. If two steps can run in
  either order, they're probably one step.
- **Short.** Each step should fit on a screen. If it doesn't, split it or
  push detail into a reference doc under the bundle.
- **Include commands.** Where an API or CLI is involved, show the exact
  invocation. Fenced blocks, not prose.
- **Include the exit condition.** What makes a step "done" for this wake?

## What NOT to put here

- Persona / voice — that's `SOUL.md`.
- Tool catalogues — that's `TOOLS.md`.
- Policy (what the agent refuses to do) — that's `AGENTS.md`.
- Long background context. Link to it; don't inline it.

## Template

```markdown
# HEARTBEAT.md -- <Role> Heartbeat Checklist

Run this checklist every time you wake. It covers <summary of the loop>.

## 1. Identity and Context

- Confirm who you are: <how — API call, env var check, etc.>
- Check wake context: <what env vars / task ids to inspect>

## 2. <Planning / Memory Check>

1. Read <path> for today's plan.
2. Review each planned item: done, blocked, up next.
3. For blockers, resolve or escalate.
4. Record progress in <path>.

## 3. Get Assignments

- <API call or query to discover assigned work>
- Prioritization rules: <in_progress before todo before blocked>, etc.

## 4. Checkout and Work

- <How to claim work without stepping on other agents>
- Do the work. Update status. Comment when done.

Status quick guide (if relevant):

- `todo`: <meaning>
- `in_progress`: <meaning>
- `in_review`: <meaning>
- `blocked`: <meaning>
- `done`: <meaning>

## 5. <Delegate | Execute>

<If managerial:>
- Create subtasks: <command or API>
- Always set <parentId, goalId, etc.>
- Assign work to the right report.

<If IC:>
- Take the task to done. Don't re-route unless you hit a genuine blocker.

## 6. <Fact Extraction | Learning>

<Only if the agent has memory. Otherwise skip this section.>

1. <extraction step>
2. <write step>

## 7. Exit

- Comment on any in-flight work before exiting.
- If nothing is assigned, exit cleanly.

---

## <Role> Responsibilities

- <3–5 bullets. Anchors, not procedure.>

## Rules

- <3–5 hard rules. "Always include X header on mutating calls.">
```

## Quality bar

- Length: 60–130 lines. A heartbeat longer than that is a symptom that the
  role is doing too many things.
- Every step is actionable without reading another file first.
- Commands and API calls are in fenced blocks, copy-pasteable.
- The loop ends. It does not recurse. Exit is a step.
