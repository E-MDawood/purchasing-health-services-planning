# Sprint 0 — Analysis Notes

Internal file: our own scope judgment and the decisions resolved so far. None of this needs business-owner input.

Related files:
- `stories.md` — raw story text
- `business-questions.md` — items waiting on the business (shareable)
- `codebase.md` — code state: greenfield
- `brd-context-and-business-gaps.md` — BRD and gap history
- `docs/brd/` — content extracted from BRD v1.1: overview, roles, use-case map, process flow, reference data (statuses, transitions, SLAs, code lists, attachment rules, numbering), UI mockups

---

## 1. How this sprint is scoped

- **Greenfield backend.** No code exists (`codebase.md` §1). Every item in Sprint 0 is new build, including the solution skeleton, persistence and the background-job mechanism.
- **Almost every endpoint is inbound.** The provider and TPA *systems* call our API (create authorization, table 001-01). We call **out** to:
  - Yaqeen (001-02)
  - the eligibility engine (001-03)
  - the referral distribution system (001-06)
  - the TPA system — notification of a new request (001-04)
  - the provider system — notification of an advanced authorization (001-05)

  There are no portal screens in Sprint 0.
- **Stories in this sprint:**
  - **PALJ-2** (UC_001 Create authorization) — the core of the sprint.
  - **PALJ-18** (UC_014 Action Log) — **write side only**.
- **Foundation items** from the project plan, all exercised by UC_001:
  - Yaqeen
  - eligibility
  - **referral (added)**
  - TPA routing
  - notification framework
  - attachments
  - audit log
  - numbering
  - duplicate check
  - status engine
  - roles

  They are not separate Jira stories.
- **The story files in `docs/stories/` are the source of truth for use cases.** BRD v1.1 (`brd.docx`, 28 Sep 2026) has use-case tables identical to the story files, row for row. Its overview, permissions, appendices and diagrams (extracted to `docs/brd/`) are the source for everything the stories leave out. The BA feedback document is reference only. Where the BRD's extra sections conflict with the stories, the stories win and the conflict is raised as a question.
- **Async-verification pattern** is shared by Yaqeen (ALT006, BR006), eligibility (ALT007, BR008) and referral (ALT012, BR013):
  - accept the request
  - set the flag to `PENDING_VERIFICATION`
  - retry in the background
  - set the flag to `VERIFIED` / `NOT_VERIFIED`
  - log the result and notify

  Build it **once** as a reusable mechanism.

## 2. Impact per story (backend repo)

| Story | Impact | Basis |
|---|---|---|
| PALJ-2 UC_001 | **Large.** One inbound API, 3 outbound integrations (async), 2 outbound notifications, validation rules, numbering, CNHI_ID generator, duplicate and previous-month logic, VAT calculation check, attachments. | Story tables 001-01 to 001-06; `codebase.md` §2 (all NOT FOUND). |
| PALJ-18 UC_014 (write side) | **Medium.** Immutable log model, recording inside the same transaction as each action, actor/channel capture, attachment snapshot, before/after status. Only Sprint 0 actions are recorded now; the remaining actions are added by later sprints. | UC_014 BR001–BR007, BR009–BR010; decision D2. |

## 3. Decisions resolved

- **D1 — Referral integration is in Sprint 0** (user decision, 04 Oct). Build the 001-06 client with the async-verification pattern (ALT011 → MSG007 when not found; ALT012 when the system is down). It's not in the original project plan, so the estimate must include it.
- **D2 — Action Log: write side only in Sprint 0** (user decision). Screens (table 014-01, screens 1–3) belong to the Sprint 4 "Action Log Development" row. Sprint 0 records these actions:
  - authorization created
  - sent to TPA
  - sub-request created
  - Yaqeen data mismatch (BR005)
  - verification flag updates (ALT006, ALT007, ALT012)
- **D3 — Sub-request status enum includes `RETURN_REQUESTED`.** UC_006 defines it, and the BRD's sub-request status appendix and transition table include it ("Under Review – Return Requested by Provider", no code given; we use UC_006's `RETURN_REQUESTED`). The status lists in 001-01, 003-02 and 014-01 still leave it out; that's a documentation gap, it doesn't block us.
- **D4 — Ignore `docs/stories/PALJ-5/إنشاء طلب موافقة طبية.docx`.** It is an older draft of 001-01 (no `AUTHORIZATION_MONTH`, filed under the wrong ticket). UC_001 is authoritative.
- **D5 — CNHI_ID format** = `CNHI` + YY + 8 digits, no separators (UC_001 BR007). The BA feedback's `CNHI-YYYY-NNNNNNNN` is superseded. The algorithm (no duplicates, includes a check value, not sequential and not random) is a **tech decision**, to be designed in Phase 4.
- **D6 — Month logic uses `AUTHORIZATION_MONTH`, not the creation date** (BR002, BR004, BR014):
  - only the current or next month is accepted (ALT013 → MSG008)
  - the validity end is the end of that month, or the discharge date
- **D7 — Tech-owned parameters**, not business questions:
  - retry schedule for Yaqeen, eligibility and referral (BR006)
  - notification retry count and interval (BR009)
  - rate limits (UC_002 BR005, UC_016)
  - storage technology for attachments (the limits themselves are business-defined, see D9)

  Decide them in Phase 4.
- **D8 — Notification delivery** (BR009): retry, then mark "Delivery Failed" with no status change. A per-notification delivery record is needed so Sprint 4 screens can show it.

- **D9 — Attachment rules** (BRD appendix "قواعد المرفقات"; closes the old attachment question): PDF, JPG, PNG; max 5 MB per file; max 10 files per action; retained as long as the action log; applies to 001-01, 003-01, 004-01, 005-01 and their notifications; Base64 with document type and file name. Keep the values configurable.
- **D10 — Status engine source** (BRD appendices): main statuses `ACTIVE` / `COMPLETED` / `CANCELLED`; sub-request statuses and both transition tables are in `docs/brd/reference-data.md` §1–§4. End of validity is a flag/date, **not** a status change. A cancelled sub-request is never restored on reopen. Build the status engine from these tables; the business process diagram is outdated (raised as PALJ-2 Q13).
- **D11 — Numbering, SLAs and code lists** come from `docs/brd/reference-data.md` (§5, §6, §8). The old "missing appendices" question is closed. The only SLA still undefined is the TPA decision deadline (Sprint 1).
- **D12 — Roles** come from `docs/brd/roles-and-permissions.md`. Where the permissions list and the action matrix disagree (printing, reassign), we follow the **action matrix** until the BA answers (logged in the gaps file); none of these actions is in Sprint 0.

## 4. Working assumptions (until the business answers, see `business-questions.md`)

| Topic | Assumption used for planning | Question ref |
|---|---|---|
| TPA routing | Exactly one TPA per provider, held as reference data loaded by tech. No admin screen. Error MSG005 when the provider has no assigned TPA. | PALJ-2 Q1 |
| Access fields | Implement 001-01 as written (`ACCESS_TYPE` / `ACCESS_MODE`, the BRD reference list confirms the new codes). Accept all three access types from both provider and TPA systems. Keep the outbound 001-04 field mapping isolated so it's cheap to change. | PALJ-2 Q2 |
| First-time newborn / unidentified ID | Provider sends its own file number in `PATIENT_IDENTIFIER`; the system stores it as the provider reference and returns the new `CNHI_ID`. | PALJ-2 Q3 |
| Visit fields | `ENCOUNTER_CLASS` and `EMERGENCY_DEPARTMENT_DISPOSITION` accepted as **optional** inputs on 001-01 (they already exist in the provider reply 004-01, and UC004 BR006 lets the provider correct the visit class) and passed through in 001-04; `ENCOUNTER_CLASS` sent empty in 001-04 when not provided, although 001-04 marks it mandatory. The triage rule uses `ACCESS_MODE` DA01 / DA02 until visit class is available. `VISIT_DATE` stored as given (assumed: first arrival date, may precede the authorization month, per UC013 BR005) and used on the coverage form. | PALJ-2 Q4 |
| Amount names | Internal model uses net / tax / gross totals. Outbound field names follow each table as written until told otherwise. | PALJ-2 Q5 |
| Price list | Reference data per provider category, versioned with effective dates. Each sub-request (and each provider reply that changes services) is priced with the version valid on its own submission date. Provider category is provider master data, loaded by tech with the provider → TPA mapping. | PALJ-2 Q7 |
| VAT and unit price | Per line, validate `SERVICE_NET_AMOUNT = UNIT_PRICE × QUANTITY` and `SERVICE_GROSS_AMOUNT = NET + TAX`; do not validate the tax amount against a VAT rate: the requirements define tax "at the approved rate" (001-01) but BR011 / ALT009 list no tax-rate validation. Revisit if the business confirms a rate check (the BA had promised a ZATCA rate reference). For listed codes, reject (MSG006) when `UNIT_PRICE` differs from the price list for the provider's category; for Unlisted, accept the provider's price. Totals and `APPROVED_AMOUNT` are calculated by us. | PALJ-2 Q6 |
| Patient nationality | Mandatory for every ID type except "Identity Number for Unknowns". | PALJ-2 Q11 |
| Referral date | Required from the provider and stored as a fallback; replaced by the referral system's date once verification succeeds (BR013). | PALJ-2 Q12 |
| Patient ID type codes | Inbound requests use the appendix codes (`National ID`, `IQAMA`, `Border Number`, `Visa Number`, `Passport`, `Identity Number for Unknowns`, `Newborn`); a returning CNHI_ID patient is sent with Unknowns / Newborn type and the CNHI_ID as number. Outbound calls to Yaqeen (001-02) and the eligibility engine (001-03) map to those systems' own codes (`NID`, `IQAMA`, `BORDER`, `VISA`, `PASSPORT`, `CNHI_ID`) in the integration client. | PALJ-2 Q15 |
| Requested by | Store `REQUESTED_BY` as `PROVIDER` / `TPA`; map TPA to `ADVANCED` in 001-05 and to "Pre" / "Advanced" on the coverage form. | PALJ-2 Q16 |
| Ehalati referral number | Optional everywhere (001-01, 001-06); sent empty in 001-04 when absent. Stored as a cross-reference only; we do not integrate with Ehalati. | PALJ-2 Q14 |

## 5. Cross-story observations

- **Validation rules are shared.** UC_001 BR011 and UC_005 BC004 (Sprint 2) use the same rules. Build one validator now and reuse it.
- **Async eligibility is shared.** UC_005 BR003 re-checks eligibility for every sub-request (Sprint 2), using the same client and pattern.
- **The notification client is shared** by 001-04, 001-05, the Sprint 1 tables (003-02, 004-02, 006-02, 006-04) and later sprints. Build a generic outbound notification and delivery mechanism.
- **The Action Log action list is incomplete.** UC_014 BR009/BR010 don't list the verification-flag updates or the Yaqeen mismatch, although UC_001 ALT006, ALT007, ALT012 and BR005 say these are logged. We will log them anyway (D2). Raised as a question so the list gets updated.
- **Some "notify" steps have no payload.** UC_001 says the system notifies parties when a Yaqeen, eligibility or referral verification completes, but **no data table defines that notification**. Raised as a business question. It blocks the notification part of the async pattern, but not the verification itself.
