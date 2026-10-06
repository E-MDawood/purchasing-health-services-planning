# Business gaps for the BA

Health Services Purchasing Platform · Pre-authorization

30 open questions from the 16 user stories (PALJ-2 to PALJ-20), checked against the development plan v0.5 and merged with the backend team's Sprint 0 questions and the planning team's review of BRD v1.1. Prepared 4 October 2026, revised 6 October 2026.

**How to answer:** reply per ID (BA-01 …). Until an item is answered, the team builds the assumption shown, behind a setting where possible, so an answer changes configuration rather than code.

| Blocks Sprint 0 | Sprint 1 | Sprint 2 | Sprint 3 |
|---|---|---|---|
| 13 · answer by 8 Oct | 7 · answer by 15 Oct | 8 · answer by 2 Nov | 2 · answer by 16 Nov |

## Decisions the stories should reflect

No answer needed. Please update the stories so partners and testers see the same behaviour.

- **Results are delivered later, not in the first reply.** Every partner action gets an immediate "queued" acknowledgement with a tracking ID. The data in each story's response table (authorization number, statuses, amounts, message codes) arrives in the result message, by push or poll. Please reflect this in the stories' response sections.
- **UC002 eligibility inquiry is queued too.** UC002 ALT002 describes a synchronous call. It is handled like every other action: queued, with the result in a message.
- **Push and poll, chosen per partner.** Each partner chooses webhook delivery (with ACK_STATUS RECEIVED / FAILED, retries, then "Delivery failed") or polling. After a failed delivery partners use the UC016 query.
- **FHIR for partner systems.** The story data tables are mapped to FHIR R4 messages (NPHIES style) with CNHI extensions. Field names in the stories stay the business reference.
- **Action log written within seconds.** UC014 says "in the same transaction". The log entry is saved with the change and appears in the log screens a few seconds later. Nothing is lost.

## Questions

### Documents and appendices

**BA-01** · Blocks Sprint 0 · by 8 Oct · All UCs (all stories, PALJ-2 to PALJ-20)

Please share BRD V1.1 and the appendices the stories refer to: attachment rules, the full status list and allowed status changes, the role × action matrix, service levels, reference code lists and numbering formats.

- Why it matters: The stories point to these appendices for rules we must implement; without them several rules are guesses.
- Until answered: The user stories are the source of truth. Statuses come from the stories; the old package's state-machine draft fills the gaps.

### Submitting an authorization (UC001)

**BA-02** · Blocks Sprint 0 · by 8 Oct · Table 001-01 ACCESS_MODE, table 001-04 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

The create request uses ACCESS_MODE (DA01, DA, DA02, DA07, DA08, DA12, DA13, DA14) but the request sent to the TPA uses DIRECT_ADMISSION_REASON (DA01–DA13). Which list is current? What do "DA" (new referral), DA14 (no financial coverage) and access type "Already admitted" mean for processing? ACCESS_MODE is mandatory for every access type: should it also be sent for a Referral (code "DA")? And can provider systems send "Already admitted", or only TPAs in an advanced authorization?

> BRD permissions, TPA system: "(احالة / دخول مباشرة/ منوم مسبقا) ... لغرض الموافقة المتقدمة"

- Why it matters: We validate these codes and pass them to the TPA; mismatched lists will reject valid requests or confuse TPAs.
- Until answered: Store ACCESS_MODE as received and map it to the TPA codes through a configurable table. ACCESS_MODE required for every access type; "Already admitted" accepted from providers and TPAs.

**BA-03** · Blocks Sprint 0 · by 8 Oct · UC001 BR011 (VAT) ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

Which VAT rate applies, and how is a service marked as tax-exempt? Is this part of the price list?

> 001-01 `SERVICE_TAX_AMOUNT`: "وفق النسبة المعتمدة، وتساوي صفرا للخدمات المعفاة"

- Why it matters: We cannot validate tax amounts without the rate and exemptions.
- Until answered: Only check that gross = net + tax for each service.

**BA-04** · Blocks Sprint 0 · by 8 Oct · UC001 BR011, BC004, table 001-01 UNIT_PRICE ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

Prices come from a CNHI list by provider category. Where does the list come from, how are new versions published, and which version applies: the one valid on the request date or on the admission date? The provider's category is not in the request: where is it kept? Providers must send UNIT_PRICE and the amounts, so how do they get the price list? If a provider's unit price differs from the list, should the request be rejected, or the price replaced from the list?

> UC001 BR011: "قائمة معرفة لدى المركز بأسعار الخدمات بما يتقاطع مع فئة المزود" · 001-01 `UNIT_PRICE`: "تلقائي اذا الرمز من قائمة الأسعار"

- Why it matters: Price validation and the approved amounts depend on it.
- Until answered: The version valid on the request date, per provider category; provider category held as reference data. A unit price that differs from the list is rejected (configurable: reject or replace).

**BA-05** · Blocks Sprint 0 · by 8 Oct · Attachments appendix, tables 001-04, 003-02, 016-03 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

Please confirm 5 MB per file, 10 files per action, PDF / JPG / PNG. Attachments travel as Base64 (10 × 5 MB ≈ 67 MB in one message), including in notifications to partners and in UC016 downloads. Is a total size per message acceptable?

- Why it matters: Gateway and partner systems usually reject messages this large.
- Until answered: Configurable limits with those defaults, plus a total size cap per message.

**BA-06** · Blocks Sprint 0 · by 8 Oct · UC001 ALT006 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

When the Yaqeen check ends as NOT_VERIFIED, the story says the Center "takes the appropriate action". What is that action? Does the request continue as normal meanwhile? Does the Center need a work list of these cases?

> UC001 ALT006: "ويشعر الجهتين ليتخذ المركز الإجراء المناسب"

- Why it matters: Decides whether we build a Center work queue or only a flag.
- Until answered: The request continues. The authorization is flagged and the portal can filter on it. No work queue.

**BA-07** · Blocks Sprint 0 · by 8 Oct · UC001 ALT006, ALT007, ALT012 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

When a delayed Yaqeen, eligibility or referral check finishes, the parties are notified, but no data table defines this notification. What should it contain?

- Why it matters: We cannot build or test the message without its fields.
- Until answered: Verification type, result and the request status; recipients as in the story (Yaqeen: provider and TPA; eligibility: TPA; referral: TPA, or both when not verified).

**BA-08** · Blocks Sprint 0 · by 8 Oct · UC001 BR006, BR008, BR013 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

How long do we keep retrying Yaqeen, eligibility and the referral system before the result becomes NOT_VERIFIED?

> UC001 BR006: "ويعيد المحاولة آليا وفق جدولة يحددها الفريق التقني"

- Why it matters: Affects when the parties are told a check failed.
- Until answered: Every 15 minutes for 72 hours (configurable).

**BA-09** · Blocks Sprint 0 · by 8 Oct · UC001 step 3, table 001-06, UC013 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

Referral distribution system: can we get its interface description and test environment? Is PATIENT_MOBILE on the coverage form taken only from referral data (so empty for direct admissions)?

> 013-01 Patient Mobile: "كما ورد من نظام توزيع الإحالات ... ويترك فارغا إن لم يتوفر"

- Why it matters: The referral check is part of every referral submission.
- Until answered: Build from table 001-06 with a simulator until the interface is shared. PATIENT_MOBILE empty when there is no referral.

**BA-10** · Blocks Sprint 0 · by 8 Oct · UC001 BR013, tables 001-01, 001-04, 001-06 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

Referral fields: REFERRAL_DATE is mandatory from the provider, but the referral system's date replaces it when they differ. Is the provider's date only a fallback while the referral check is pending? The Ehalati referral number is optional in the create request and "not always present" from the referral system, but mandatory in the request sent to the TPA. Which is correct?

> UC001 BR013: "ويعتمد تاريخ الإحالة ورقم إحالتي كما وردا من نظام التوزيع عند اختلافهما عن المرسل من المزود" · 001-06 Ehalati: "لا يوجد دائما"

- Why it matters: A mandatory field we cannot always fill will fail the message to the TPA.
- Until answered: The provider's referral date is stored as a fallback and replaced on verification. The Ehalati number stays optional everywhere and is sent empty when absent.

**BA-11** · Blocks Sprint 0 · by 8 Oct · Tables 001-01, 001-04, 016-01, 013-01 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

VISIT_DATE is mandatory in the create request, but it is not sent to the TPA and not returned by the authorization inquiry; it only appears on the coverage form ("Period From"). Should the TPA and the inquiry also receive it?

- Why it matters: The TPA reviews the request without a date the provider is required to send.
- Until answered: Stored and printed on the form only; not sent to the TPA or the inquiry.

**BA-12** · Blocks Sprint 0 · by 8 Oct · Table 001-01 PATIENT_NATIONALITY ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

PATIENT_NATIONALITY is mandatory, but its note says it is not required for an unidentified patient. Is it also optional for a newborn? No nationality code list is given: which list should be used?

> 001-01 `PATIENT_NATIONALITY`: "غير الزامي في حال ان المستفيد مجهول الهوية"

- Why it matters: The field is validated on every request.
- Until answered: Mandatory except for "Identity Number for Unknowns"; ISO country codes.

**BA-13** · Blocks Sprint 0 · by 8 Oct · Tables 001-01, 001-05, 002-01, 003-01, 013-01, 016-01 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-5](https://leansa.atlassian.net/browse/PALJ-5), UC_002; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

The same values are written with different codes in different tables. ID types: "National ID", "Border Number", "Visa Number" (001-01, appendix) vs "NID", "BORDER", "VISA", "CNHI_ID" (002-01, which also has no "Newborn" or "Identity Number for Unknowns"). Requested by: "ADVANCED" (001-05) vs "PROVIDER" / "TPA" (016-01) vs "Pre" / "Advanced" (coverage form). Partial approval: "PARTIAL" (decision), "PARTIALLY APPROVED" (per service) and "PARTIALLY_APPROVED" (status). Can one set of codes be used for each?

- Why it matters: Partners validate against these codes; different spellings for the same value break their integrations.
- Until answered: Appendix codes for everything partners send us, mapped per partner system where needed. Requested by stored as PROVIDER / TPA. Partial stored as PARTIALLY_APPROVED; each table's spelling accepted as written.

### TPA decision, returns and reassignment (UC003–UC006, UC012)

**BA-14** · Sprint 1 · by 15 Oct · UC003, gap #18 ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003)

What is the TPA decision service level (how many days or hours), and what happens when it is breached?

- Why it matters: The SLA timer must start when a request is assigned; the breach action is a business rule.
- Until answered: Configurable and switched off until answered.

**BA-15** · Sprint 1 · by 15 Oct · Tables 001-01, 003 MSG001; filters in UC012, UC015, UC016 ([PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006; [PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-19](https://leansa.atlassian.net/browse/PALJ-19), UC_015; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

RETURN_REQUESTED is defined in UC006 but missing from the status lists in table 001-01, UC003 MSG001 and the status filters of UC012, UC015 and UC016. Please confirm it is a valid sub-request status everywhere.

- Why it matters: Partners and screens must handle every status the system can produce.
- Until answered: It is included in every status list and filter.

**BA-16** · Sprint 1 · by 15 Oct · UC001 BR003, UC004 BR006, table 005-01 ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-8](https://leansa.atlassian.net/browse/PALJ-8), UC_004; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

Main-authorization data may be edited on "the first return of the sub-request" (UC001 BR003) or on "the first return of the first sub-request only" (UC004 BR006)? UC005 also lets the provider send diagnosis codes with a new sub-request "at main authorization level". Which rule applies?

> UC001 BR003: "في الإعادة الأولى للطلب الفرعي" · UC004 BR006: "في الإعادة الأولى للطلب الفرعي الأول فقط" · 005-01: "يرسل فقط إذا اختلف عن الموافقة الرئيسية ويحدث على مستوى الموافقة الرئيسية"

- Why it matters: The edit rule is enforced on every provider response.
- Until answered: Main data only on the first return of the first sub-request; diagnoses may be added with a new sub-request (UC005).

**BA-17** · Sprint 1 · by 15 Oct · UC003 BR001, BR004, tables 003-01, 003-02 ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003)

The TPA sends both DECISION for the sub-request and SERVICE_DECISION for each service, while the sub-request status is calculated from the service decisions. What happens when they disagree (for example DECISION = APPROVED with a rejected service)? Also in 003-01: DOCTOR_NOTES is marked mandatory but is described as needed only on a return; SERVICE_DECISION_NOTE is mandatory for every service, but BR004 requires it only for a partial approval; APPROVED_QUANTITY is mandatory even for rejected services; and the service note is SERVICE_DECISION_NOTE in 003-01 but DOCTOR_DECISION_ON_SERVICE in 003-02.

> UC003 BR001: "وتحدد حالة الطلب الفرعي بناء على قرارات الخدمات"

- Why it matters: TPA decisions are rejected or accepted on these rules.
- Until answered: Reject a final decision whose DECISION does not match the service decisions. Notes required only where the rules say. APPROVED_QUANTITY 0 for rejected services. One note field, named as in 003-01.

**BA-18** · Sprint 1 · by 15 Oct · Appendix reason lists ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011; [PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006)

The rejection (RJ), cancellation (CR), reopen (RO), reassignment (RA) and return-request (RR) reasons are fixed lists. Can CNHI change them after go-live, and how? This was earlier deferred to an Administration BRD.

- Why it matters: Decides whether we build a maintenance screen or load the lists as reference data.
- Until answered: Loaded as reference data by the technical team; no maintenance screen.

**BA-19** · Sprint 2 · by 2 Nov · Table 005-01 ([PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

PATIENT_SHARE_AMOUNT appears in the add-sub-request input but is not used anywhere else. What is it for, and should it be validated or shown?

- Why it matters: Unclear fields end up stored but never used.
- Until answered: Stored as received; not validated or displayed.

**BA-20** · Sprint 2 · by 2 Nov · UC012 BR006 ([PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012)

Please clarify BR006.

> UC012 BR006: "لا يمكن رفع طلب فرعي جديد حتى لو تم إعادة الطلب لشركة ادراة المطالبات من المركز"

- Why it matters: We cannot implement the rule as written.
- Until answered: No new sub-request while another sub-request of the authorization is still open (same as UC005).

### Discharge, automatic updates, cancel and reopen (UC007–UC011)

**BA-21** · Sprint 2 · by 2 Nov · Table 009-01, table 011-02, UC010 BR002 ([PALJ-13](https://leansa.atlassian.net/browse/PALJ-13), UC_009; [PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

Claim events only include CLAIM_SUBMITTED and CLAIM_ACCEPTED. Cancelling is allowed when the linked claim was rejected or cancelled, and the claim deadline may need to restart. Can the Claims Service also send CLAIM_REJECTED and CLAIM_CANCELLED? Also, the claim status list (011-02) includes PARTIALLY_APPROVED: does a partially approved claim count as accepted and complete the authorization?

- Why it matters: Without them we cannot know a claim was rejected or cancelled, or when an authorization is complete.
- Until answered: Accept both as optional event types; the cancel check also asks the Claims Service for the claim status. Only CLAIM_ACCEPTED completes the authorization.

**BA-22** · Sprint 2 · by 2 Nov · UC009, UC011 table 011-02, UC015, UC016 ([PALJ-13](https://leansa.atlassian.net/browse/PALJ-13), UC_009; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

Claims Service: is it the future Claims module of this platform or an existing service? Who owns it, and when can we get the interface for its events (009-01) and the linked-claims lookup (011-02)?

> UC009 BR006: "وتحدد بنيتها في جدول البيانات 009-01 كواجهة متفق عليها بين الخدمتين"

- Why it matters: Reopen is blocked when the lookup fails, and authorizations complete only from claim events.
- Until answered: An adapter with a simulator until the interface is known.

**BA-23** · Sprint 2 · by 2 Nov · UC011 BR008 vs ALT003, ALT007, MSG004, MSG007 ([PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

BR008 says there is no maximum number of reopens, but ALT003, ALT007, MSG004 and MSG007 refer to a maximum period, a maximum count and a count above which a supporting document is mandatory. Which applies, and what are the values?

> UC011 BR008: "لا يوجد حد اقصى لاعادة فتح الموافقة من المركز" · ALT003: "تجاوز عدد مرات إعادة الفتح المسموح بها"

- Why it matters: The story contradicts itself.
- Until answered: No limit by default (configurable); the reopen counter is always kept.

**BA-24** · Sprint 2 · by 2 Nov · UC011 conflict check ([PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

Before reopening, the system checks for another authorization for the same beneficiary, provider and month that is not cancelled (Completed included). This is stricter than the duplicate rule at submission (Active and not discharged). Please confirm.

> UC011 BR007: "لا يقبل إعادة الفتح إذا وجدت موافقة أخرى غير ملغاة لنفس المستفيد لدى نفس مزود الخدمة في نفس شهر الموافقة الأصلية"

- Why it matters: It decides whether some reopens are blocked.
- Until answered: As written: any non-cancelled authorization blocks the reopen.

**BA-25** · Sprint 2 · by 2 Nov · UC010 BR007 ([PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010)

Providers cannot cancel. Please confirm they ask the Center (support) to cancel, and whether that request needs to be recorded in the system.

- Why it matters: Decides whether we need a "cancellation request" flow for providers.
- Until answered: Handled outside the system; the Center Admin cancels on the portal with a reason.

### Notifications (all use cases)

**BA-26** · Sprint 1 · by 15 Oct · UC012 BR005, UC011 BR010, UC016 BR004 ([PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

The stories disagree: UC016 BR004 says automatic updates, cancel, reopen and reassign are not notified, while UC012 BR005 (reassign) and UC011 BR010 (reopen) say the provider and TPA are notified. Which is correct?

> UC016 BR004: "التغييرات التي لا يرسل النظام إشعارا بها: ... وإعادة الفتح "UC011"، وإعادة التعيين "UC012"" · UC011 BR010: "ويرسل النظام إشعار لمزود الخدمة وشركة إدارة المطالبات بتحديث الحالة"

- Why it matters: Partners build their integration around which events they receive.
- Until answered: No notification for automatic updates and cancel; notify on reopen and reassign, behind a switch.

**BA-27** · Sprint 1 · by 15 Oct · All UCs with notifications ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-8](https://leansa.atlassian.net/browse/PALJ-8), UC_004; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005; [PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006)

Please confirm the complete event → recipient list (which events notify the provider, the TPA, or both), including the delayed verification results in BA-07.

- Why it matters: One table drives every notification; gaps mean missing or extra messages.
- Until answered: Notify the assigned TPA and the provider on the events the stories list; nothing else.

### Action log, coverage form and retention (UC013, UC014)

**BA-28** · Sprint 2 · by 2 Nov · UC014 BR009, BR010 ([PALJ-18](https://leansa.atlassian.net/browse/PALJ-18), UC_014; [PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

The action list does not include: a delayed verification finishing, a difference between the provider's patient data and Yaqeen's (UC001 BR005), and a failed notification delivery. Other stories say some of these are logged. Should all of them be added?

> UC001 ALT006 (Yaqeen result): "ويسجل ذلك في سجل النشاطات"

- Why it matters: The action log is permanent; missing entries cannot be added later.
- Until answered: Log all three.

**BA-29** · Sprint 3 · by 16 Nov · UC013 ([PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

The coverage decision form has fields still marked "to be set by the business": Transaction Type, Episode ID, Period From / To and Adjudication Reason. Please provide them and sign off the two layouts (authorization and sub-request, Arabic and English).

> 013-01 Transaction Type: "يحدد من قطاع الأعمال"

- Why it matters: The form cannot be finished without them.
- Until answered: Build both layouts with placeholders for these fields.

**BA-30** · Sprint 3 · by 16 Nov · Gap #9, UC014 ([PALJ-18](https://leansa.atlassian.net/browse/PALJ-18), UC_014)

Is permanent retention of authorizations, the action log and attachments confirmed? Is there a legal maximum retention period?

> UC014 BR004: "ويحتفظ به طوال عمر الموافقة وبعد إقفالها أو إلغائها وفق سياسة الاحتفاظ بالبيانات"

- Why it matters: Drives storage and archiving design.
- Until answered: Permanent, with older data moved to cheaper storage.

## Handled by the technical team

For information; no business answer needed.

- CNHI_ID algorithm (CNHI + YY + 8 digits, unique, non-sequential, with a check digit): a proposal is ready in code.
- Request limits per partner system per minute (UC002 BR005, UC016 BR006).
- Retry counts and intervals for notifications.
- Storage technology for attachments.

Source: user stories in `docs/stories/`, BRD v1.1, development plan v0.5 section 15, backend team Sprint 0 questions (Q1–Q13).
