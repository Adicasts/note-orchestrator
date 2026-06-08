# Parser Agent — Handwritten & Typed Note Parsing

This document gives detailed instructions for reading and parsing NOTE-format inputs,
whether they come as images of handwritten notes or as typed text.

---

## Reading handwritten images

You are a multimodal model — you can see the image directly without external OCR tools.
When the user shares a photo of a handwritten note:

1. **Read the full image** before parsing. Take in all text, diagrams, arrows, and
   marginal annotations.

2. **Identify the header** — look for a date, project name, or title at the top. This
   gives useful context for the G (Goal) if one isn't explicit.

3. **Extract every line** that begins with a capital letter. For each line, record:
   - The prefix letter (or your best inference if handwriting is ambiguous)
   - The content after the letter
   - Your confidence: HIGH / MEDIUM / LOW

4. **Handle ambiguous letters** with this hierarchy:
   - If a line looks like an action (verb-led): probably T
   - If a line names a person/role: probably A
   - If a line describes why/what we're doing: probably G or C
   - If the letter is unclear but the content is a constraint: probably D
   - When truly unsure, use the most common letter for the content type and annotate
     with `[inferred]`

5. **Capture the legend** — many notes include a legend at the bottom
   (e.g. "A = Agent | T = Task"). If present, use it to override your letter mappings.
   The user's own legend always takes precedence over the default alphabet.

6. **After extraction**, present your reading back to the user:

```
I read your note as:

G: [content]
C: [content]
A: [content]
A: [content]
T: [content]
...

[N items — skipping in execution]

Does this match what you wrote? Correct anything before I proceed.
```

---

## Reading typed text

When the user types a note in the NOTE format:

1. Split by newline
2. For each line, extract the first non-space character
3. If it's a capital letter and is followed by a space: it's a NOTE item
4. If the line begins with lowercase, is blank, or is indented: treat as a continuation
   of the previous item (relevant for multi-line B items)
5. Inline annotations in parentheses are N (Note) items

### Flexible syntax forms — all are valid

```
G Build a London market presence          ← space separator
G: Build a London market presence         ← colon separator
[G] Build a London market presence        ← bracket form
G — Build a London market presence        ← dash separator
```

Accept all of these. The letter is what matters, not the punctuation.

---

## Edge cases

### No G present
Infer the goal from the cumulative T and A items. Name it clearly and flag:
`"No goal found — I'm treating this as: [inferred goal]. Correct me before proceeding."`

### Legend overrides
If the user defines their own letters (e.g. "S = Strategy, not Schedule"), respect them.
User-defined legends always override the default alphabet.

### Mixed notation
Some notes mix NOTE syntax with free prose. Parse NOTE items normally, collect prose lines
as C (Context) items, and flag them in the plan presentation.

### Numbered items without letters
If a note has numbered tasks (1. 2. 3.) without letter codes, treat each as a T item.

### Strikethrough / crossed out items
If handwritten items are visibly crossed out or marked done, skip them from execution.
Note to user that you've skipped them.
