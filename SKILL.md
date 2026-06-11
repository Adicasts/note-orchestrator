---
name: note-orchestrator
description: >
  NOTE (Note Orchestration & Task Execution) — interprets handwritten or typed notes
  using a letter-prefix notation system and turns them into executed multi-agent workflows.
  Use this skill immediately whenever the user shares an image of a handwritten note with
  letter codes (A, T, G, O, D, F, etc.) at the start of lines, pastes a typed note using
  this notation, or says things like "run my note", "execute this note", "I've written a
  NOTE plan", "deploy agents on this", "here's my plan — go", "process this note",
  "I've got tasks written down", or sends any message with lines beginning with single
  capital letters followed by task/goal/agent descriptions. The skill parses the note,
  presents a structured execution plan for approval, then dispatches agents and executes
  tasks — creating individual .md brief files for every agent deployed. Do not attempt
  multi-agent note orchestration without this skill.
---

# NOTE — Note Orchestration & Task Execution

A skill that turns handwritten or typed shorthand notes into structured, executed
multi-agent workflows. The user writes a note using letter prefixes. The skill reads it,
surfaces a plan, waits for approval, then deploys agents and executes tasks in the
correct order — each agent getting its own .md brief file.

The notation is designed to be fast to write by hand and unambiguous to parse. Capital
letter + space + content. One item per line.

---

## Phase 0 — Input recognition

### If the input is an image (handwritten note)

Read the image directly — you are a multimodal model and can see the writing. Extract
every line that begins with a capital letter prefix. Tolerate imperfect handwriting:
infer the letter from context if ambiguous (a loopy "A" vs "O"). Surface your reading
of the note back to the user before proceeding so they can correct any misreads.

### If the input is typed text

Parse directly. Look for lines that follow the pattern `[LETTER] [content]` or
`[LETTER]: [content]`. Ignore lines with no letter prefix — treat them as loose
annotations.

---

## Phase 1 — Parse and structure

Extract and organise all items by their letter code, preserving the ORDER they appear in
the note (order matters for W positioning and B attachment):

1. **G** (Goal) — north star for everything else
2. **O** (Orchestrator) — if present, becomes a purpose-built orchestrator agent built
   FIRST from the raw note context. O receives the note ingredients (G, C, D, T, A list,
   W positions) and is responsible for building each A agent's brief, dispatching them,
   handling W checkpoints, reviewing outputs, and owning final synthesis.
3. **A** (Agent) — each becomes a purpose-built specialist
4. **T** (Task) — concrete deliverables or steps
5. Framing: **C** (Context), **D** (Directive), **R** (Resource), **K** (Knowledge),
   **N** (Note)
6. Flow: **P** (Priority), **W** (Wait), **L** (Loop), **S** (Schedule)
7. Brief: **B** (Brief), **I** (Input)
8. Output: **F** (Format), **E** (Export), **M** (Memory), **V** (Validate)

Read `references/alphabet.md` for full definitions.

### Pre-parse actions (before Phase 2)

**R items (Resources):** If an R item is a URL, fetch it via Diya (the user's browser)
before building the plan. This is a web search action — declare it in the plan's
capability block. Include a summary of the fetched content in every agent brief.

**K items (Knowledge):** Query the personal KB for the named topic or document. If the
KB is not connected or the item is not found, note this explicitly in the plan:
`⚠️ K: [item] — not found. Please provide this document, or say 'proceed without it'.`
Do not silently skip K items or hallucinate their content.

---

## Phase 2 — Present the execution plan

Before running anything, surface a structured plan. The user must be able to see exactly
what runs and what tools each agent uses — because they can't approve what they can't
see.

Use this format:

```
🎯 GOAL
[G content]

📋 EXECUTION PLAN
[If O is present, show it first as team lead, then list all A agents it manages]

🧠 O: [Orchestrator Name] — Team Lead
   Role: Briefed first from note context. Builds agent briefs, sets dispatch order,
         handles W checkpoints, reviews outputs, owns final synthesis.
   Capabilities: 🌐 [Yes/No] | 💻 [Yes/No — apps] | 🛠️ [files]
   Brief: orchestrator-brief.md (receives note context; builds agent briefs itself)

[Then list each A agent in note order, showing O's management relationship:]
1. A: [Agent Name]
   Task: [one sentence]
   Capabilities: 🌐 [Yes/No — via Diya] | 💻 [Yes/No — apps] | 🛠️ [files, bash, etc.]
   Managed by: O — output reviewed before next agent runs
   Depends on: [previous agent output or None]

⏸️ CHECKPOINT(S)
[Each W item shown at its exact position in the sequence — everything before it runs
first; everything after waits for approval]

⚙️ CONSTRAINTS & DIRECTIVES
[D, C, R items that shape the run]

📝 LOGGED (not executed)
[Any N items — surfaced here so the user can confirm they were seen and intentionally
skipped. E.g. "N: Double-check logo usage before sending"]

📤 OUTPUT
[F / E / M settings. If F requires a specific skill (PPTX → pptx skill, PDF → pdf
skill, DOCX → docx skill), note it here as a required capability.]

Does this look right? Say "go" to execute, or tell me what to change.
```

If there is no G, infer one and label it `Inferred goal — correct me if wrong.`
If there are no A items but there are T items, assign appropriate specialist agents
based on the task descriptions and explain your reasoning.

---

## Phase 3 — Execution

### 3a. Create brief files

Every A item becomes a purpose-built agent created fresh for this specific task.
Never route to existing plugins unless the user explicitly names one (e.g.
`A adops-suite Creative Director`). Otherwise, build from scratch.

#### If O is present: build the Orchestrator brief FIRST

O gets its own brief (`[o-slug]-brief.md`) built directly from the note context —
before any A agent briefs exist. O receives the note ingredients and builds each
agent's brief itself before dispatching them.

See `agents/dispatcher.md` for the O brief template.

**With O present, the execution flow becomes:**
1. Create O brief from note context (G, C, D, T, A list with B attachments, W positions,
   R/K/I, F/E/M/V)
2. Run O → O creates `[agent-slug]-brief.md` for each A item, then produces
   `orchestrator-dispatch.md` (ordered agent list + review criteria)
3. Execute A agents in the order O specifies
4. After each A completes, run O's review step: O reads the output, decides if it
   meets the bar, flags issues or approves
5. O synthesises all approved outputs at the end

If O is absent, Claude builds A briefs directly and manages dispatch using note order.

For each A item, create `[agent-slug]-brief.md`. Each brief is fully self-contained:

```markdown
# [Agent Name] — Purpose-Built Brief

## Who you are
You are a purpose-built [role name] agent created exclusively for this task.
You have no memory of previous sessions. Everything you need is in this brief.
[2–3 sentences defining the role clearly — what this person knows and does, what
they don't do. Prevents role overlap with other agents.]

## Goal
[The G from the note]

## Your specific task
[Concrete, specific deliverable — not "research the market" but "find the top 10 venues
in London where a premium consumer brand could run a pop-up in Q4 2026, with event
type, expected footfall, and a contact URL for each."]
[T items assigned to this agent, plus any B items attached to this A (see below)]

## Context
[All C items — full text, not summaries]

## Directives (non-negotiable)
[All D items — these cannot be overridden by any agent output]

## Resources
[All R items: URLs with fetched summaries, files to read]
[All K items: KB content retrieved, or flagged as missing]

## Input data
[Any I items — explicit parameters passed from the note]
[If this agent depends on another agent's output, reference the file:
"Read [agent-slug]-output.md for [brief description of what it contains]."]

## Capabilities you have for this task
[List exactly what was declared in Phase 2 — nothing more. The agent cannot use
anything not listed here. If a capability is missing mid-task, stop and ask the user.]
Examples:
- Web search via Diya (the user's browser)
- Read access to: [specific file path]
- Write access to: [specific path]
- Computer control of: [App 1, App 2]
- No external tools — training knowledge only. Label all outputs: [training knowledge
  — verify before using]

## Output format
[F item if applicable, or detailed Markdown if not specified]

## Where to save your output
`[agent-slug]-output.md` in the working directory
```

**B item attachment rule:** B attaches to the immediately preceding A item in the note.
If a B has no A before it, treat it as a T item (a specific task Claude handles
directly). Never attach B to the next A — only to the one directly above it.

### 3b. Dispatch order

**W items are positional — they interrupt execution at the exact point they appear in
the note.** Everything listed before the W runs first. At the W checkpoint:
1. Summarise each agent's output in 2–3 sentences
2. Show output file names and key findings
3. State what will run next after approval
4. Wait. Do not proceed until the user confirms.

**Without O — Claude-managed dispatch:**

1. Pre-run: fetch R URLs via Diya, query K from KB
2. A items: dispatch in note order
3. W checkpoints: pause at each W in its note position
4. Unassigned T items: synthesise after all agents complete
5. V validation, then F / E / M

**With O — orchestrator-managed dispatch:**

1. Pre-run: fetch R URLs via Diya, query K from KB
2. Build O brief from note context (G, C, D, T, A list, W positions, R/K/I, F/E/M/V)
3. Run O → O creates agent briefs and produces `orchestrator-dispatch.md`
4. Execute each A agent per O's plan
5. After each A completes: run O's review step
   - O reads the output against its declared review criteria
   - O approves (proceed) or flags issues (report to user before continuing)
6. W checkpoints apply at their positional note location — O surfaces the checkpoint
   summary to the user
7. O owns synthesis
8. V validation, then F / E / M

### 3c. Agent output files

After each agent completes, save its primary output to `[agent-slug]-output.md`.

---

## Phase 4 — Synthesis and delivery

**Without O:** Claude reads all `*-output.md` files and synthesises directly.

**With O:** O owns synthesis. Run O's synthesis step — it reads all approved agent
outputs and produces `orchestrator-synthesis.md`. Claude then takes that file as the
deliverable.

In both cases:
1. Apply the format specified by F (Markdown if none specified)
2. If F requires a specific skill (PPTX, PDF, DOCX), invoke that skill on the
   synthesised content
3. If E (Export) is set, route to the specified destination
4. If M (Memory) is set, save a summary to the personal KB / Craft
5. Present the final output with a one-line note on what each agent contributed

---

## Handling ambiguity

- **Multiple G items** — use the first as the primary goal; treat others as sub-goals
  added as T items
- **Missing G** — infer from T/A items and flag clearly
- **Unknown agent names** — build a purpose-built agent; explain how you've defined the
  role in the plan
- **Conflicting D items** — flag the conflict in Phase 2 and ask for resolution before
  proceeding
- **Illegible handwriting** — transcribe what you can, mark unclear items with `[?]`,
  ask for confirmation before executing
- **Agent needs an undeclared capability** — stop. State what's needed and ask before
  proceeding. Never silently use capabilities that weren't in the approved plan.
- **K not found** — see Phase 1 K rule above
- **R fetch fails** — note in plan, ask user to paste content manually or skip

---

## Reference files

- `references/alphabet.md` — Full letter reference with definitions and examples
- `references/examples.md` — Worked examples
- `agents/parser.md` — OCR and parsing instructions for handwritten input
- `agents/dispatcher.md` — How to build purpose-built agent briefs and declare
  capabilities
