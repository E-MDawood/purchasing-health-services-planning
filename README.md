# Purchasing Health Services — Planning

> **This repository is for the planning phase only.** It holds requirements, analysis and sprint planning documents for the Purchasing Health Services platform (CNHI medical authorizations). It contains **no application code**. The backend and frontend will live in their own repositories.

## What the project is

A platform for CNHI (National Center for Health Insurance) that manages medical authorizations between private healthcare providers and the TPAs (third-party administrators) assigned to them. Providers and TPAs integrate their own systems with the platform's APIs; CNHI, provider and TPA staff use screens on the Seha platform. The platform integrates with Yaqeen, the eligibility engine, the referral distribution system and the claims service.

Jira project: **PALJ**.

## Repository layout

| Path | Content |
|---|---|
| `docs/stories/PALJ-*/` | Use-case documents (`UC_001` … `UC_016`), one folder per Jira story. Source of truth for requirements. |
| `docs/brd/` | Content extracted from the BRD v1.1: overview and scope, roles and permissions, use-case map, business process flow, reference data (statuses, transitions, SLAs, code lists, numbering), diagrams (`flows/`) and UI mockups (`ui/`). |
| `docs/plans/sprint-<N>/` | Sprint planning files produced with the `sprint-planning` skill (see below). |
| `.claude/skills/` | Claude Code skills used for planning and later delivery (sprint planning, estimation, HLD, etc.). |

### Sprint planning files

Each sprint folder follows the `sprint-planning` skill's phases:

| File | Purpose |
|---|---|
| `codebase.md` | State of the code the sprint builds on (greenfield for Sprint 0). |
| `stories.md` | The sprint's stories, copied verbatim. |
| `analysis-notes.md` | Internal scope judgment, resolved decisions and working assumptions. |
| `business-questions.md` | Open questions for the business. Written to be shared outside the engineering team. |
| `brd-context-and-business-gaps.md` | BRD context and the gap history between the BRD, the stories and the project plan. |
| `brd.docx` | BRD v1.1 (source document). |

Later phases add `stories-plan.md` (development plan), API contract files and `tasks.md`.

## Current status

- **Sprint 0** (30 Sep – 15 Oct 2026): stories PALJ-2 (UC_001 Submit authorization) and PALJ-18 (UC_014 Action log). Clarification round in progress; see `docs/plans/sprint-0/business-questions.md`.

## Getting oriented

Using Claude Code? Run `/project-context` (optionally with a sprint number, e.g. `/project-context 1`) in a fresh session. It reads the planning files in order, reports where planning stands, and continues from there.

New to the project? Start with `docs/brd/README.md`, then `docs/brd/use-case-map.md` and `docs/brd/reference-data.md`. Open questions and the assumptions we are working with are in `docs/plans/sprint-0/`.
