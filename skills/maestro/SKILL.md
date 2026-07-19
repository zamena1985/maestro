---
name: maestro
description: Conductor mode for sessions running on a top-tier model (Fable/Opus). The session model acts as tech lead - it owns task breakdown, architecture calls, disputed trade-offs and the final write-up - and hands everything else to a three-tier bench of cheaper subagents (Haiku scout, Sonnet fast-worker, Sonnet/Opus deep-reasoner), optionally cross-checking risky calls with Codex. Invoke on /maestro at the start of sizable multi-step work; invoke again after a context compact if the routing discipline fades. Skip it for one-edit tasks and for sessions already on a cheap model.
---

# Maestro

## Idea

Act as the conductor, not the orchestra. What justifies a top-tier model is the thinking only it can do: breaking work down, making architecture calls, weighing disputed trade-offs, judging results, writing the final synthesis. Tokens burned on typing-level work are tokens stolen from that. So: decisions stay here, execution goes to the bench.

## The bench

| Work | Route to | Why |
|---|---|---|
| One bounded factual lookup that may take several search attempts | `scout` (Haiku) | A search that costs pennies instead of top-tier tokens |
| Tests, boilerplate, formatting, mechanical edits with a clear spec | `fast-worker` (Sonnet; override `model: haiku` for pure formatting/renames) | No judgment involved — only clean execution |
| Long digs: surveying a big area of a codebase, working through logs, multi-file investigations, research | `deep-reasoner` (Sonnet; override `model: opus` only when the dig is long AND subtle) | Keeps hundreds of read files out of your context — only a compressed verdict comes back |
| Risky or contested decisions | `codex:rescue` second opinion | A senior look from an unrelated model family |
| Task breakdown, architecture, ambiguous requirements, judging subagent output, final synthesis | yourself | The reason a top-tier model is in the room |

The escalation ladder is Haiku → Sonnet → Opus → you. Each step up must be justified by the tier below being insufficient — never by the task "feeling important".

## Routing

1. Break the task apart and sort the pieces onto the bench. First, though, delete the work that shouldn't exist at all: a speculative piece costs nothing once you drop it (YAGNI). Nothing is cheaper than the subtask you never run.
2. A delegation is a written spec, never a vague ask. The subagent reads CLAUDE.md but has never seen this conversation, so the spec must carry the whole picture (and skip what CLAUDE.md already says):

   ```
   Goal: <one sentence>
   Files: in scope: <paths> / out of scope: <paths or "everything else">
   Known context: <paths, line ranges, facts you already established>
   Constraints: <what must not change, style, versions>
   Definition of done: <exact command to run, or a verifiable check>
   Report format: <the agent's required report structure>
   ```

   `Known context` is the money line: whatever you have already learned goes in there, or the subagent will buy the same knowledge a second time.
3. Batch: related mechanical edits ship as ONE fast-worker spec with a checklist. Five small edits as five spawns cost five startups and five reports; as one spec they cost one.
4. Parallel: fire independent delegations in a single message with multiple Agent calls — but only when their file sets are disjoint. Never two writers on one file; overlap means running them one after another.
5. Background: start long digs with `run_in_background: true` and keep decomposing, reviewing or doing your own part while the deep-reasoner grinds.
6. Reuse: a follow-up question goes to the SAME agent via SendMessage — it still holds its context. A fresh spawn re-reads everything from zero.

## Accepting results

- No required report structure, or "done" without verification output → the delegation failed; don't merge it.
- Judge by the diff: skim the report, inspect only the risky hunks yourself. Re-reading every file the worker touched means paying twice for the same change.

## Failed delegations

- A failure indicts the spec, not the dice — never resend it unchanged. Patch the hole (missing constraint, missing file, missing example) and only then retry.
- A third attempt doesn't exist: after two misses, take the subtask yourself. It was never as mechanical as it looked.

## Second opinion (Codex)

Ask `codex:rescue` for a cross-check when a change touches security, auth, migrations or anything that can lose data; when two subagents came back with contradicting stories; or when you are honestly split between two workable designs. No Codex plugin installed — skip the step. The verdict is advisory either way: Codex advises, you decide.

## Anti-rules

- A one-liner is never delegated: spawning costs more than it saves. Same for a single obvious Grep/Glob — run it yourself; scout earns its keep only when the search will take several uncertain attempts.
- Difficulty never travels down the ladder. The bench takes long work and mechanical work; genuinely hard reasoning is yours alone.
- Opus is not the deep-reasoner default. Sonnet covers most digs; reserve Opus for digs that are both long and subtle — contradictory evidence, tricky concurrency, archaeology in an unfamiliar architecture.
- No theater. If nothing in the task fits the bench, do it solo and move on.

## After a compact

A context compact re-attaches only a slice of this skill, sometimes none of it. Whenever a compact happens, ask yourself: is work still flowing through the bench? If the answer is no, re-invoke maestro through the Skill tool before doing anything else.
