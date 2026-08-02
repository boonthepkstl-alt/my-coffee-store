---
name: requirement-to-backlog
description: Turn a raw, unstructured requirement from the user into a Thai requirement spec document under docs/01-requirements/01-spec/, then break it into backlog items in docs/01-requirements/backlog.md and record the work in a daily log. Use when the user describes a feature, need, problem, or change in loose terms and wants it captured as a proper requirement or backlog; also when asked to update, extend, or supersede an existing spec. Triggers on "requirement", "สเปค", "ความต้องการ", "backlog", "อยากได้ฟีเจอร์", "เพิ่มฟีเจอร์".
---

# Requirement → Spec → Backlog

Convert raw user input into three durable artifacts, in this order. Do not skip
step 2: guessing at an ambiguous requirement is the main failure mode here.

1. `docs/01-requirements/01-spec/{YYYYMMDD}-{NN}-{topic-slug}.md` — the spec
2. `docs/01-requirements/backlog.md` — backlog rows pointing at that spec
3. `docs/05-log/{YYYYMMDD}-log.md` — what was done and why

## Non-negotiable repo conventions

Read `CLAUDE.md` if you have not. In short:

- **All document prose is Thai.** Headings, tables, and body text. Only
  filenames, slugs, and IDs are ASCII.
- **Links inside `docs/` are Obsidian wikilinks ending at a page**, with an
  explicit label: `[[../01-spec/20260802-01-online-ordering|20260802-01]]`.
- **Never delete a document.** Superseded specs move to `docs/00-archived/`.
- Every new folder needs an `index.md`.

## Step 0 — Resolve the filename

```bash
ls docs/01-requirements/01-spec/
```

- `YYYYMMDD` — today, from `date +%Y%m%d`. Never hardcode a date.
- `NN` — running number **scoped to that date**, zero-padded to 2. Find the
  highest `NN` among files already named `{YYYYMMDD}-*` and add 1. First spec
  of the day is `01`.
- `topic-slug` — short ASCII kebab-case, 2–5 words, describing the topic
  (`online-ordering`, `member-points`). ASCII keeps paths safe for git and
  CLI; the Thai title lives in the document's `title` field.

## Step 1 — Check for an existing spec first

If the user references an existing requirement, or the topic looks like one
already covered, **decide explicitly whether to write a new document or amend
an existing one** rather than defaulting to new.

```bash
ls docs/01-requirements/01-spec/ && grep -ril "<keyword>" docs/01-requirements/
```

| Situation | Action |
| --- | --- |
| Adds detail to a requirement that still holds | Edit the existing spec in place, bump `updated` |
| Changes or reverses a decision in an existing spec | New spec; set `supersedes:`; move the old one to `docs/00-archived/` and set its `status: superseded` |
| Related but separately deliverable | New spec; cross-link both via `related:` |
| Genuinely unrelated | New spec |

State which case applies and why before writing. If the evidence is thin, that
is a question for Step 2 — do not guess.

## Step 2 — Ask before assuming

Any point where the requirement could reasonably be read more than one way is a
question. Scope boundaries, who the user is, what happens in failure cases,
whether something is in this release — these are almost never stated in raw
input.

Use `AskUserQuestion`, and obey both of these:

- **Every question offers at least 3 concrete approaches**, not yes/no. Each
  option needs a `description` explaining the trade-off and consequence — the
  user is choosing a direction, so give them enough to choose on.
- **Lead with a recommendation.** Put the option you would pick first and mark
  its label `(แนะนำ)`.

Tool limits: 4 options per question maximum, 4 questions per call. "อื่น ๆ" is
added automatically — never write your own.

Batch related questions into one call rather than interrogating one at a time.
Write questions and options in Thai.

If a point stays unresolved after asking, record it under
`## คำถามที่ยังค้าง` in the spec rather than inventing an answer.

## Step 3 — Write the spec

Use `references/spec-template.md` verbatim as the structure. Fill every
section; write `— ยังไม่ระบุ —` where information is genuinely missing, so gaps
are visible instead of silently absent.

Acceptance criteria must be checkable — a tester should be able to mark each
one pass or fail without asking what it meant.

## Step 4 — Update the backlog

`docs/01-requirements/backlog.md` — see `references/backlog-format.md`. Create
it from that template if it does not exist yet.

Each spec produces one or more rows. Backlog IDs are `BL-###`, globally
sequential and **never reused**, including for removed items. Read the whole
file first to find the current maximum.

Rows are the deliverable slices of the spec, not a copy of its sections. If a
spec yields exactly one row, that spec is probably too small or the breakdown
was skipped — check.

## Step 5 — Log it

Append to `docs/05-log/{YYYYMMDD}-log.md`, creating it if absent (template in
`references/backlog-format.md`). Append; never overwrite an earlier entry.

Record the **reasoning**, not just the file list — specifically the Step 1
decision, what the user chose in Step 2, and anything left open. Per
`docs/05-log/index.md` this is the record used later to reconstruct why a
choice was made.

## Step 6 — Report back

Tell the user, in Thai: the spec path, the backlog IDs added, the log path, the
questions asked and what they picked, and anything still unresolved. Do not
claim the requirement is complete while `คำถามที่ยังค้าง` still has entries —
say what is outstanding.

## Delegating

For a large requirement, or several at once, hand the analysis and drafting to
the `requirement-analyst` subagent. It cannot talk to the user, so it returns
clarifying questions instead of asking them; you ask via `AskUserQuestion`,
then send the answers back with `SendMessage`.
