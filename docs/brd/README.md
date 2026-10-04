# BRD — Medical Authorizations (extracted)

Content extracted from the detailed BRD **"إدارة موافقات ومطالبات العلاج داخل المملكة – إدارة طلبات الموافقة"** (CNHI, Technology & AI Sector), **version 1.1, 28 Sep 2026**. Source file: `docs/plans/sprint-0/brd.docx`.

## Relation to the use-case files

The 16 use cases in the BRD (UC_001 to UC_016, with their data tables) were compared table row by table row against `docs/stories/PALJ-*/UC_*.docx`: **they are identical**. The use cases are therefore not repeated here; read them from the story files (or the sprint's `stories.md`).

This folder holds only what the BRD has **in addition** to the use cases:

| File / folder | Content | BRD section |
|---|---|---|
| [overview.md](overview.md) | Purpose, in scope, out of scope, glossary, assumptions and dependencies, risks, document history and approvals | نظرة عامة على الوثيقة |
| [roles-and-permissions.md](roles-and-permissions.md) | User permissions per party, action × authorized party matrix | صلاحيات المستخدمين، مصفوفة الصلاحيات |
| [use-case-map.md](use-case-map.md) | The 4 feature groups, the 16 use cases in each, executing and affected parties, Jira keys | حالات الاستخدام (diagram) |
| [process-flow.md](process-flow.md) | End-to-end business process (swimlanes: provider, system, TPA) as text | إجراء العمل (diagram) |
| [reference-data.md](reference-data.md) | Status lists, status transitions, SLAs, all reference code lists, attachment rules, numbering formats | انتقالات حالة الطلب الفرعي، الملحقات |
| [flows/](flows/) | Original diagrams: process flow (`.png`, `.vsdx` original, `.drawio` editable), use-case map (`.png`) | — |
| [ui/](ui/README.md) | UI mockups and printed forms per use case (UC_008, UC_010 to UC_015) | صور توضيحية للنظام "Prototypes" و نماذج مطبوعة "Forms" |

## Precedence

- Use cases and data tables: the story files are the source of truth (identical to the BRD today).
- Everything in this folder: the BRD is the only source. Where it conflicts with a use case, the conflict is logged in `docs/plans/sprint-0/business-questions.md` and `docs/plans/sprint-0/brd-context-and-business-gaps.md`.
- The text in these files is an English rendering of the Arabic BRD; codes, field names and values are kept exactly as written. Arabic labels are kept next to statuses and lists for reference.
