# BRD Context & Business Gaps — Medical Authorizations (Sprint 0 & 1)

> Shared reference file for the team and for Claude.
> **Source of truth:** the user-story files in `docs/stories/PALJ-*`, the latest version of the requirements.
> **Last updated:** 04 Oct 2026
> **Scope of the gap analysis:** business gaps only. Infrastructure and technical design are owned by the tech team.

---

## 1. Sources

| Source | Role |
|---|---|
| `docs/stories/PALJ-*/UC_*.docx` (16 UCs) | **Primary source.** Latest requirements. All UC numbers in this file follow these files. |
| `Files/BRD Gap Report - Sprint 0 & 1 Business Feedback.docx` | BA's answers to our gap report (28 Sep 2026). **Reference only.** Where it differs from the stories, the stories win. |
| `docs/plans/sprint-0/brd.docx` (**BRD v1.1, 28 Sep 2026**) | Current BRD. Its use-case tables are **identical** to the story files (checked row by row). Used for everything the stories leave out (see 1.2); extracted to `docs/brd/`. Replaces BRD V1.0. |
| `Files/Sprint Only view 20092026.xlsx` | Project plan, Sprint 0 to go-live. |

### 1.1 Use cases (story files)

| UC | Title | Jira | Plan sprint |
|---|---|---|---|
| UC_001 | Submit medical authorization request (provider or TPA "advanced") | PALJ-2 | Sprint 0 |
| UC_002 | **Stand-alone eligibility inquiry** before submitting (new) | PALJ-5 | ❓ not in plan |
| UC_003 | Receive TPA decision | PALJ-7 | Sprint 1 |
| UC_004 | Provider reply to a returned sub-request | PALJ-8 | Sprint 1 |
| UC_005 | Add sub-request to an existing authorization | PALJ-9 | Sprint 2 |
| UC_006 | **Provider asks the TPA to return a sub-request** (new) | PALJ-10 | ❓ not in plan (fits Sprint 1) |
| UC_007 | Discharge update via API | PALJ-11 | Sprint 2 |
| UC_008 | **Discharge from the platform screen by a provider user** (new) | PALJ-12 | ❓ not in plan |
| UC_009 | Automatic status updates (scheduled jobs + claim events) | PALJ-13 | Sprint 2/3 |
| UC_010 | Cancel authorization (CNHI screen / TPA API) | PALJ-14 | Sprint 3 |
| UC_011 | Reopen authorization (CNHI) | PALJ-15 | Sprint 3 |
| UC_012 | Reassign a returned sub-request back to the TPA (CNHI) | PALJ-16 | Sprint 3 |
| UC_013 | Print coverage decision form (Coverage Form / Visa) | PALJ-17 | Sprint 4 |
| UC_014 | Action Log | PALJ-18 | Sprint 0 (framework) / Sprint 4 (screens) |
| UC_015 | Portal view screens | PALJ-19 | Sprint 4 |
| UC_016 | System inquiry APIs | PALJ-20 | Sprint 4 |

> ⚠️ `docs/stories/PALJ-5/إنشاء طلب موافقة طبية.docx` is an **older draft of table 001-01**: it has no `AUTHORIZATION_MONTH` and is filed under the wrong Jira ticket. Ignore it; use UC_001.

### 1.2 Not in the story files (now provided by BRD v1.1)
The appendices the story files refer to are in BRD v1.1, together with the overview sections. All of it is extracted to `docs/brd/`:
- Scope, out of scope, glossary, assumptions, risks → `docs/brd/overview.md`
- User permissions and the action × party matrix → `docs/brd/roles-and-permissions.md`
- The 4 feature groups and the parties per use case → `docs/brd/use-case-map.md`
- Business process flow (BPMN) → `docs/brd/process-flow.md` + `docs/brd/flows/`
- Status lists, status transitions, SLA table, reference code lists, attachment rules, numbering formats → `docs/brd/reference-data.md`
- UI mockups and printed forms → `docs/brd/ui/`

Still **not** provided: the Administration BRD (TPA routing, editing of reason lists), the TPA decision SLA, and a notification event list.

---

## 2. Business Context (from the story files)

### 2.1 Actors
- **Provider system** (API) — submits requests, replies to returns, adds sub-requests, asks the TPA to return a sub-request (UC_006), records discharge, runs inquiries.
- **Provider user** (portal) — views. **New:** can record a discharge from the screen (UC_008).
- **TPA system** (API) — decides; submits "advanced" authorizations on behalf of an assigned provider; cancels; rejects a provider's return request.
- **TPA user** (portal) — views only.
- **CNHI Admin** (portal) — views all; cancels, reopens, reassigns.
- **CNHI General** (portal) — views all; prints only.
- **System** — scheduled jobs, internal claim events, asynchronous retries.

### 2.2 Integrations
| Integration | Use | If it is down |
|---|---|---|
| **Yaqeen** (NID / Iqama) | Beneficiary data. Yaqeen data wins on mismatch; the difference is logged and never causes a rejection (BR005). | **Asynchronous:** accept the request with the provider's data; `YAQEEN_VERIFICATION_STATUS` = Pending; retry until Verified or Not verified; notify both parties (ALT006, BR006). |
| **Eligibility engine** | Informational status only — never blocks. Checked at creation **and at every sub-request**, stored per sub-request (UC_005 BR003). | **Asynchronous:** status `PENDING_VERIFICATION`; retry; notify the TPA when the result arrives (ALT007, BR008). |
| **Referral distribution system** — **now in scope** | When the access type is Referral, the referral data is retrieved by referral number (table 001-06). Referral date and Ehalati number from that system win (BR013). | Not found → error MSG007. Down → accept with `REFERRAL_VERIFICATION_STATUS` = Pending and retry (ALT011, ALT012). |
| **Claims service** (internal) | Claim submitted / accepted events; claims retrieval for reopen. | — |
| **Stand-alone eligibility inquiry** (UC_002) | Read-only. Returns the status only. Not logged. | **Synchronous:** returns error MSG002. |

### 2.3 Core rules
- **Authorization month** (`AUTHORIZATION_MONTH`, YYYY-MM) is chosen by the submitter. Only the **current or next month** is accepted (BR014, ALT013). The duplicate check, previous-month linking, and validity end are all based on it, **not on the creation date**.
- **One authorization per beneficiary, per provider, per authorization month**, unless the earlier one has a discharge recorded (BR002).
- **Previous-month continuation:** if the previous month's authorization is Active with no discharge, the new request must include its number and the "still admitted" confirmation (BR004, ALT004, ALT005). The previous one must be **Active only**.
- **Linking authorizations** (BR012): next month → previous month, **or automatically when they share the same `REFERRAL_NUMBER`**.
- **Advanced authorization** (BR010): the TPA submits for an **assigned** provider; `PROVIDER_LICENSE` is required. The same TPA decides via UC_003. The provider is notified (table 001-05). `REQUESTED_BY` = ADVANCED.
- **CNHI_ID** (BR007): generated for **unidentified patients and newborns**. Format `CNHI` + YY + 8 digits (e.g. `CNHI2664412975`); the algorithm prevents duplicates and includes a check value. Used in all later transactions.
- **Validations** (BR011, ALT009 → MSG006): service code is in the reference price list or is Unlisted; no duplicate codes; quantity > 0; valid ICD-10; admission date not in the future; month is current or next.
- **Pricing:** a CNHI price list, by **provider category**.
- **VAT per service:** `SERVICE_NET_AMOUNT` (price × quantity), `SERVICE_TAX_AMOUNT` (0 if exempt), `SERVICE_GROSS_AMOUNT` (net + tax). The system validates the calculation.
- **Notification delivery** (BR009): on ACK = FAILED or no ACK, the system retries automatically; if that fails, it marks the notification "Delivery Failed", visible to users who can see the request. No status change.
- **One open sub-request at a time.** To change a sub-request that is pending TPA review, the provider asks for it to be returned (UC_006); if it is already returned, the provider changes it in the reply (UC_004).

### 2.4 Review cycle (Sprint 1 core)
- **TPA decision** (UC_003) is per service: `APPROVED`, `PARTIALLY APPROVED` (approved quantity is less than requested), or `REJECTED` (with reason RJ01–RJ06 or RJ99 + text). The sub-request status is **calculated** from the service decisions.
- **Approved quantity:** `0 < approved ≤ requested`. A partial approval requires `SERVICE_DECISION_NOTE` (BR004, ALT005 → MSG004).
- **Return (RETURNED)** applies to the whole sub-request; no mixing with approvals. `DOCTOR_NOTES` is required. **Maximum 20 return cycles**, then error MSG003 and the TPA must give a final decision.
- **Provider reply** (UC_004): within **7 days**, or the sub-request is auto-cancelled.
  - The note is optional, but at least one item is required: note, attachment, service change, or data change.
  - Service changes allowed: change quantity, replace, delete, add. At least one service must remain.
  - **Main-data edits are allowed only on the first return of the first sub-request** (BR006).
  - The system does not check that every requested document was attached (BR010).
  - Line numbers are sequential and never reused (BR009).
- **Provider-requested return** (UC_006):
  - Sub-request status → `RETURN_REQUESTED`. Reasons RR01–RR04, RR99.
  - The TPA can then return it (counts as a normal return: 7-day SLA and part of the 20 cycles), give a final decision, or reject the request (status goes back to Pending TPA Review, via tables 006-03/04).
  - No SLA for the TPA to act. The provider may ask again.
- **Every action is logged** (UC_014). The log can't be edited or deleted and is **never deleted** (BR004).

### 2.5 Statuses
- **Main authorization:** `ACTIVE`, `COMPLETED`, `CANCELLED`.
- **Sub-request:** `PENDING_TPA_REVIEW`, `PENDING_PROVIDER_RESPONSE`, **`RETURN_REQUESTED`** (new), `APPROVED`, `PARTIALLY_APPROVED`, `REJECTED`, `CANCELLED`.
- **Verification flags** (Yaqeen / referral): `VERIFIED`, `PENDING_VERIFICATION`, `NOT_VERIFIED`.
- **Eligibility:** `ELIGIBLE`, `PARTIALLY_ELIGIBLE`, `NOT_ELIGIBLE`, `UNIDENTIFIED`, `PENDING_VERIFICATION`.

### 2.6 Key code lists
- **ID types:** National ID, Iqama, Border, Visa, Passport, **Unknown-identity ID**, **Newborn**.
- **Access type** (`ACCESS_TYPE`, new): Referral, Direct Admission, **Already Admitted**.
- **Access mode** (`ACCESS_MODE`, new): DA01, DA ("new referral"), DA02, DA07, DA08, DA12, DA13, DA14 ("no financial coverage").
- **Triage:** CTAS 1–5.
- **Documents:** MED_REPORT, LAB, RAD_REPORT, RAD_IMAGE, OP_REPORT, RX, DISCHARGE_SUMMARY, REFERRAL_FORM, ID_COPY, **RED_CRESENT**, OTHER. Final list.
- **Service change type:** UNCHANGED, QUANTITY_MODIFIED, REPLACED, DELETED, ADDED.
- **Numbering:** authorization `MA-YYYY-NNNNNNNN` (resets yearly); sub-request `<auth no.>-NN`.

---

## 3. Sprint 0 & 1 Plan (Excel rows)

**Sprint 0 — 30 Sep to 15 Oct**
- #4 Yaqeen
- #5 Eligibility
- #6 TPA Routing
- #7 Notifications
- #8 Attachments
- #9 Audit Log
- #10 Numbering
- #11 Duplicate check
- #12 Data model *(tech)*
- #13 Status engine
- #14 Roles & access
- #15 Create API

**Sprint 1 — 16 Oct to 02 Nov**
- #18 TPA review
- #19 Full approval
- #20 Partial approval
- #21 Rejection
- #22 Return for information
- #23 Service-level decision
- #24 Rejection reasons
- #25 Approved quantity
- #26 TPA documents
- #27 Decision notification
- #28 Provider response
- #29 Provider documents
- #30 Quantity amendment
- #31 TPA re-review
- #32 Review cycle status
- #33 Review cycle audit
- #34 Review cycle notifications

---

## 4. Gap Status (checked against the story files)

**Legend:**
- ✅ **Closed** — covered in the stories.
- ⚠️ **Open** — still missing or unclear.
- 🆕 **New gap** — found in the stories themselves.

The BA feedback column is for reference only.

### Sprint 0

| # | Item | Status | Detail (stories) | BA feedback said |
|---|---|---|---|---|
| 4 | Yaqeen | ✅ | Asynchronous handling, Yaqeen data wins, AR/EN names, CNHI_ID (UC_001 ALT006, BR005–BR007). | Same. Note: the feedback's CNHI_ID format (`CNHI-YYYY-…`) is **replaced** by `CNHI`+YY+8 in the stories. |
| 4 | Yaqeen | 🆕 ⚠️ | `PATIENT_IDENTIFIER` is mandatory, but what does the provider send for a **first-time** newborn or unidentified patient (no CNHI_ID yet)? Its own file number? | — |
| 5 | Eligibility | ✅ | Asynchronous, Pending Verification; re-checked per sub-request; UC_002 stand-alone inquiry. | Same. |
| 6 | **TPA Routing** | ⚠️ **Open** | Only a precondition: "provider is linked to an assigned TPA". The mapping and who maintains it are not defined. | Moved to a separate **Administration BRD** (not shared). |
| 7 | Notifications | ✅ | Retry, then "Delivery Failed" (BR009). | Same. |
| 7 | Notifications | ⚠️ Open | No single list of events and recipients. Reopen and reassign say "notify", but UC_016 says no notification for them (later sprints). | The BA thought we meant emails; we need to explain. |
| 8 | Attachments | ✅ | Optional attachments on creation and sub-request; file name added; document list final. | Same. |
| 8 | Attachments | ✅ | Limits in the BRD v1.1 appendix: PDF / JPG / PNG, 5 MB per file, 10 files per action, kept as long as the action log, applies to 001-01, 003-01, 004-01, 005-01 and their notifications. (Table 003-01 still says "to be decided"; the appendix wins.) | Proposed 5 MB / 10 files / PDF-JPG-PNG. |
| 9 | Audit Log | ✅ | Log is never deleted; ALT001 = none (UC_014 BR004). | — |
| 10 | Numbering | ✅ | `MA-YYYY-NNNNNNNN`; line numbering rule (UC_004 BR009). | Same. |
| 11 | Duplicates | ✅ | Active only; month = **`AUTHORIZATION_MONTH`** (BR002, BR014). Cross-ID-type check out of scope. | Feedback said "creation date" — **the stories changed this to authorization month.** |
| 12 | Data model / VAT | ✅ | VAT per service and totals. | Same. |
| 12 | Price list | 🆕 ⚠️ | BR011 / UC005 BC004 only say "a list defined at CNHI, by provider category". Missing: source and update mechanism, versioning and effective date, the provider category list and where a provider's category is held (provider master data, like TPA routing #6), and which version applies to later sub-requests and replies. 🆕 The price list and provider category are not inputs, so the system must hold them as reference data; yet providers must send `UNIT_PRICE` and amounts, and no use case says how providers obtain the price list (no API). If they don't have it, the system should fill price and amounts for listed codes. | Price-list answer cut off. |
| 12 | Unit price | 🆕 ⚠️ | `UNIT_PRICE` is a mandatory input in 001-01 / 005-01 / 004-01 but described as "automatic" for listed codes; UC004 BR003 says amounts are calculated from the price list. Behaviour when the provider's price differs from the list is not defined (reject or override). | — |
| 12 | Patient share | 🆕 ⚠️ | `PATIENT_SHARE_AMOUNT` (تحمل المستفيد) exists only in 005-01 (UC005, add sub-request), as a per-service-line field in the requested services block (optional, no description). Not in 001-01, not sent to the TPA, not shown or returned anywhere. | — |
| 12 | VAT | 🆕 ⚠️ | Total field names differ (after-tax total: `TPA_AMOUNT` in 001-01; `NET_AMOUNT` in 001-04, 005-02, 013-01, 015-01, 016-01; `NET_REQUESTED_AMOUNT` in 004-01, 004-02, 005-01; pre-tax total only in 001-01; total tax only in 001-01 and 001-04). Earlier summary: `TPA_AMOUNT` (001-01), `NET_AMOUNT` holding the gross (001-04), `NET_REQUESTED_AMOUNT` (004). `APPROVED_AMOUNT` = price × approved quantity, **with no VAT**: is it net or gross? The VAT rate reference is not stated. | Promised a ZATCA rate reference. |
| 13 | Status engine | ✅ | BRD v1.1 gives the main and sub-request status lists and both transition tables, including "Return Requested by Provider" (`docs/brd/reference-data.md`). | Appendix promised. |
| 13 | Status engine | ⚠️ Open | `RETURN_REQUESTED` has no code in the appendix and is still missing from the status lists in 001-01, 003-01, 003-02, 014-01 (before / after), the 012-01 and 015-01 search filters, 016-02, and from UC_003 MSG001. It appears only in 006-01, 006-02, 016-01 and the 015-01 detail page. | — |
| 13 | Code values | 🆕 ⚠️ | **Inconsistent codes across tables:**<br>• Partial approval is `PARTIAL` (`DECISION`, 003-01 / 003-02 / 016-01), `PARTIALLY APPROVED` with a space (`SERVICE_DECISION`), and `PARTIALLY_APPROVED` (`SUB_REQUEST_STATUS`, `CLAIM_STATUS`).<br>• `PATIENT_IDENTIFIER_TYPE`: `National ID` / `Border Number` / `Visa Number` (001-01, 001-04, appendix; also misspelled "Broder") vs `NID` / `BORDER` / `VISA` / `PASSPORT` / `CNHI_ID` (001-02, 001-03, 002-01). 002-01 has no `Newborn` or `Identity Number for Unknowns`.<br>• `REQUESTED_BY`: `ADVANCED` (001-05) vs `PROVIDER` / `TPA` (016-01); form shows Pre / Advanced.<br>• Several lists have Arabic labels but **no codes**: `CLOSURE_TYPE`, `VALIDITY_END_TYPE` (only `DISCHARGE` in 007-01), `SUB_REQUEST_TYPE`, `RETURN_SOURCE`, the 015-01 indicators, notification delivery status. We will define codes (tech).<br>• `PATIENT_NATIONALITY` has no reference list; `PATIENT_GENDER` is defined only in the Yaqeen table (M / F). | — |
| 13 | Process diagram | 🆕 ⚠️ | The BRD business process diagram is outdated: main statuses "Pending claim submission" and "Claim processed by the TPA" (not in the status list; the authorization stays `ACTIVE` until the claim is accepted, then `COMPLETED`); the provider fills in referral data (the use case retrieves it from the referral system); Yaqeen and eligibility shown as blocking steps (the use case makes them asynchronous); no Already Admitted, advanced authorization, UC_006, discharge, cancel, reopen or reassign. Details in `docs/brd/process-flow.md`. | — |
| 14 | Roles | ✅ | BRD v1.1 gives the permissions per party and the action × party matrix (`docs/brd/roles-and-permissions.md`). The "Advanced" term is fixed. | Matrix promised. |
| 14 | Roles | 🆕 ⚠️ | **The BRD contradicts itself:**<br>• The out-of-scope section says provider and TPA users are view only, but the provider user can record a discharge (UC_008) and print (UC_013).<br>• The permissions list says the TPA user and CNHI General can only view, but the matrix lets the TPA user and all CNHI users print (UC_013).<br>• The CNHI Admin permissions list leaves out reassign (UC_012), which the matrix gives to the Admin.<br>• The matrix says "CNHI Viewer"; the permissions list says "CNHI user – General".<br>• The use-case map diagram shows UC_014 executed by the System and the TPA, with CNHI only affected and the provider missing. | — |
| 15 | Create API | ✅ | Advanced flow, validations, triage CTAS 1–5, price list by provider category. The BRD reference lists confirm the new `ACCESS_TYPE` and `ACCESS_MODE` codes (DA01, DA, DA02, DA07, DA08, DA12, DA13, DA14). | Same. |
| 15 | Access type | 🆕 ⚠️ | The glossary and the provider-system permissions mention only Referral and Direct Admission; "Already Admitted" appears only for the TPA's advanced authorization, while 001-01 and the reference list offer it to everyone. The reference list titles the `ACCESS_MODE` codes "direct admission reasons" but includes "DA" (new referral), and `ACCESS_MODE` is mandatory even for a referral. | — |
| 15 | Create API | 🆕 ⚠️ | **Request and notification fields don't match:**<br>• 001-01 uses `ACCESS_TYPE` / `ACCESS_MODE` (new codes), while 001-04 and 004-01 still use `ADMISSION_TYPE` / `DIRECT_ADMISSION_REASON` DA01–DA13.<br>• `ENCOUNTER_CLASS` and `EMERGENCY_DEPARTMENT_DISPOSITION` are missing from the 001-01 request but sent in 001-04, and the triage rule depends on EMER.<br>• `VISIT_DATE` is new and its meaning is not defined.<br>• What do code "DA" (new referral) and DA14 mean?<br>• Admission date vs referral date check (promised) is missing.<br>• Price list version / effective date is not stated. | Validations promised; price-list answer cut off. |
| 15 | Visit fields | 🆕 ⚠️ | `ENCOUNTER_CLASS` (mandatory in 001-04) and `EMERGENCY_DEPARTMENT_DISPOSITION` are missing from 001-01, yet they are in the provider reply 004-01 and editable per UC004 BR006, and the triage rule depends on `EMER`. `VISIT_DATE` is mandatory in 001-01 but has no definition and is not sent to the TPA (001-04) or returned by inquiry (016-01); UC013 BR005 and 013-01 ("Period From", "تاريخ دخول المستفيد", to be confirmed) suggest it is the first arrival date. | — |
| 15 | Create API | 🆕 ⚠️ | **Mandatory flags conflict with their notes in 001-01:**<br>• `PATIENT_NATIONALITY` is mandatory, but its note says it is not required for an unidentified patient. Newborn is not mentioned.<br>• `REFERRAL_DATE` is mandatory from the provider, but BR013 says the referral system's date wins. Is the provider's value only a fallback while the check is pending? | — |
| 15 | Referral | 🆕 ⚠️ | `Ehaility_Referral_Number` is optional in 001-01 and "not always present" in 001-06, but mandatory in 001-04 (request to the TPA). Field name is also misspelled (Ehalati). | — |
| 15 | Referral | 🆕 ⚠️ | **Referral integration is now in scope** (001-06, BR013), but it is **not in the project plan**. Needs a sprint and estimate. | — |

### Sprint 1

| # | Item | Status | Detail (stories) | BA feedback said |
|---|---|---|---|---|
| 18 / 31 | TPA deadline | ⚠️ **Open** | No SLA for the TPA decision anywhere in the stories. | "CNHI requested SLA to be enforced" — but no days or breach rule given. |
| 18 | Decision correction | ✅ | Decision is final (only status Pending / Return Requested is accepted). | Same. Wording said "CNHI"; should be TPA. |
| 19 | Full approval | ✅ | Status calculated from services; `DOCTOR_NOTES` required on RETURNED. | Same. |
| 19 | Field names | 🆕 ⚠️ | `TPA_NOTES` renamed to `DOCTOR_NOTES`, but the column still says "mandatory" (the note says only on return). The service note is `SERVICE_DECISION_NOTE` in 003-01 but `DOCTOR_DECISION_ON_SERVICE` in 003-02. `APPROVED_QUANTITY` is mandatory for every service in 003-01, including rejected ones? | — |
| 20 | Partial approval | ✅ | Partial = approved quantity less than requested (UC_003 BR001). | Same. |
| 20 | Decision consistency | 🆕 ⚠️ | 003-01 has both a sub-request `DECISION` (`APPROVED` / `REJECTED` / `PARTIAL` / `RETURNED`) and a `SERVICE_DECISION` per line, and BR001 says the sub-request status is **calculated** from the lines. Nothing says what happens when they disagree (for example `DECISION = APPROVED` with a rejected line). Proposed: for final decisions, reject the message when `DECISION` does not match the calculated result. | — |
| 21 | Rejection | ✅ | — | — |
| 22 | Return | ✅ | 20 cycles + MSG003; no mixed decisions. | Same. |
| 23 | Service-level decision | ✅ | Line numbering rule. | Same. |
| 24 | Rejection reasons config | ⚠️ Open | Fixed list; editing is not defined. | Administration BRD. |
| 25 | Approved quantity | ✅ | `0 < approved ≤ requested`; note required. | Same. |
| 26 / 29 | Documents | ✅ | Limits in the BRD v1.1 appendix, same as #8. | — |
| 27 | Decision notification | ✅ | Base64 + file name. | Same. |
| 28 | Provider response | ✅ | Note optional; documents not enforced; no appeal (a new sub-request is the route). | Same. |
| 30 | Quantity amendment | ✅ | REPLACED; first return of the first sub-request only; referral linking (BR012). | Same. |
| 32 | Review cycle status | ✅ | 20 cycles. | Same. |
| 33 | Review cycle audit | ✅ | BR010 completed, including UC_006 actions. | Same. |
| 34 | Review cycle notifications | ✅ | 003-02, 004-02, plus the new 006-02 and 006-04. | — |
| — | **UC_006 Provider-requested return** | 🆕 ⚠️ | New flow, new status, 4 new API tables. **Not in the plan** — it fits Sprint 1 (review cycle). | — |

---

## 5. Open Items (follow up with the BA)

**Blocking or high priority**
1. **TPA Routing (#6) and rejection reasons config (#24):** share the Administration BRD, or agree an interim rule: one TPA per provider, with the mapping set up by tech.
2. **TPA decision SLA (#18, #31):** number of days and what happens on breach.
3. ~~**Missing appendices**~~: provided in BRD v1.1 (04 Oct). Remaining: the Administration BRD (see 1) and the TPA decision SLA (see 2).
4. **001-01 vs 001-04 / 004-01 field mismatch:** `ACCESS_TYPE` / `ACCESS_MODE` vs `ADMISSION_TYPE` / `DIRECT_ADMISSION_REASON`; missing `ENCOUNTER_CLASS` and ED disposition; meaning of `VISIT_DATE`, "DA" and DA14.
5. **Plan alignment:** which sprint for the referral integration (001-06), UC_002 (eligibility inquiry), UC_006 (provider-requested return), and UC_008 (screen discharge)?

**Medium**

6. First-time newborn / unidentified patient: what goes in `PATIENT_IDENTIFIER`?
7. Amount fields: unify the total names; is `APPROVED_AMOUNT` net or gross? VAT rate reference.
8. Field names and mandatory flags in 003-01 / 003-02: `DOCTOR_NOTES`, the service note, `APPROVED_QUANTITY` for rejected services.
9. Add `RETURN_REQUESTED` to every status list and to UC_003 MSG001.
10. Permissions conflicts inside the BRD (#14): "portal is view-only" vs UC_008 discharge and UC_013 print; permissions list vs matrix for printing (TPA user, CNHI General) and reassign (CNHI Admin).
11. Notification event list (system-to-system, not email).
12. Price list version / effective date; admission date vs referral date check.
13. 001-01 mandatory flags vs notes: `PATIENT_NATIONALITY` for unidentified / newborn patients; `REFERRAL_DATE` from the provider vs the referral system's date (BR013).
14. Business process diagram is outdated (#13): update it to the current statuses and flows.
15. "Already Admitted" for provider systems, and `ACCESS_MODE` for a referral (#15).
16. Ehalati referral number: optional in 001-01 / 001-06 but mandatory in 001-04 (#15).
17. Claim events (UC009, Sprint 2/3): no rejected / cancelled claim event although rules depend on it; whether a partially approved claim completes the authorization; who accepts claims.
18. Coverage form (UC013, Sprint 4): mockup vs rules and 013-01 conflicts, missing per-sub-request mockup, fields to be confirmed, no template file.
19. Reopen limits (UC011, Sprint 3): BR008 "no limit" vs ALT003 / ALT007 limits.
20. Code values (#13): partial approval written three ways; ID type codes differ between tables; `REQUESTED_BY` values; nationality list missing; `RETURN_REQUESTED` missing from more lists.
21. Decision consistency (#20, Sprint 1): `DECISION` vs per-service decisions when they disagree.

---

## 6. New Scope vs BRD V1.0 (affects estimation)

**Sprint 0 related**
- Asynchronous Yaqeen verification: flag, retry, notifications.
- Asynchronous eligibility check, plus an eligibility check on every sub-request.
- **Referral distribution integration** (001-06): asynchronous, with flag and retry. ⚠️ not in plan.
- CNHI_ID generation (unidentified patients and newborns).
- Authorization month logic (current / next month).
- Advanced (TPA-submitted) flow and provider notification (001-05).
- Request validations (MSG006).
- VAT per service and totals.
- Attachments on creation.
- Notification retry and "Delivery Failed".
- Linking authorizations by referral number.

**Sprint 1 related**
- 20-cycle limit.
- Approved quantity validation.
- **UC_006 provider-requested return** (new status and 4 tables). ⚠️ not in plan.

**Other / unplanned**
- UC_002 stand-alone eligibility inquiry.
- UC_008 discharge from screen.

---

## 7. Parked for Later Sprints

| Topic | Sprint |
|---|---|
| 7-day auto-cancel: provider not notified; day counting (daily job) | Sprint 2 — SLA automation |
| UC_005: diagnosis can be edited on a new sub-request, which conflicts with the "first return only" rule | Sprint 2 — Additional service |
| Notifications: reopen (UC_011) and reassign (UC_012) say "notify", but UC_016 BR004 lists them as not notified | Sprint 3 |
| Reopen limits (UC011): 🆕 BR008 now says there is **no limit** on reopening, but ALT003 still blocks a reopen when a maximum period since validity end or a maximum reopen count is exceeded (MSG004), and ALT007 requires a supporting document above a "suggested" reopen count (MSG007). Which applies, and what are the values? Also still open: the TPA deadline for reassigned requests. | Sprint 3 |
| Coverage form (UC013, table 013-01 + mockups in `docs/brd/ui/UC_013-print-coverage-form/`): fields still "to be confirmed" (`EPISODE ID`, Period From / To, Transaction Type, Adjudication Reason category). 🆕 Mockup conflicts with BR001 / BR008 and 013-01: the whole-authorization mockup shows item details instead of the decided sub-requests table; no mockup for the per-sub-request form; labels differ ("Bundle ID" vs `EPISODE ID`, extra "Provider Type" and "Arrival Code", title "Coverage Request Status"); Main Authorization Status, Coverage Status, Patient Mobile and the per-item Rejection Reason are missing; "Admit Source" uses the old `ADMISSION_TYPE` / `DIRECT_ADMISSION_REASON`. No layout template file is provided (only images). | Sprint 4 |
| 🆕 Claim events (UC009, table 009-01): only `CLAIM_SUBMITTED` and `CLAIM_ACCEPTED` exist, yet the 90-day clock must restart on a **rejected or cancelled** claim (SLA appendix, UC009), UC010 BR002 allows cancelling when the claim is rejected or cancelled, and the 009-01 response mentions "ACTIVE after submission or rejection". A `CLAIM_REJECTED` / `CLAIM_CANCELLED` event (or equivalent) is missing. Also: does a `PARTIALLY_APPROVED` claim (011-02) count as accepted, making the authorization `COMPLETED`? And who reviews and accepts the claim is not stated (outdated process diagram suggests the TPA, with a completion certificate). | Sprint 2/3 |
| Screen for notification delivery status; provider branch / group access | Sprint 4 |

---

## 8. Change Log

| Date | Change |
|---|---|
| 24 Sep 2026 | Gap analysis of BRD V1.0 vs Sprint 0/1 plan; gap reports sent to the BA. |
| 28 Sep 2026 | BA feedback received. |
| 01 Oct 2026 | Follow-up message to the BA drafted. |
| 04 Oct 2026 | File rebuilt on the story files (`docs/stories/PALJ-*`) as the source of truth; gaps re-checked; new gaps from the stories added; BA feedback kept as reference only. |
| 04 Oct 2026 | Added the 001-01 mandatory-flag conflicts (`PATIENT_NATIONALITY`, `REFERRAL_DATE`). |
| 05 Oct 2026 | Added the Ehalati referral number mandatory-flag conflict (001-01 / 001-06 vs 001-04). |
| 06 Oct 2026 | Expanded Q7 (price list): source, provider category, version for later sub-requests and replies. |
| 06 Oct 2026 | Added the unit price mismatch gap to Q6 (reject or override when it differs from the price list). |
| 06 Oct 2026 | Detailed the total-amount naming gap per table (Q5) and added `PATIENT_SHARE_AMOUNT` (005-01 only). |
| 06 Oct 2026 | Sharpened Q4 (visit fields) with evidence from 004-01, UC004 BR006, UC013 BR005 and 013-01; added that `VISIT_DATE` is not sent to the TPA. |
| 05 Oct 2026 | Added the `DECISION` vs `SERVICE_DECISION` consistency gap (UC003). |
| 05 Oct 2026 | Full enum review across all 31 data tables: added the code-values gap, widened the `RETURN_REQUESTED` gap. |
| 05 Oct 2026 | Updated the reopen-limits gap: UC011 BR008 (no limit) contradicts ALT003 / ALT007. |
| 05 Oct 2026 | Expanded the coverage form gap (mockup vs 013-01 / BR001 / BR008). |
| 05 Oct 2026 | Added the claim-event gap (missing rejected / cancelled event, partial claims, who accepts claims), parked for UC009. |
| 04 Oct 2026 | BRD v1.1 received and extracted to `docs/brd/`. Use-case tables match the stories. Closed: attachment limits (#8, #26/29), status lists and transitions (#13), roles matrix (#14), missing appendices. New gaps: outdated process diagram, permissions contradictions, access type for providers. |
