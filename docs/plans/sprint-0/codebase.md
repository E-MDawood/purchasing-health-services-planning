# Codebase — Purchasing Health Services (Backend)

> Phase 1 output for Sprint 0. First run of the sprint-planning skill in this repo.
> **Repo type:** Backend (assumed. No code exists yet; Sprint 0 scope is API- and integration-heavy).
> **Explored:** 04 Oct 2026

## 1. Current state — greenfield

This repo has **no application code**. It contains only:

| Path | Content |
|---|---|
| `.claude/skills/` | Project skills (sprint-planning, estimation, forms, vitest, …) |
| `docs/stories/PALJ-*/UC_*.docx` | Use case documents per Jira story (16 UCs; source of truth for requirements) |
| `docs/brd/` | Content extracted from BRD v1.1: overview, roles, use-case map, process flow, reference data, diagrams (`flows/`), UI mockups (`ui/`) |
| `docs/plans/sprint-0/` | Planning files for this sprint |

No `*.sln`, `*.csproj`, `package.json`, `Dockerfile`, CI pipeline, database migrations, or config files exist.

## 2. Areas the Sprint 0 stories need — all NOT FOUND

| Area | Status |
|---|---|
| Solution / project structure (API, Application, Domain, Infrastructure layers) | NOT FOUND |
| API controllers / endpoints (create authorization, notifications to TPA/provider systems) | NOT FOUND |
| Domain model: Authorization, SubRequest, ServiceLine, Attachment, ActionLog, Beneficiary/identity | NOT FOUND |
| Enums: authorization status, sub-request status, eligibility status, verification status, ID type, access type/mode, encounter class, triage, document type, actor type | NOT FOUND |
| Numbering generators (`MA-YYYY-NNNNNNNN`, sub-request `-NN`, `SERVICE_LINE_NO`, `CNHI_ID`) | NOT FOUND |
| Integration clients: Yaqeen, eligibility engine, referral distribution system | NOT FOUND |
| Asynchronous retry / background job mechanism (verification retries, notification retries) | NOT FOUND |
| Outbound notification (webhook) client to provider/TPA systems, with ACK handling | NOT FOUND |
| Provider ↔ TPA assignment data (needed for routing) | NOT FOUND |
| Reference data: price list (by provider category), ICD-10 list, code lists | NOT FOUND |
| Integration-client registration and authentication (which provider/TPA system is calling) | NOT FOUND |
| Roles / permissions model | NOT FOUND |
| Persistence (database, ORM) | NOT FOUND |
| Attachment storage | NOT FOUND |

## 3. Related repos on disk (not explored — by user decision)

These sibling folders (next to this repo) look related, but were **not explored** for this sprint. The user chose to rely on the story data tables only.

| Folder | What it appears to be | Relevant to |
|---|---|---|
| `../backend` (`Eligibility.API.sln`) | Eligibility API | UC_001 table 001-03 (eligibility check) |
| `../ReferralsDist` (`Refferal_Api.sln`) | Referral distribution system | UC_001 table 001-06 (referral retrieval) |
| `../seha-application-platform` (`Physician.sln`, .NET, layered: API / Application / Domain / Infrastructure) | Existing Seha platform service | Possible reference for conventions and hosting on Seha |

## 4. Implication for planning

Everything in Sprint 0 is new build. The dev plan (Phase 4) must also decide:
- the tech stack and solution layout
- the persistence approach
- the background-job approach

These are technical decisions owned by the tech team, so record them as decisions rather than business questions.
