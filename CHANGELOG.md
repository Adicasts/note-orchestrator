# NOTE Orchestrator — Changelog

---

## v1.2.0 — Orchestrator redesign
**Date:** 2026-06-08

### Changed
- **O is now a purpose-built agent, not a persona.** Previous versions incorrectly
  described O as "a role Claude adopts" for orchestration decisions. O is now built
  exactly like an A agent — with its own `[o-slug]-brief.md` — but operates at the
  team management layer above the specialist agents.

### Added
- **O brief template** in `agents/dispatcher.md` — full template covering: who O is,
  goal, context, directives O enforces, team roster with brief file references, and
  three explicit responsibilities: dispatch planning, output review, and synthesis.
- **O dispatch flow** in `SKILL.md` Phase 3b — when O is present, O produces
  `orchestrator-dispatch.md` before any A agent runs, specifying ordered sequence
  and acceptance criteria per agent.
- **O review step** — after each A agent completes, O reviews the output against its
  declared acceptance criteria before the next agent starts. If output falls short,
  O flags it and pauses for user input rather than silently carrying weak output forward.
- **O synthesis ownership** — O produces `orchestrator-synthesis.md` as the final
  deliverable. Phase 4 now splits clearly: with O → O synthesises; without O → Claude
  synthesises directly.
- **O in Phase 2 plan format** — plan now shows O as the team lead at the top with its
  own capability block, before listing A agents with "Managed by: O" notation.
- **O output files** documented in dispatcher.md:
  `orchestrator-dispatch.md`, `orchestrator-review-[agent].md`,
  `orchestrator-synthesis.md`
- **O brief includes all A briefs as appendices** — O gets complete visibility of
  every agent's full instructions before it plans the run.

### Build order clarified
All A briefs must be created before the O brief is built — O needs the full team
context to write its appendices and dispatch plan.

---

## v1.1.0 — Iteration 1: eval-driven fixes
**Date:** 2026-06-08

### Fixed (11 issues from 3 eval runs)

1. **W positioning** — W was listed as "step 3 after all agents" in Phase 3b dispatch
   order. W is positional: it interrupts execution at the exact point it appears in the
   note. Everything before W runs first; everything after waits for user approval.

2. **K fallback** — Skill assumed personal KB MCP is always connected. Now: if KB is
   not connected or the item is not found, the plan surfaces `⚠️ K: [item] — not found`
   and pauses rather than silently skipping or hallucinating content.

3. **R fetching tool** — Skill said "fetch URL before agents run" but didn't specify
   how. Now explicit: R URLs are fetched via Diya (the user's browser) before dispatch,
   treated as a web search action and declared in the plan's capability block.

4. **O framing** — O was described as "a persona Claude adopts". Corrected to a
   purpose-built agent (fully resolved in v1.2.0).

5. **B attachment** — B was described as attaching to "the preceding or following A".
   Rule tightened: B attaches to the immediately preceding A only. If B has no
   preceding A, it becomes a T item.

6. **Unassigned T handler** — "Claude directly" was vague. Now: unassigned T items
   (no preceding A) are synthesised by Claude after all agents complete, as an explicit
   step in the plan.

7. **W checkpoint format** — Skill said "pause and surface results" without specifying
   what. Now: at each W, Claude surfaces a 2–3 sentence summary per agent, output file
   names + key findings, and what runs next. No ambiguity about what the user sees.

8. **N item logging** — N items were silently skipped with no user confirmation. Now:
   N items appear in the plan under `📝 Logged (not executed): [N content]` so the
   user can see they were intentionally excluded.

9. **F = PPTX capability** — F items requiring specific skills (PPTX, PDF, DOCX) are
   now declared as required capabilities in the Phase 2 plan, not just treated as
   labels.

10. **All-caps "REQUIRED in every plan"** — Replaced with a WHY explanation: "because
    you can't approve what you can't see — every agent's capabilities must be declared
    so you know exactly what runs on your system."

11. **Verbose sections** — Phase 4 and "Handling ambiguity" sections tightened.

### Added
- `agents/dispatcher.md` — B item attachment example with visual annotation
- Phase 2 plan format updated to include W in its positional sequence
- `📝 Logged (not executed)` section to the Phase 2 plan template

---

## v1.0.0 — Initial build
**Date:** 2026-06-08

### Added
- `SKILL.md` — Core skill with 4-phase execution (input recognition, plan, execute,
  synthesise)
- `references/alphabet.md` — Full 19-letter notation system (G, A, T, O, D, C, N, F,
  R, K, I, B, P, W, L, S, E, M, V) across 5 layers: Framing, Execution, Data, Flow,
  Output
- `references/examples.md` — Two worked examples: city launch market research note
  and "Make the Switch" SaaS campaign note
- `agents/parser.md` — Handwritten image parsing (multimodal) and typed text parsing
  with edge cases, confidence labelling, and legend override
- `agents/dispatcher.md` — Purpose-built agent construction rules: always build fresh,
  never route to existing plugins unless user explicitly names one; capability
  declaration format and assessment checklist
- `evals/evals.json` — 3 test cases covering image input, full typed alphabet, and
  complex O/K/B/N/R note

### Design decisions
- Handwritten input parsed directly via Claude multimodal — no external OCR
- Every A agent gets a fully self-contained `.md` brief before dispatch
- Capability transparency enforced: web search (Diya), computer control (app list),
  file access, and training-knowledge-only all declared in Phase 2 plan before "go"
- Purpose-built agents only — existing plugins blocked unless user explicitly names
  them in the note
