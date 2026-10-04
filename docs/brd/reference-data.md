# Reference Data — Statuses, Transitions, SLAs, Code Lists, Numbering

Source: BRD v1.1, section "انتقالات حالة الطلب الفرعي" and the appendices section "الملحقات". Codes and values are exactly as written in the BRD.

## 1. Main authorization statuses

| Status | Description |
|---|---|
| Active (نشط) | Created and open from creation until the claim is accepted or the authorization is cancelled; accepts new sub-requests and TPA decisions. |
| Completed (مكتملة) | Its linked claim was accepted and its sub-requests under review were cancelled; accepts no action except reopen, view and print. |
| Cancelled (ملغى) | Cancelled by CNHI or the TPA, or automatically when the claim deadline passes; **not counted** in the one-authorization-per-month rule; accepts no action except reopen by CNHI, view and print. |

## 2. Sub-request statuses

| Status | Description |
|---|---|
| Under Review – Pending TPA Review (تحت الدراسة – بإنتظار مراجعة شركة إدارة المطالبات) | Created (the first one with the authorization, or an additional one), or sent back to the TPA after the provider's reply or a CNHI reassignment; waiting for the TPA's decision on its services. |
| Under Review – Return Requested by Provider (تحت الدراسة – مطلوب إعادة الطلب من المزود) | The provider asked for the sub-request to be returned to it before the TPA decides; waiting for the TPA to return it, give a final decision, or reject the return request (it then goes back to Pending TPA Review). **No new sub-request is accepted on the authorization while it is in this status.** |
| Under Review – Pending Provider Response (تحت الدراسة – بإنتظار رد مزود الخدمة) | The TPA returned it for missing information; waits for the provider's reply within 7 days or is cancelled automatically; CNHI can reassign it to the TPA before then. **No new sub-request is accepted on the authorization while it is in this status.** |
| Approved (مقبول) | The TPA approved all services at the requested quantities; its services appear with their status on the coverage decision form for the full authorization. |
| Partially Approved (مقبول جزئيا) | The TPA rejected some services or reduced the approved quantity of some; shown on the coverage form. |
| Rejected (مرفوض) | The TPA rejected all services; shown on the coverage form. |
| Cancelled (ملغى) | Cancelled automatically when the provider's reply deadline passed, or because the main authorization was cancelled, completed by claim acceptance, or its claim deadline passed while the sub-request was under review. **Not restored** when the authorization is reopened. |

The appendix gives the Return Requested status no code. UC_006 uses `RETURN_REQUESTED`; the other statuses use `PENDING_TPA_REVIEW`, `PENDING_PROVIDER_RESPONSE`, `APPROVED`, `PARTIALLY_APPROVED`, `REJECTED`, `CANCELLED` in the data tables.

## 3. Main authorization status transitions

| From | To | Trigger | Use case |
|---|---|---|---|
| — | Active | Authorization created | UC001 |
| Active | Active (validity ended) | End of calendar month or discharge recorded (validity-end flag, **no status change**) | UC007, UC009, UC008 |
| Active | Completed | Linked claim accepted | UC009 ALT002 |
| Active | Cancelled | Cancelled by CNHI or the TPA, or automatic cancellation after 90 days with no claim | UC010, UC009 ALT003 |
| Cancelled | Active | Reopened by CNHI | UC011 |
| Completed | Active | Reopened by CNHI | UC011 |

## 4. Sub-request status transitions

| From | To | Trigger | Use case |
|---|---|---|---|
| — | Pending TPA review | Authorization created, or an additional sub-request submitted | UC001, UC005 |
| Pending TPA review | Return requested by provider | Provider asks for the sub-request to be returned to it | UC006 |
| Return requested by provider | Pending TPA review | TPA rejects the return request | UC006 |
| Pending TPA review / Return requested by provider | Pending provider response | TPA decides to return for missing information | UC003 |
| Pending TPA review / Return requested by provider | Approved / Partially approved / Rejected | TPA final decision | UC003 |
| Pending provider response | Pending TPA review | Provider replies, or CNHI reassigns | UC004, UC012 |
| Pending provider response | Cancelled | 7 days pass with no provider reply | UC009 |
| Any under-review status | Cancelled | Main authorization cancelled, completed, or auto-cancelled | UC010, UC009 |
| Cancelled | — | A cancelled sub-request is not restored when the authorization is reopened | UC011 |

## 5. SLAs and deadlines

| Deadline | Duration | Starts from |
|---|---|---|
| Provider reply to a request returned for missing information | 7 calendar days | Date the TPA returned the request |
| Main authorization validity | Until the end of `AUTHORIZATION_MONTH` or discharge, whichever comes first | Start of the authorization month (the creation date may be earlier) |
| Claim submission | 90 calendar days | Validity end date (end of calendar month or discharge), or the reopen date. Counted on the active authorization; stops when a claim is submitted; restarts when the claim is rejected or cancelled; no longer applies once the authorization is cancelled |

No SLA is defined for the TPA's decision.

## 6. Code lists

### Beneficiary ID type (أنواع هوية المستفيد)
| Code | Value |
|---|---|
| National ID | هوية وطنية |
| IQAMA | إقامة |
| Border Number | رقم حدود |
| Visa Number | رقم تأشيرة |
| Passport | رقم جواز |
| Identity Number for Unknowns | Special ID for unidentified patients (the system generates a CNHI_ID) |
| Newborn | Newborn (the system generates a CNHI_ID) |

### Beneficiary eligibility status (حالة أهلية المستفيد)
| Code | Value |
|---|---|
| ELIGIBLE | مؤهل |
| PARTIALLY_ELIGIBLE | مؤهل جزئيا |
| NOT_ELIGIBLE | غير مؤهل |
| UNIDENTIFIED | غير معرف |
| PENDING_VERIFICATION | بانتظار التحقق: the eligibility engine could not be reached at submission; retried automatically |

### Access type (نوع الدخول)
| Code | Value |
|---|---|
| Referral | إحالة |
| Direct Admission | دخول مباشر |
| Already Admitted | منوم مسبقا |

### Direct admission reasons (أسباب الدخول المباشر), used by `ACCESS_MODE` in 001-01
| Code | Value |
|---|---|
| DA01 | Transfer by the Red Crescent (نقل من خلال الهلال الأحمر) |
| DA | New referral (إحالة جديدة) |
| DA02 | Direct admission from the emergency department (دخول مباشر من الطوارئ) |
| DA07 | Deterioration of the medical condition (تدهور الحالة الطبية) |
| DA08 | Newborn (مولود جديد) |
| DA12 | Transfer from outpatient clinics to inpatient (تحويل من العيادات الخارجية للتنويم) |
| DA13 | Readmission (إعادة تنويم) |
| DA14 | No financial coverage (لاتوجد تغطية مالية) |

### Encounter class (طبيعة الزيارة)
| Code | Value |
|---|---|
| AMB | Outpatient clinics (عيادات خارجية) |
| EMER | Emergency (طوارئ) |
| HH | Home health (صحة منزلية) |
| IMP | Inpatient (تنويم) |
| SS | Short stay (إقامة قصيرة) |
| VR | Virtual care (رعاية عن بعد) |
| ACUTE | Acute inpatient (تنويم حاد) |

### TPA decision on a sub-request (قرار شركة إدارة المطالبات على الطلب الفرعي)
| Code | Value |
|---|---|
| APPROVED | قبول |
| REJECTED | رفض |
| PARTIAL | قبول جزئي |
| RETURNED | إعادة لاستكمال النواقص |

### Service rejection reasons (أسباب رفض الخدمة)
| Code | Value |
|---|---|
| RJ01 | Medically stable (الحالة مستقرة طبيا) |
| RJ02 | Wrong procedure followed (تم اتباع إجراءات خاطئة) |
| RJ03 | Patient demographically ineligible for treatment (عدم أهلية علاج المريض الديموغرافية) |
| RJ04 | Patient has other insurance coverage (المريض لديه تغطية تأمينية أخرى) |
| RJ05 | Service not purchased by CNHI (الخدمة لا يتم شراؤها من المركز) |
| RJ06 | Duplicate authorization request (طلب موافقة مكرر) |
| RJ99 | Other, with a text reason (أخرى) |

### Document and attachment types (أنواع المستندات والمرفقات)
| Code | Value |
|---|---|
| MED_REPORT | Medical report |
| LAB | Lab results |
| RAD_REPORT | Radiology report |
| RAD_IMAGE | Radiology images |
| OP_REPORT | Operation or procedure report |
| RX | Prescription |
| DISCHARGE_SUMMARY | Discharge summary |
| REFERRAL_FORM | Referral form |
| ID_COPY | ID copy |
| RED_CRESENT | Red Crescent report |
| OTHER | Other |

### Discharge disposition (قرار خروج المستفيد)
| Code | Value |
|---|---|
| LAMA | Left against medical advice (غادر ضد نصيحة الطبيب) |
| home | Home / other (المنزل/أخرى) |
| acute-hospital | Transfer to an acute care hospital |
| SDTC | Statistical discharge – change of care type |
| died | Died in hospital |
| DTRAS | Transfer to residential aged care |
| DTPH | Transfer to a psychiatric hospital |
| DTOHA | Transfer to another healthcare place |
| SDFL | Statistical discharge |
| DAMA | Discharged against medical advice (خرج ضد النصيحة الطبية) |

### Authorization cancellation reasons (أسباب إلغاء الموافقة)
| Code | Value |
|---|---|
| CR01 | Duplicate request |
| CR02 | Wrong beneficiary data |
| CR03 | Submitted by mistake by the provider |
| CR04 | Service was not given to the beneficiary |
| CR05 | At the provider's request |
| CR06 | Cancelled automatically after 90 days with no claim (set by the system only; not available to users or the TPA) |
| CR99 | Other, with a text reason |

### Authorization reopen reasons (أسباب إعادة فتح الموافقة)
| Code | Value |
|---|---|
| RO01 | Cancelled by mistake |
| RO02 | Claim submitted late for an acceptable reason |
| RO03 | Services not covered by the processed claim |
| RO04 | Resubmitting a rejected claim |
| RO05 | Committee decision or settlement |
| RO06 | At the provider's request |
| RO99 | Other, with a text reason |

### Sub-request reassignment reasons (أسباب إعادة تعيين الطلب الفرعي)
| Code | Value |
|---|---|
| RA01 | Provider did not respond |
| RA02 | Unjustified information request from the TPA |
| RA03 | Emergency case that needs a decision |
| RA04 | At the provider's request |
| RA05 | At the TPA's request |
| RA99 | Other, with a text reason |

### Provider's sub-request return reasons (أسباب طلب إعادة الطلب الفرعي من مزود الخدمة)
| Code | Value |
|---|---|
| RR01 | Missing attachments not sent with the sub-request |
| RR02 | Missing services not added to the sub-request |
| RR03 | Correct quantities or replace services |
| RR04 | Correct referral, admission or diagnosis data |
| RR99 | Other, with a text reason |

### Yaqeen verification status (حالات التحقق من يقين)
| Code | Value |
|---|---|
| VERIFIED | Verified: beneficiary data comes from Yaqeen |
| PENDING_VERIFICATION | Pending: Yaqeen could not be reached at submission; retried automatically |
| NOT_VERIFIED | Not verified: the ID matched no data in Yaqeen |

### Actor type in the action log (الجهة المنفذة في سجل النشاطات)
| Code | Value |
|---|---|
| CENTER | CNHI (المركز) |
| PROVIDER | Provider (مزود الخدمة) |
| TPA | TPA (شركة إدارة المطالبات) |
| SYSTEM | System (النظام) |

### Service change type in the provider's reply (نوع تغيير الخدمة في رد مزود الخدمة)
| Code | Value |
|---|---|
| UNCHANGED | No change |
| QUANTITY_MODIFIED | Quantity changed |
| REPLACED | Service replaced |
| DELETED | Service deleted |
| ADDED | Service added |

### Triage category CTAS (فئة الفرز)
| Level | Description |
|---|---|
| 1 | Resuscitation (إنعاش) |
| 2 | Emergent (طارئ) |
| 3 | Urgent (عاجل) |
| 4 | Less Urgent (أقل عجلة) |
| 5 | Non-Urgent (غير عاجل) |

## 7. Attachment rules (قواعد المرفقات)

| Rule | Value |
|---|---|
| Allowed formats | PDF, JPG, PNG |
| Maximum size per file | 5 MB |
| Maximum files per action | 10 |
| Retention | Same as the action log, per CNHI's data retention policy |
| Applies to | All attachment tables: 001-01, 003-01, 004-01, 005-01 and their notifications. Files are sent as Base64 with the document type and file name |

## 8. Numbering formats (صيغ الترقيم)

| Item | Format | Example | Notes |
|---|---|---|---|
| Main authorization number | `MA-YYYY-NNNNNNNN` | `MA-2026-00012345` | MA = Medical Authorization; calendar year; 8-digit sequence that resets every year |
| Sub-request number | `<authorization no.>-NN` | `MA-2026-00012345-01` | 2-digit sequence within the authorization; the first request is 01 |
| CNHI_ID | `CNHI` + `YY` + `NNNNNNNN` | `CNHI2664412975` | `CNHI` is fixed; `YY` is the last two digits of the calendar year the ID is generated; `NNNNNNNN` is 8 digits from an approved algorithm set by the technical team (not ascending, not random) that guarantees no duplicates and can be validated; generated for an unidentified patient or a newborn (UC001 BR007) |
