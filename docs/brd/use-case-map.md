# Use-Case Map — 4 Feature Groups, 16 Use Cases

Source: BRD v1.1, section "حالات الاستخدام", diagram "تقسيم حالات الاستخدام (16 حالة)". Original image: [flows/use-case-map.png](flows/use-case-map.png).

**Legend (from the diagram):** an **executes** party carries out the use case (filled chip); an **affected** party is affected by it or notified (outlined chip). Parties: Provider, TPA, CNHI (المركز), System (النظام).

## 1. Submission and decision (التقديم والقرار)

| UC | Title | Summary (from diagram) | Executes | Affected | Jira |
|---|---|---|---|---|---|
| 001 | Submit a medical authorization request (تقديم طلب موافقة طبية) | Verify identity and eligibility, and create the authorization | Provider, TPA | — | PALJ-2 |
| 002 | Stand-alone eligibility inquiry (الاستعلام المستقل عن الأهلية) | Early eligibility check before submitting | Provider, TPA | — | PALJ-5 |
| 003 | Receive the TPA decision (استقبال قرار شركة إدارة المطالبات) | Approve, partial approve, reject, or return for missing information | TPA | Provider | PALJ-7 |
| 004 | Reply to a request returned for missing information (الرد على طلب معاد للنواقص) | Complete what was requested within 7 days | Provider | TPA | PALJ-8 |
| 005 | Add a sub-request to an existing authorization (إضافة طلب فرعي لموافقة قائمة) | Additional services on the same authorization | Provider | TPA | PALJ-9 |
| 006 | Request a sub-request return from the TPA (طلب إعادة الطلب الفرعي من الشركة) | The provider asks for it before the TPA decides, to complete something it forgot | Provider | TPA | PALJ-10 |

## 2. Closure and automatic updates (الإقفال والتحديث الآلي)

| UC | Title | Summary (from diagram) | Executes | Affected | Jira |
|---|---|---|---|---|---|
| 007 | Update the authorization with the beneficiary's discharge (تحديث الموافقة بخروج المستفيد) | Closes the treatment period and starts the claim deadline | Provider | — | PALJ-11 |
| 008 | Record discharge from the platform screen (تسجيل الخروج من شاشة المنصة) | Same effect as UC007, from the screen, for the provider user | Provider | — | PALJ-12 |
| 009 | Automatic authorization status updates (التحديث الآلي لحالات الموافقة) | Monthly closure, deadlines and claim events | System | — | PALJ-13 |

## 3. CNHI and TPA interventions (تدخلات المركز والشركة)

| UC | Title | Summary (from diagram) | Executes | Affected | Jira |
|---|---|---|---|---|---|
| 010 | Cancel a medical authorization (إلغاء موافقة طبية) | From screens or integration, with a documented reason | CNHI, TPA | Provider | PALJ-14 |
| 011 | Reopen a medical authorization (إعادة فتح موافقة طبية) | For a cancelled authorization or one whose claims were processed | CNHI | — | PALJ-15 |
| 012 | Reassign a returned sub-request (إعادة تعيين طلب فرعي معاد) | Send the request back to the TPA to decide | CNHI | Provider, TPA | PALJ-16 |

## 4. Supporting services (خدمات مساندة)

| UC | Title | Summary (from diagram) | Executes | Affected | Jira |
|---|---|---|---|---|---|
| 013 | Print the coverage decision form (طباعة نموذج قرار التغطية) | For the full authorization, Arabic or English | TPA, CNHI, Provider | — | PALJ-17 |
| 014 | Action log (سجل النشاطات) | Every action by every party, for every user with view permission | System, TPA | CNHI | PALJ-18 |
| 015 | View medical authorization requests (الاطلاع على طلبات الموافقات الطبية) | Search and read-only view from screens, each within its scope | TPA, CNHI, Provider | — | PALJ-19 |
| 016 | System inquiry about medical authorizations (الاستعلام النظامي عن الموافقات الطبية) | Detailed or list inquiry via APIs within the calling system's scope | TPA, Provider | System | PALJ-20 |

## Notes

- Executes/affected parties are transcribed exactly as the diagram shows them. Two look odd and should not be read as permissions: UC014 shows the Provider as neither executing nor affected, and shows CNHI only as affected. For who may do what, use [roles-and-permissions.md](roles-and-permissions.md).
- Jira keys come from the folder names under `docs/stories/`.
