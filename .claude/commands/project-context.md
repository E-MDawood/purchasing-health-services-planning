---
description: Load the full project context for Purchasing Health Services planning and continue business planning from where it stopped
argument-hint: "[sprint number, optional — defaults to the latest sprint folder]"
---

# Load project context and continue planning

You are joining the **planning phase** of the Purchasing Health Services platform (CNHI medical authorizations, Jira project **PALJ**). This repository holds requirements and planning documents only; there is no application code here. Your job is to rebuild a working understanding of the project from the files, report where planning stands, and continue business planning from there.

Sprint requested: `$ARGUMENTS` (if empty, use the highest-numbered folder under `docs/plans/`).

## Step 1 — Read, in this order

Read these files fully before saying anything about the project. Do not skim, and do not rely on memory from other conversations.

1. `README.md` — what this repo is and how it is laid out.
2. `.claude/skills/sprint-planning/SKILL.md` and the reference file for the current phase under `.claude/skills/sprint-planning/references/` — this is the process we follow. Read `clarification-round.md` in any case: it defines the writing rules for `business-questions.md`.
3. BRD extraction, `docs/brd/`:
   - `README.md` (how the BRD relates to the use cases)
   - `overview.md` (scope, out of scope, glossary, assumptions, risks)
   - `use-case-map.md` (4 feature groups, 16 use cases, parties, Jira keys)
   - `roles-and-permissions.md`
   - `reference-data.md` (statuses, transitions, SLAs, code lists, attachment rules, numbering)
   - `process-flow.md` (note: the diagram is outdated, the file says how)
   - `ui/README.md` (index of mockups; open an image only when a question needs it)
4. The sprint folder `docs/plans/sprint-<N>/`, in this order:
   - `codebase.md`
   - `brd-context-and-business-gaps.md` (sources, use-case list with planned sprints, gap status per plan item, open items, change log)
   - `analysis-notes.md` (scope, decisions D1…Dn, working assumptions)
   - `business-questions.md` (open questions for the business)
   - `stories.md` (verbatim stories; long and in Arabic, read the sections relevant to the current work)
   - any later-phase files that exist: `stories-plan.md` / `<feature>-plan.md`, `*-api-changes.md`, `api-changes-inconsistencies.md`, `tasks.md`, `batches/`
5. Earlier sprint folders only if the current sprint's files point to them.

The original sources are `docs/stories/PALJ-*/UC_*.docx` (use cases) and `docs/plans/sprint-0/brd.docx` (BRD v1.1). The markdown files above already extract them; open the docx files only to check a specific detail (use `pandoc` or `python-docx`).

## Step 2 — Report back

Reply with a short briefing, no longer than one screen:

- **The project in 3–4 sentences.**
- **Current sprint and phase**: which `sprint-planning` phase the sprint is in, worked out from which files exist (per the phase table in `SKILL.md`), and a one-line summary of what exists.
- **Open business questions**: the count per story, and the 3–5 that block the most.
- **Decisions and assumptions** the next step depends on.
- **Proposed next step**, and the question you need the user to answer before taking it (for example, proceed to Phase 4 on the working assumptions, run another clarification round, or ask whether delivery should be batched).

Then stop and wait for the user.

## Rules to follow while continuing

These are standing preferences from earlier planning sessions:

- **Source of truth:** the use-case files in `docs/stories/` win. BRD v1.1 has identical use-case tables; its overview, permissions, appendices and diagrams (in `docs/brd/`) fill in what the stories leave out. When the two conflict, the stories win and the conflict is raised as a question.
- **Log every problem you find.** Any inconsistency, contradiction or gap in the requirements must be added to `business-questions.md` (as a hedged question with our working assumption) **and** to `brd-context-and-business-gaps.md` (a gap row, an open item and a change-log line). Mirror the working assumption in the assumptions table in `analysis-notes.md` so planning can proceed. Don't just mention it in chat, and don't quietly resolve it as an engineering decision.
- **When an answer arrives**, move the item from `business-questions.md` to a decision in `analysis-notes.md`, update its row in the gaps file, and renumber the questions, updating every reference (for example `PALJ-2 Q5` in the assumptions table).
- **`business-questions.md` is shared with the business:** no em dashes, no file names or paths, no checkboxes, hedged wording ("we are assuming"), one `##` section per story.
- **Paths are always relative** to the repo root (`docs/...`, or `../<sibling-repo>`), never absolute machine paths.
- **Technical choices** (retry schedules, rate limits, storage, the CNHI_ID algorithm, stack) are tech-owned decisions for the dev plan, not business questions.
- **Jira:** the Atlassian connector has returned `403 app not installed` for `leansa.atlassian.net`. Try it once if needed; if it still fails, use the docx files and ask the user to paste anything else (subtasks, comments).
- Use the `sprint-planning` skill for the planning work itself and the `estimation` skill for time estimates.
