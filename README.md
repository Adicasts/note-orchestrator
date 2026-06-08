# N.O.T.E.
### Note Orchestration & Task Execution

> Write a note. Deploy a team of AI agents.

Write a shorthand letter-coded note — by hand or typed. Claude reads it, surfaces a full execution plan for your approval, then dispatches purpose-built specialist agents in order. Every agent gets its own brief file. You stay in control at every step.

---

## A note in 30 seconds

```
G  Launch Volta in London
C  Premium electric scooter brand
O  Lead Strategist
A  Research Analyst
A  Creative Director
B  Bold, minimal — not tech-bro
W  Review before creative phase
A  Copywriter
F  PDF launch brief
```

That 9-line note becomes:
- A full execution plan shown to you before anything runs
- A purpose-built orchestrator managing the agent team
- 3 specialist agent briefs created and dispatched in order
- A human checkpoint (W) pausing for your review mid-run
- A PDF at the end

---

## 19 letters. Infinite combinations.

| Layer | Letters | What they do |
|-------|---------|--------------|
| 🧭 Framing | `G` `C` `D` `N` | Goal, Context, Directives, Notes — read globally by every agent |
| ⚡ Execution | `O` `A` `T` | Orchestrator, Agent, Task — who does the work |
| 📦 Data | `R` `K` `I` `B` | Resource URLs, Knowledge base, Input data, Agent briefs |
| 🔀 Flow | `P` `W` `L` `S` | Priority, Wait checkpoint, Loop, Schedule |
| 📤 Output | `F` `E` `M` `V` | Format, Export destination, Memory save, Validate |

---

## How it works

**1. Write a note** — by hand (photograph it) or typed. Capital letter + space + content.

**2. Share it** — paste the note or upload the photo. Say "run my note" or "execute this."

**3. Review the plan** — Claude surfaces a full execution plan: every agent, every tool, every capability it will use. Nothing runs until you approve.

**4. Say "go"** — agents execute in order, pausing at any `W` checkpoints. Every agent gets a self-contained `.md` brief file. Final output in your chosen format.

---

## The Orchestrator (O)

Add an `O` line to create a purpose-built team lead. The Orchestrator is not a generic manager — it's built from your note's context and role name. It:

- Reads all agent briefs before dispatch
- Produces a dispatch plan with acceptance criteria per agent
- Reviews each agent's output before the next one starts
- Owns the final synthesis

Without `O`, Claude manages dispatch directly using note order.

---

## Install

N.O.T.E. is a skill for **[Claude Cowork](https://claude.ai)**.

1. Download `note-orchestrator.skill` from [Releases](../../releases)
2. Open Cowork → Settings → Capabilities → Install Skill
3. Drop the `.skill` file in

Once installed, share any letter-coded note and Claude will recognise it automatically.

---

## The interactive guide

📖 **[Read the guide](https://adicasts.github.io/note-orchestrator)** — a full interactive reference covering every letter, all 5 layers, worked examples, the O+A team model, and tips. Works on mobile.

---

## Files in this repo

```
SKILL.md                    ← Core skill definition (the brain)
CHANGELOG.md                ← Version history
guide/index.html            ← Interactive guide (also served as GitHub Pages)
references/
  alphabet.md               ← Full 19-letter reference
  examples.md               ← Two worked examples
agents/
  parser.md                 ← Handwritten + typed note parsing instructions
  dispatcher.md             ← Agent brief construction + capability declaration
```

---

## Writing your first note

Minimum viable note:

```
G  [Your goal]
A  [A specialist role]
T  [What you want produced]
```

Add more letters as you need them. The system is designed to be written fast — in a notebook, while you're thinking — and executed precisely.

---

## Version

**v1.2.0** — Orchestrator redesign. O is now a purpose-built agent with its own brief, not a meta-instruction. See [CHANGELOG](CHANGELOG.md) for full history.

---

*Built by [@Adicasts](https://github.com/Adicasts)
