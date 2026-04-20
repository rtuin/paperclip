---
name: create-agent-bundle
description: >
  Generate a four-file agent bundle (AGENTS.md, SOUL.md, HEARTBEAT.md, TOOLS.md)
  that defines a long-running, filesystem-backed agent. Use when the user asks
  to "create an agent", "scaffold an agent", "write a soul file", or otherwise
  wants a durable markdown agent definition that any runtime (Claude Code
  harness, OpenClaw/NanoClaw, Paperclip, a bespoke loop) can load as its
  personality plus operating loop. This skill writes files; it does NOT call
  any control-plane API.
---

# Create Agent Bundle

Use this skill when the user wants to scaffold an agent as a set of markdown
files. The output is a runtime-agnostic bundle: a single entry file
(`AGENTS.md`) that references three siblings describing identity (`SOUL.md`),
loop (`HEARTBEAT.md`), and capabilities (`TOOLS.md`).

This convention was popularized by OpenClaw/NanoClaw and is used by several
orchestrators (Paperclip's `claude_local` adapter, bare Claude Code harnesses,
custom loops). The files themselves have no vendor lock-in — they're just
markdown.

This is NOT for creating Claude Code sub-agents (those live in
`.claude/agents/*.md` with YAML frontmatter and are a different format). This
is for "proper" agents: long-running personas with an execution loop.

## When to invoke

Trigger on any of:

- "create an agent", "scaffold an agent", "new agent"
- "write a soul.md", "generate AGENTS.md for X"
- "I need a CTO agent" / "scaffold an X" when the user wants files
- "agent files in the OpenClaw style"

Do NOT invoke for:

- Claude Code sub-agents in `.claude/agents/` — those use a different schema.
- Hiring via a running orchestrator's API (e.g. a live Paperclip instance) —
  use that orchestrator's own create-agent workflow.

## The bundle shape

```
<target-dir>/
  AGENTS.md      # entry file. Loaded first. References the others.
  SOUL.md        # persona: beliefs, posture, voice, tone
  HEARTBEAT.md   # numbered checklist the agent runs every wake
  TOOLS.md       # tools the agent can use, with usage notes
```

`AGENTS.md` is the **entry** — most runtimes pick it up by name. The other
three are sibling references that `AGENTS.md` tells the agent to read.

## Workflow

### 1. Gather intent

Ask the user (one compact question block, not a quiz):

1. **Role** — short slug and human title (e.g. `cto` / "Chief Technology Officer").
2. **Mission** — one sentence: what does this agent exist to accomplish?
3. **Scope of ownership** — what they own; what they delegate; what they refuse.
4. **Reporting** — do they report to someone, and do they have reports?
5. **Voice/tone** — direct, warm, terse, scholarly? Any forbidden phrases?
6. **Runtime (optional)** — where will this run (Claude Code, OpenClaw,
   Paperclip, custom harness)? Only ask if it changes what you write into
   `HEARTBEAT.md` or `TOOLS.md`. If the user doesn't know or doesn't care,
   default to runtime-agnostic prose.
7. **Tools** — which external systems, CLIs, or APIs this agent uses.
8. **Target directory** — absolute path where the bundle should land. Default:
   `./agents/<role-slug>/` under the current working directory.

If the user gave you a rich brief already (a paragraph describing the role),
infer answers and confirm the inferences in a short bulleted summary before
writing files. Do not block on questions you can answer from context.

### 2. Draft the four files

Work through the files in this order. Quality bars are in each reference doc —
read the reference before drafting that file.

1. `AGENTS.md` — entry. Read [references/agents-md.md](references/agents-md.md).
2. `SOUL.md` — persona. Read [references/soul-md.md](references/soul-md.md).
3. `HEARTBEAT.md` — loop. Read [references/heartbeat-md.md](references/heartbeat-md.md).
4. `TOOLS.md` — tools. Read [references/tools-md.md](references/tools-md.md).

Keep the four files coherent with each other. The mission stated in `AGENTS.md`
must be reflected by `SOUL.md`'s posture and `HEARTBEAT.md`'s checklist.

If the user named a specific runtime, you may bind concrete endpoints, env
vars, or CLI commands in `HEARTBEAT.md` / `TOOLS.md`. Otherwise, keep those
files runtime-agnostic — use placeholders like `<orchestrator>` / `<task-id
env var>` / "your task-tracker API" and let the operator bind them later.

### 3. Write to disk

Create the target directory if it does not exist. Write the four files. Do not
overwrite existing files without confirming; if a file already exists, show the
user what would change and ask.

### 4. Report

Print a short summary:

- target path
- role + title
- one-line mission
- list of files written, with byte sizes or line counts
- next step suggestion (how to plug this bundle into a runtime — see
  "Wiring into a runtime" below)

## Wiring into a runtime

The bundle is runtime-agnostic. Common wiring patterns:

- **Bare Claude Code / CLI harness**: concatenate the files as the system
  prompt, or pass `AGENTS.md` via an `--append-system-prompt-file`-style flag
  and let the agent itself read the siblings at runtime. Point Claude Code at
  the bundle directory as its working directory so relative paths resolve.
- **Claude Code sub-agent wrapper**: have your sub-agent's system prompt
  include `Read ./AGENTS.md and the three sibling files before starting.`
  Keep the sub-agent's own frontmatter tight — the bundle carries the body.
- **OpenClaw / NanoClaw**: point the agent's working directory at the bundle
  root. The runtime resolves `AGENTS.md` as the primary instruction file and
  `SOUL.md` / `HEARTBEAT.md` / `TOOLS.md` as sibling references.
- **Paperclip `claude_local` adapter**: set `adapterConfig.instructionsFilePath`
  to the absolute path of the bundle's `AGENTS.md`. The adapter loads it and
  the sibling files automatically.
- **Bespoke orchestrator / cron loop**: read and inline all four files into
  the system prompt each wake, or mount the directory and instruct the agent
  to re-read it on every invocation.

## Quality bar

Before finishing:

- `AGENTS.md` MUST open with `You are the <ROLE>.` and end with a reference
  block that lists the three sibling files.
- `SOUL.md` MUST have a `## Voice and Tone` section and a posture/principles
  section — abstract beliefs, not task instructions.
- `HEARTBEAT.md` MUST be runnable as a numbered checklist. Each step is
  concrete and observable. No vague "think about X".
- `TOOLS.md` SHOULD be populated if tools are known; otherwise leave the
  "(Your tools will go here...)" stub so the agent can append as it acquires
  tools. Never invent tools that don't exist.
- Do not duplicate content across files. `AGENTS.md` summarizes and links;
  `SOUL.md` is identity; `HEARTBEAT.md` is procedure; `TOOLS.md` is inventory.
- Keep each file skimmable. Short sentences. Bullets over prose.
- No emoji unless the user asked.
- No placeholder text like `TODO: fill this in` in the final files. Either
  write real content or remove the section.
- If the user didn't name a runtime, don't hardcode one. Prefer
  `<orchestrator>` / `<task-tracker>` placeholders over a concrete API.

## Examples

Two reference bundles live under `references/examples/`:

- `references/examples/cto/` — engineering leader with reports
- `references/examples/engineer/` — individual contributor with no reports

Both are deliberately runtime-agnostic — they show the *shape* of a good
bundle without binding to a specific orchestrator's API. Read them when you
need a concrete shape. Do not copy them verbatim: the agent's mission and
voice must be specific to what the user asked for.
