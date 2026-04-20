# SOUL section — Persona Reference

The SOUL section (wrapped in `<SOUL_MD>…</SOUL_MD>` in the combined agent
file) captures **identity, not procedure**. It answers "who is this agent"
and "how do they sound." A reader should be able to pick up this section
alone and role-play the agent convincingly, without knowing what tasks are
in the queue.

If you're writing a step, it belongs in the `<HEARTBEAT_MD>` section. If
you're writing a tool, it belongs in the `<TOOLS_MD>` section. SOUL is the
feel.

## Required shape

1. Heading: `# SOUL -- <Role> Persona`
2. One-line opener: `You are the <ROLE>.`
3. A **posture / principles** section — the beliefs, priors, and trade-off
   heuristics that shape decisions. 8–14 bullets. Each bullet is a principle,
   not a task. Framed as "how this agent thinks," not "what this agent does."
4. A `## Voice and Tone` section — how the agent communicates in writing.
   6–12 bullets. Specific. "No exclamation points unless something is on fire"
   beats "be professional."

Optional sections (use when relevant, don't pad):

- `## What you refuse` — anti-patterns the agent rejects. Useful for managers
  who shouldn't do IC work, or ICs who shouldn't do scope creep.
- `## How you disagree` — stance on pushback, challenge, conflict.
- `## What counts as done` — definition-of-done priors.

## Principles that make SOUL good

- **Specific > generic.** "Stay within hours of truth on revenue, burn,
  runway" beats "be data-driven."
- **Named trade-offs.** Great principles point at a tension and resolve it:
  "Optimize for learning speed and reversibility. Move fast on two-way doors;
  slow down on one-way doors."
- **Behavioral not aspirational.** "Pull for bad news and reward candor"
  (behavior) beats "value transparency" (aspiration).
- **Voice has teeth.** "Skip the corporate warm-up. No 'I hope this message
  finds you well.'" gives the agent a clear do/don't.

## What to avoid

- Generic leadership quotes. No "be a servant leader."
- Restating mission. The mission is in the AGENTS body (above this section).
- Listing responsibilities. Those are in the AGENTS body under "What you DO."
- Listing procedures. Those are in the `<HEARTBEAT_MD>` section (below).
- Marketing copy about the company. SOUL is about the agent, not the brand.

## Template

```markdown
# SOUL -- <Role> Persona

You are the <ROLE>.

## <Posture | Mindset | Operating Principles>

- <Principle 1 — a named trade-off or prior, stated as how you think.>
- <Principle 2 — behavioral, specific, falsifiable.>
- <...>
- <8 to 14 bullets total. Cut the weakest two before finalizing.>

## Voice and Tone

- Be direct. Lead with the point, then give context.
- <Specific voice rule: sentence length, forbidden phrases, structure.>
- <Rule about uncertainty: how you hedge or don't.>
- <Rule about praise or criticism: when, how specific, how rare.>
- <Rule about emoji / exclamation / formality.>
- <6 to 12 bullets. Each one should be something a reader could catch you
  violating.>
```

## Quality bar

- Length: 40–80 lines. Longer means you're padding or mixing in procedure.
- Read the bullets aloud. If a bullet could apply to any professional agent
  ("be thoughtful", "communicate clearly"), cut it or make it specific.
- The voice section should make the agent's writing recognizable. A reader
  should be able to spot this agent's replies in a feed of mixed messages.
- Echo the mission from the AGENTS body — the posture should fit what the agent
  is actually asked to do. A caution-heavy compliance officer and a
  move-fast founder have different souls; don't give them both the same
  "default to action" bullet.
