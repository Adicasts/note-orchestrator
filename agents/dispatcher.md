# Dispatcher — Purpose-Built Agent Construction & Capability Declaration

Every A (Agent) and O (Orchestrator) item in a NOTE note becomes a purpose-built agent
created exclusively for that task. This document explains how to build these agents
correctly — both the A specialist agents and the O team lead — and how to declare their
capabilities transparently before the user approves the plan.

---

## The core rule: always build fresh

Never route an A item to an existing plugin or pre-built specialist skill unless the user
explicitly names one in the note. Examples of explicit user requests that allow existing
tools:

- `A adops-suite Creative Director` → use the adops-suite plugin
- `A Use the existing brand kit builder` → use brand-kit skill
- `A Run deep-research on this` → invoke the deep-research skill

If the user writes `A Creative Director` or `A Research Analyst` with no plugin
reference, build a purpose-built agent from scratch. Do not silently map it to a plugin.
The user's note names a role, not a tool.

---

## How to build a purpose-built agent

A purpose-built agent is Claude adopting a named role with a complete, isolated brief.
It has no memory of prior sessions or access to anything outside its brief file. Building
it well means the brief file is truly self-sufficient.

### Step 1: Define the role

From the A item text, extract:
- **The role title** (e.g. "Research Analyst", "Creative Director", "Copywriter")
- **The implied expertise** — what does this person know and do? Write 2–3 sentences
  defining the role clearly in the brief under "Who you are"

Do not use vague labels. "Creative Director" should be defined as something like:
> You are a creative director specialising in campaign concepting and visual language.
> Your job is to translate a strategic brief into a clear creative idea — the central
> concept, tone, and visual direction. You do not write copy and you do not design;
> you define the concept so others can execute it.

This prevents role overlap between agents and keeps each one focused.

### Step 2: Determine what the agent actually needs to do

Cross-reference the role against the note's T items, B items, and G to determine:
- What output is this agent producing?
- What inputs does it need from other agents (if any)?
- What is its dependency position in the execution order?

Write these as the "Your specific task" section of the brief. Be concrete — not "research
the market" but "find the top 10 events in Berlin between July and December 2026 where
a premium consumer brand could have a presence. Include event type, expected footfall,
venue, and a contact or URL for each."

**B item attachment rule:** A B item attaches to the immediately preceding A item in the
note — not the following one. If B appears before any A item, treat it as a T item (a
task Claude handles directly). Never scan forward to attach B to a later A. Example:

```
A Research Analyst      ← B below will attach here
B Focus only on Tier 2 cities in Maharashtra
A Copywriter            ← B above does NOT attach here
```

### Step 3: Declare capabilities explicitly

Every agent brief must include a "Capabilities you have for this task" section. This
section is copied directly from what was declared and approved in the Phase 2 plan. The
agent cannot use anything not listed here.

**Capability options:**

| Capability | How to declare it in the brief |
|---|---|
| Web search | `Web search via Diya (the user's browser)` |
| File read | `Read access to: [specific file or folder path]` |
| File write | `Write access to: [specific path]` |
| Computer control | `Computer control of: [App 1, App 2]` |
| Training knowledge only | `No external tools — training knowledge only. Label outputs: [training knowledge — may not be current]` |

If an agent's task genuinely requires a capability that wasn't in the approved plan, stop
execution and ask the user before proceeding. Do not assume approval.

### Step 4: Specify the output

Every brief ends with a clear output specification:
- What format (markdown, structured JSON, bullet list, prose, etc.)
- What file to save to (`[agent-slug]-output.md`)
- Any quality criteria the output must meet

---

## Capability assessment before Phase 2

Before presenting the execution plan (Phase 2), assess what each agent will actually need
to do its job. Use this checklist for each A item:

**Does this agent need current information that Claude's training wouldn't have?**
→ Yes: declare web search via Diya
→ No: training knowledge may suffice (still flag it)

**Does this agent need to read or write files?**
→ Yes: declare which paths; check the user has connected a folder

**Does this agent need to open or control an app on the user's computer?**
→ Yes: list every app by name in the plan. This is a significant action — the user must
see it explicitly.

**Will this agent control the user's browser (Diya) for web search?**
→ Yes: state "Web search via Diya" — this means the browser will open and navigate.
The user should know their browser will be used.

**Does this agent depend on another agent's output?**
→ Yes: note the dependency clearly in the plan so the user understands the sequencing.

---

## Capability declaration format in Phase 2 plan

For each agent in the execution plan, use this sub-block:

```
[N]. A: [Agent Name]
   Task: [one sentence]
   🛠️ Tools: [list all tools — web search, file read/write, bash, etc.]
   🌐 Web search: [Yes — via Diya / No]
   💻 Computer control: [Yes — [App list] / No]
   📚 Knowledge source: [Live research via Diya / Training knowledge only / Mix]
   📥 Depends on: [Agent N output / None]
```

---

## The "no capabilities" exception

If the user explicitly says one of:
- "Don't use any tools"
- "No web search"
- "Use only what you know"
- "Don't open anything"

...then all agents in that note run on training knowledge only, with no external tool
use. All outputs must be labelled: `[Generated from training knowledge — content may not
reflect current state. Verify before using.]`

This exception applies to the entire note unless the user scopes it to specific agents
(e.g. "A Research Analyst — no web search, use what you know").

---

---

## Building the Orchestrator (O) brief

The O brief is built AFTER all A agent briefs exist, because O needs to know the full
team it's managing. O is a purpose-built agent like any other — not a meta-instruction
or a persona Claude adopts. It gets its own brief file and produces its own outputs.

### O brief template

```markdown
# [Orchestrator Name] — Orchestrator Brief

## Who you are
You are a purpose-built orchestrator agent created exclusively to manage this team run.
You do not execute research, write copy, or produce creative work — that's what your
specialist agents are for. Your job is to manage them: understand the full context,
set the dispatch order, review each agent's output before the next one runs, and own
the final synthesis.
[2–3 sentences defining the orchestration lens based on the O label — e.g. "Lead
Strategist" means you evaluate all outputs through a strategic lens and make decisions
about sequencing that serve the overall strategy, not just task completion.]

## Goal
[The G from the note]

## Context
[All C items — full text]

## Directives you enforce across all agents (non-negotiable)
[All D items — you are responsible for ensuring every agent and the final output
respects these constraints]

## Resources available to the team
[R and K items — you determine which agents need access to which resources]

## Your team
[List of all A agents with their role and brief file reference]
| Agent | Role | Brief file |
|-------|------|-----------|
| [Name] | [one sentence role description] | [agent-slug]-brief.md |
| ... | ... | ... |

## Your responsibilities in this run

### 1. Dispatch planning
Before any agent runs, produce a dispatch plan — written to `orchestrator-dispatch.md`:
- Which agents run first (and why)
- Which agents depend on another's output before starting
- What you expect each agent to produce (your acceptance criteria)
- Flag any gaps in the briefs you think need resolving before dispatch

### 2. Output review (after each agent)
After each agent saves its output, you review it against your acceptance criteria:
- Does it meet the bar? → Approve and proceed to the next agent
- Does it fall short? → Flag the specific gap. Present to user before continuing.
  Do not silently carry forward weak output to the next agent.

### 3. Final synthesis
After all agents complete and outputs are approved:
- Read all `*-output.md` files
- Synthesise into a coherent final deliverable
- Apply the format specified by F (or Markdown if none)
- Save to `orchestrator-synthesis.md`

## Capabilities you have for this task
[Listed exactly as declared in Phase 2 — nothing more]

## Appendices — All agent briefs
[Full contents of every agent brief file, pasted in here so O has complete context
on what each agent has been asked to do]

### Appendix A: [Agent Name] Brief
[Full text of [agent-slug]-brief.md]

### Appendix B: [Agent Name] Brief
[Full text of [agent-slug]-brief.md]
...
```

### O's output files

| Output | File | When |
|--------|------|------|
| Dispatch plan | `orchestrator-dispatch.md` | Before any agent runs |
| Post-agent review notes | `orchestrator-review-[agent-slug].md` | After each agent |
| Final synthesis | `orchestrator-synthesis.md` | After all agents complete |

---

## Brief file naming

| Note entry              | Brief file name                  | Output file name                   |
|-------------------------|----------------------------------|------------------------------------|
| O Lead Strategist       | `lead-strategist-brief.md`       | `orchestrator-dispatch.md` + more  |
| O Project Manager       | `project-manager-brief.md`       | `orchestrator-dispatch.md` + more  |
| A Creative Director     | `creative-director-brief.md`     | `creative-director-output.md`      |
| A Research Analyst      | `research-analyst-brief.md`      | `research-analyst-output.md`       |
| A Copywriter + Editor   | `copywriter-editor-brief.md`     | `copywriter-editor-output.md`      |
| A Strategist + Research | `strategist-research-brief.md`   | `strategist-research-output.md`    |
| A Market Intelligence   | `market-intelligence-brief.md`   | `market-intelligence-output.md`    |

Rules:
- Lowercase, hyphen-separated, no special characters
- Keep compound roles joined with hyphens
- Max 40 chars before `-brief.md`
