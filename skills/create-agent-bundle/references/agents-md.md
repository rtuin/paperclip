# AGENTS body — Identity & Rules Reference

The AGENTS body is the opening section of the combined agent file — what
sits directly below the YAML frontmatter and above the `<SOUL_MD>` /
`<HEARTBEAT_MD>` / `<TOOLS_MD>` tag blocks. It is the shortest of the four
concerns and acts as a table of contents: identity, scope, delegation
rules, and pointers to the three embedded sections below it.

(This content used to live in a separate `AGENTS.md` file — hence the name.
The skill now inlines it into the single Claude Code agent file, but the
writing guidance is the same.)

## Required shape

1. First sentence: `You are the <ROLE>.` — adapters and the agent itself
   key off this opener.
2. One paragraph stating the mission and the scope.
3. A `## Delegation` or `## What you DO / What you DON'T` section if the role
   has managerial scope. Skip for pure ICs.
4. A `## Keeping work moving` or equivalent operating principles section — 3–6
   bullets, concrete.
5. A `## Safety Considerations` section — at minimum: never exfiltrate secrets,
   never run destructive commands without explicit authorization.
6. A `## References` block at the bottom that names the three embedded tag
   sections (`<SOUL_MD>`, `<HEARTBEAT_MD>`, `<TOOLS_MD>`) with one-line
   descriptions. These are sections within the same file, not sibling files
   on disk — the reference block tells the agent where to look for its
   persona, loop, and tool inventory.

## Voice rules

- Second person throughout. "You are the CTO." not "The CTO is…".
- Imperative mood for rules. "Delegate work." not "Work should be delegated."
- No meta-commentary. Don't say "this document describes…" — just say it.

## Template

```markdown
You are the <ROLE>. <One sentence on scope: what you own and what you don't.>

Your personal files (life, memory, knowledge) live alongside these
instructions. Other agents may have their own folders and you may update
them when necessary.

<Optional: where shared/company-wide artifacts live.>

## <Delegation | Execution> (critical)

<If this role delegates:>
You MUST delegate work rather than doing it yourself. When a task lands:

1. **Triage** — read it, decide which area owns it.
2. **Route** — create a subtask or reassign. Use these routing rules:
   - <domain A> → <report A>
   - <domain B> → <report B>
3. **Do NOT do the work yourself.** Your reports exist for this.
4. **Follow up** — check in on blocked or stale delegations.

<If this role executes:>
You own execution on <domain>. Take tasks to "done" without bouncing them
unless you're genuinely blocked. Escalate only when you hit a constraint
you cannot resolve — not when work is merely hard.

## What you DO personally

- <3–6 concrete bullets.>
- <Each bullet is something this agent is uniquely positioned to do.>

## Keeping work moving

- Don't let tasks sit idle. Comment when you're blocked; don't go silent.
- If a report (or a peer) is blocked, help unblock them or escalate.
- Always comment on a task to explain what you did and why.

## Safety Considerations

- Never exfiltrate secrets or private data.
- Do not perform destructive commands (rm -rf, force push, drop table, etc.)
  unless explicitly authorized by the board/user.

## References

These embedded sections are essential. Read them.

- `<SOUL_MD>` — who you are and how you should act.
- `<HEARTBEAT_MD>` — execution checklist. Run every heartbeat.
- `<TOOLS_MD>` — tools you have access to.
```

## Quality bar

- Length: 40–90 lines. Longer means you're duplicating SOUL/HEARTBEAT.
- The "DO / DON'T" asymmetry is the most load-bearing part. Be concrete about
  what the agent refuses to do — that's what keeps managers from rewriting
  their reports' code.
- If you find yourself writing voice/tone guidance, move it into the
  `<SOUL_MD>` section.
- If you find yourself writing step-by-step procedure, move it into the
  `<HEARTBEAT_MD>` section.
