# NOTE Alphabet — Complete Letter Reference

Every line in a NOTE note begins with a single capital letter followed by a space and
content. This document is the authoritative reference for what each letter means and how
to handle it.

---

## FRAMING LAYER
Read these first. They set the context for everything else.

### G — Goal
**What it is:** The north star. What this entire note is trying to achieve.  
**How it behaves:** There should be only one G per note. If multiple G items exist, treat
the first as the primary goal and the rest as sub-goals (fold them into T items).  
**If missing:** Infer from the T and A items. Flag your inference before executing.  
**Example:** `G Launch a London market presence for the brand by Q3`

---

### C — Context
**What it is:** Background information agents should absorb but not act on directly.
Product details, company background, existing constraints, relevant history.  
**How it behaves:** Include in every agent brief file. Agents read it as ambient knowledge.  
**Example:** `C Volta is a premium e-scooter brand targeting urban commuters aged 22–35`

---

### D — Directive
**What it is:** Rules, constraints, and non-negotiables that apply to ALL agents and tasks
in this note. Think of it as the brief's terms and conditions.  
**How it behaves:** Every agent brief file includes all D items. Agents must not violate
them. If a task would conflict with a D, flag it in the plan.  
**Example:** `D Instagram only — no other platforms` / `D Keep budget under $5,000`

---

### N — Note
**What it is:** A human annotation for reference only. Never executed. Logged but skipped
by all agents.  
**When to use it:** Reminders to self, questions to revisit, decisions pending approval.  
**Example:** `N Check with client before posting any campaign content`

---

## PEOPLE & EXECUTION LAYER
These define who does the work and what the work is.

### O — Orchestrator
**What it is:** A master coordinator agent that controls all A agents, decides their
execution order, and synthesises their outputs.  
**How it behaves:** If present, dispatch O first. O receives all agent briefs as context
and manages the run. In the absence of O, the main Claude instance acts as orchestrator.  
**Example:** `O Strategy lead` / `O Research + campaign architect`

---

### A — Agent
**What it is:** A specialist sub-agent to deploy. The text after A names the role.  
**How it behaves:** Each A gets its own `[slug]-brief.md` file before dispatch. Map the
named role to an available specialist (see `agents/dispatcher.md`). Run agents in note
order unless P items or O override this.  
**Multiple A items:** Deploy in order, or in parallel if they have no dependencies on each
other's output.  
**Example:** `A Creative Director` / `A Research Analyst` / `A Copywriter + Editor`

---

### T — Task
**What it is:** A concrete deliverable, step, or output to produce.  
**How it behaves:** T items without a preceding A are handled by Claude directly or the
most appropriate available agent. T items that follow an A are assigned to that agent.  
**Ordering:** T items run in the order they appear unless P items indicate otherwise.
Tasks with no dependency on earlier outputs can run in parallel.  
**Example:** `T Compile an intelligence report on city activation opportunities`

---

## DATA LAYER
These feed information into the execution.

### R — Resource
**What it is:** A specific input agents must use — a URL, file, dataset, Craft doc,
spreadsheet, or named tool.  
**How it behaves:** Include in every agent brief. If it's a URL, fetch it before agents
run and include a summary in the brief. If it's a file, reference its path.  
**Example:** `R volta.co` / `R brand-deck.pdf` / `R previous campaign report`

---

### K — Knowledge
**What it is:** A reference to the personal knowledge base. Pull from KB before executing.  
**How it behaves:** Trigger a KB query with the given topic/doc name. Include the result
in relevant agent briefs as background.  
**Example:** `K brand guidelines` / `K market research notes`

---

### I — Input
**What it is:** Explicit data, parameters, or content to pass directly to an agent or task.  
**How it behaves:** Different from R (which is a source to fetch) — I is the actual data
in the note itself. Treat it as a data parameter.  
**Example:** `I Budget: $20,000 total` / `I Target demographic: 25–35, urban professionals`

---

### B — Brief
**What it is:** A detailed spec or creative brief for the immediately following A or T.
Use when a short line isn't enough to describe the work.  
**How it behaves:** B lines are multi-line capable — continue indented lines as part of
the same B item. Append the B content directly to the relevant agent's brief file.  
**Example:**
```
A Copywriter
B Write 5 Instagram captions for the launch campaign.
  Tone: warm, confident. No clichés. Max 15 words per caption.
  Avoid: corporate language, generic lifestyle framing.
```

---

## FLOW CONTROL LAYER
These shape when and how things run.

### P — Priority
**What it is:** Sets execution order when the default (note order) isn't right.  
**How it behaves:** P1 runs before P2, which runs before P3. Named priorities
(e.g. `P Research before creative`) are interpreted as ordering instructions.  
**Example:** `P1 A Research Analyst` / `P Research before ideation`

---

### W — Wait
**What it is:** A human-in-the-loop checkpoint. Pause execution here and surface results
before proceeding to the next item.  
**How it behaves:** When execution reaches a W item, stop, present all outputs so far to
the user, explain what will happen next, and wait for explicit approval to continue.  
**Example:** `W Review research findings before creating content`

---

### L — Loop
**What it is:** Repeat the following A or T item N times or until a condition is met.  
**How it behaves:** `L 3` means run 3 times. `L until approved` means run until the user
approves the output. Each loop iteration saves its output to a numbered file.  
**Example:** `L 3 A Copywriter` — run 3 copywriting variations

---

### S — Schedule
**What it is:** Run this note (or the following T/A) at a future time or on a cadence.  
**How it behaves:** Triggers the scheduling system to create a repeating or one-off
scheduled task. The rest of the note becomes the scheduled payload.  
**Example:** `S Every Monday 9am` / `S 2026-06-15 10:00`

---

## OUTPUT LAYER
These control where results go.

### F — Format
**What it is:** The desired output format for the final deliverable.  
**How it behaves:** One F per note. If absent, default to Markdown. Applies to the final
synthesis output, not individual agent outputs (those are always .md).  
**Supported formats:** Report (PDF), Doc (DOCX), Slides (PPTX), Spreadsheet (XLSX),
Email draft, Craft doc, HTML, JSON, Plain markdown.  
**Example:** `F Intelligence report PDF` / `F Instagram carousel copy` / `F Craft doc`

---

### E — Export
**What it is:** The destination where the final output should be sent or saved.  
**How it behaves:** After synthesis, route output to the named destination. Supports:
Craft (saves as a new doc), Todoist (creates tasks), email (drafts a message), a specific
folder path, or "here" (just show in chat — the default).  
**Example:** `E Craft / Projects / Campaigns` / `E Todoist` / `E email draft to team`

---

### M — Memory
**What it is:** Save a summary of this note's outputs to the personal KB.  
**How it behaves:** After delivery, summarise key outputs and save to the KB with
appropriate tags. Use the G content as the document title.  
**Example:** `M` (bare — just save it) / `M tag: campaign, launch, research`

---

### V — Validate
**What it is:** Add a quality check step after an A or T completes before the next one
runs.  
**How it behaves:** Run a self-review pass on the output against the G goal and D
directives. Flag any gap or contradiction. Either auto-fix or surface to user if a W
checkpoint is nearby.  
**Example:** `V Check copy against brand directives before export`

---

## Quick-reference card

| Letter | Name        | Layer    | Run timing          |
|--------|-------------|----------|---------------------|
| G      | Goal        | Framing  | Parse only          |
| C      | Context     | Framing  | Parse only          |
| D      | Directive   | Framing  | Parse only          |
| N      | Note        | Framing  | Skipped             |
| O      | Orchestrator| Execution| First               |
| A      | Agent       | Execution| After O             |
| T      | Task        | Execution| With or after A     |
| R      | Resource    | Data     | Before agents       |
| K      | Knowledge   | Data     | Before agents       |
| I      | Input       | Data     | Attached to brief   |
| B      | Brief       | Data     | Attached to next A/T|
| P      | Priority    | Flow     | Reorders A/T        |
| W      | Wait        | Flow     | Pauses execution    |
| L      | Loop        | Flow     | Repeats A/T         |
| S      | Schedule    | Flow     | Defers execution    |
| F      | Format      | Output   | Final delivery      |
| E      | Export      | Output   | After delivery      |
| M      | Memory      | Output   | After delivery      |
| V      | Validate    | Output   | After A/T           |
