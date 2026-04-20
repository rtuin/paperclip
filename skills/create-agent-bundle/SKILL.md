---
name: create-agent-bundle
description: >
  Generate a Claude Code agent definition file (~/.claude/agents/<role>.md or
  .claude/agents/<role>.md) that embeds a full OpenClaw-style persona bundle
  — AGENTS body, SOUL, HEARTBEAT, TOOLS — as one file the user can launch
  with `claude --agent <role>`. Use when the user asks to "create an agent",
  "scaffold an agent", "write a soul file", or otherwise wants a durable
  markdown agent definition. This skill writes one file; it does NOT call
  any control-plane API.
---

# Create Agent Bundle

Use this skill when the user wants to scaffold a long-running agent persona
for Claude Code. The output is a **single file** at either
`~/.claude/agents/<role>.md` (user-level) or `./.claude/agents/<role>.md`
(project-level) that Claude Code loads via `claude --agent <role>`.

The file combines four concerns that the OpenClaw/NanoClaw / Paperclip
convention keeps in four sibling files:

- **AGENTS body** — identity, scope, delegation, safety (inlined as the
  opening of the file, below YAML frontmatter)
- **SOUL** — persona, posture, voice & tone (wrapped in `<SOUL_MD>…</SOUL_MD>`)
- **HEARTBEAT** — the ordered loop the agent runs every wake (wrapped in
  `<HEARTBEAT_MD>…</HEARTBEAT_MD>`)
- **TOOLS** — tool inventory with usage notes (wrapped in `<TOOLS_MD>…</TOOLS_MD>`)

Everything lands in one file so `claude --agent <role>` picks it up as the
whole session's system prompt with zero additional wiring.

## When to invoke

Trigger on any of:

- "create an agent", "scaffold an agent", "new agent"
- "write a soul.md", "generate AGENTS.md for X"
- "I need a CTO agent" / "scaffold an X" when the user wants a file to launch
- "agent in the OpenClaw style"

Do NOT invoke for:

- Claude Code project subagents that are meant to be delegated to mid-session
  only (they use the same file format but tend to be narrower, task-dispatch
  personas rather than full session personas). This skill is fine for both;
  just note that a full persona agent is heavier than a "code reviewer"
  subagent by design.

## The output shape

One file. This is exactly what lands on disk:

```markdown
---
name: <role-slug>
description: <one-line trigger / mission description>
---

You are the <ROLE>. <Scope sentence: what you own and what you don't.>

<AGENTS body — delegation/execution rules, what you DO, what you DON'T,
 memory & planning, safety considerations. End with a short pointer to the
 three embedded sections below.>

<SOUL_MD>
# SOUL -- <Role> Persona

<Posture / Mindset bullets — 8 to 14, each a named trade-off or behavioral
 principle.>

## Voice and Tone

<6 to 12 bullets. Specific writing rules the reader could catch you
 violating.>
</SOUL_MD>

<HEARTBEAT_MD>
# HEARTBEAT -- <Role> Loop

<Numbered checklist the agent runs every wake. Identity → planning →
 assignments → work → delegate → fact extraction → exit.>
</HEARTBEAT_MD>

<TOOLS_MD>
# Tools

<Either the "(Your tools will go here...)" stub for a new agent, or a real
 inventory organized by category with what/when/how/gotchas.>
</TOOLS_MD>
```

## Workflow

### 1. Gather intent

Ask the user (one compact question block, not a quiz):

1. **Role** — short slug and human title (e.g. `cto` / "Chief Technology Officer").
   The slug becomes both the filename (`<slug>.md`) and the YAML `name` field.
2. **Description** — one line: when should Claude Code pick up this agent? This
   becomes the YAML `description` field and drives auto-delegation if the
   agent is ever invoked as a subagent.
3. **Mission** — one sentence: what does this agent exist to accomplish?
4. **Scope of ownership** — what they own; what they delegate; what they refuse.
5. **Reporting** — do they report to someone, and do they have reports?
6. **Voice/tone** — direct, warm, terse, scholarly? Any forbidden phrases?
7. **Tools** — which external systems, CLIs, or APIs this agent uses. Optional:
   a restricted `tools:` frontmatter list (e.g. `tools: Read, Edit, Bash, Grep`)
   if the user wants to constrain what Claude Code exposes. Default: omit the
   field and let the agent inherit the full toolset.
8. **Location** — `~/.claude/agents/` (user-level, available in every project)
   or `./.claude/agents/` (project-level, only when `cwd` is this project).
   Default: ask; don't assume.

If the user gave you a rich brief already, infer answers and confirm the
inferences in a short bulleted summary before writing. Do not block on
questions you can answer from context.

### 2. Draft each concern separately, then serialize

Quality bars are in each reference doc — read the reference before drafting
that concern.

1. AGENTS body — [references/agents-md.md](references/agents-md.md)
2. SOUL — [references/soul-md.md](references/soul-md.md)
3. HEARTBEAT — [references/heartbeat-md.md](references/heartbeat-md.md)
4. TOOLS — [references/tools-md.md](references/tools-md.md)

Keep the concerns coherent. The mission stated at the top must be reflected
by SOUL's posture and HEARTBEAT's checklist. Don't duplicate content across
sections: AGENTS body is identity + rules, SOUL is voice + principles,
HEARTBEAT is ordered procedure, TOOLS is inventory.

Once all four are drafted, concatenate into a single file with this exact
structure:

- YAML frontmatter: `name`, `description`, and any optional fields
  (`tools`, `model`) the user asked for.
- AGENTS body inlined directly (no wrapping tag — this is the "main" system
  prompt).
- `<SOUL_MD>…</SOUL_MD>` block.
- `<HEARTBEAT_MD>…</HEARTBEAT_MD>` block.
- `<TOOLS_MD>…</TOOLS_MD>` block.

Rewrite the `## References` block at the end of the AGENTS body to point to
the embedded tag sections below it (`<SOUL_MD>`, `<HEARTBEAT_MD>`,
`<TOOLS_MD>`) — NOT to sibling files on disk, which no longer exist.

### 3. Write to disk

Target path is `~/.claude/agents/<slug>.md` or `./.claude/agents/<slug>.md`,
based on the user's answer in step 1.

Create the parent directory (`mkdir -p`) if it doesn't exist. If the target
file already exists, show the user a diff of what would change and ask
before overwriting.

### 4. Report

Print a short summary:

- target path (absolute)
- role slug + description (first line of YAML)
- one-line mission
- file size / line count
- the exact command to launch: `claude --agent <slug>`

## Launching the agent

After the file lands, the user runs:

```bash
claude --agent <slug>
```

Claude Code resolves the agent from (first match wins): managed settings →
`--agents` CLI JSON → `.claude/agents/` in cwd → `~/.claude/agents/` →
plugin agents. The whole file body becomes the session's system prompt; the
YAML frontmatter drives metadata and optional tool/model restrictions.

## Quality bar

Before finishing:

- The file is valid Claude Code agent shape: YAML frontmatter with at least
  `name` and `description`, followed by markdown body.
- AGENTS body MUST open with `You are the <ROLE>.` and end with a reference
  block pointing to the three embedded tag sections.
- SOUL section MUST have a `## Voice and Tone` subsection and a posture/
  principles subsection — abstract beliefs, not task instructions.
- HEARTBEAT section MUST be runnable as a numbered checklist. Each step
  concrete and observable. No vague "think about X".
- TOOLS section SHOULD be populated if the user named tools; otherwise
  leave the "(Your tools will go here...)" stub. Never invent tools.
- Do not duplicate content across sections.
- Keep each section skimmable. Short sentences. Bullets over prose.
- No emoji unless the user asked.
- No placeholder text like `TODO: fill this in` in the final file. Either
  write real content or remove the section.

## Examples

Two reference agents live under `references/examples/`:

- `references/examples/cto.md` — engineering leader with reports
- `references/examples/engineer.md` — individual contributor with no reports

Both show the exact output shape of this skill (YAML frontmatter + inlined
AGENTS body + three tag-wrapped sections) in a runtime-agnostic style. Read
them when you need a concrete shape. Do not copy them verbatim — the
agent's mission and voice must be specific to what the user asked for.
