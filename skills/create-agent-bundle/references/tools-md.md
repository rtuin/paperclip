# TOOLS.md — Tool Inventory Reference

`TOOLS.md` is the **living inventory of tools the agent can reach**. It's the
file the agent updates itself as it acquires, uses, and retires capabilities.

At bundle creation time, the file is often near-empty — that's fine. The
purpose is to reserve the slot so the agent has a canonical place to write
"I can now do X" as it learns.

## Required shape

1. Heading: `# Tools`
2. Either:
   - (a) An initial stub when no tools are known yet (very common for new
     agents), or
   - (b) One subsection per tool category / tool, with usage notes.

## Stub form (new agent, tools TBD)

```markdown
# Tools

(Your tools will go here. Add notes about them as you acquire and use them.)
```

This matches Paperclip's default CEO onboarding bundle and is the right
default when the user hasn't listed specific tools.

## Populated form

When the user tells you about specific tools the agent uses, write a proper
inventory. Organize by category (APIs, CLIs, internal services, memory
systems, etc.), not alphabetically.

```markdown
# Tools

## <Category — e.g. "Paperclip API">

### <tool name>

**What it is.** One sentence.

**When to use.** One or two sentences. Be opinionated — say what problem it
solves and what alternative you should prefer when.

**How to call.**

\`\`\`bash
<exact command or curl>
\`\`\`

**Gotchas.**

- <Known failure mode or constraint.>
- <Required env var, auth header, rate limit, etc.>

## <Next category>

...
```

## Writing rules

- **Don't invent tools.** If the user hasn't confirmed a tool exists, don't
  list it. A wrong entry is worse than no entry — the agent will try to call
  something that isn't there.
- **Show the invocation.** A tool entry without a command or API call is
  half a tool. The agent needs to know how to actually reach it.
- **Note the gotchas.** Env vars, auth, rate limits, quirks — anything the
  agent will trip over on first use.
- **Leave room for growth.** Even in the populated form, end with a line
  like "Add new tools here as you acquire them."

## What NOT to put here

- Persona or voice. That's `SOUL.md`.
- Operational procedure (the ordered loop). That's `HEARTBEAT.md`.
- Delegation policy or scope. That's `AGENTS.md`.
- Full API reference documentation. Link to it; don't inline it.

## Common categories to suggest when prompting the user

If you need to draw tools out of the user, ask about:

- Control plane / orchestration APIs (Paperclip, OpenClaw, your own harness).
- Code tools (git, GitHub, package managers, test runners).
- Shell / filesystem scope (what paths can the agent read, write, exec).
- External services (Slack, email, ticket tracker, analytics, databases).
- Memory / knowledge stores (vector DB, note files, PARA folders).
- Secrets sources (env vars, vault, KMS).

## Quality bar

- Length: either the 2-line stub OR a real inventory. Never a half-populated
  placeholder with `TODO` entries.
- Every listed tool has: what, when, how, and at least one gotcha.
- The file ends with an invitation for the agent to append new tools.
