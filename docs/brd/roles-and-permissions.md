# Roles and Permissions

Source: BRD v1.1, sections "صلاحيات المستخدمين" and "مصفوفة الصلاحيات: الإجراء والجهة المخولة".

## 1. Parties connected via integration (system-to-system)

### Provider system (نظام مزود الخدمة)

- Submit a medical authorization request, stating the admission type (referral / direct admission) and the beneficiary identity.
- Inquire about its own authorization requests, their statuses and change history.
- Receive automatic notifications on every decision and status change on its requests.
- Reply to the TPA with a note and the requested documents; change service quantities, delete or add services on a returned sub-request; correct referral, admission and diagnosis data on the first return.
- Add new services to the authorization during care.
- Update the authorization with the beneficiary's discharge, or with continued treatment into the next month.
- Stand-alone eligibility inquiry before submitting.
- Ask for a sub-request still pending TPA review to be returned to it to complete missing items.

### TPA system (نظام شركة ادارة المطالبات)

- Submit a medical authorization request, stating the admission type (referral / direct admission / **already admitted**) and the beneficiary identity, for the purpose of an **advanced authorization**.
- View the authorizations submitted by the providers assigned to it, and their statuses.
- Receive automatic notifications when a new request, a provider reply or an additional-services request arrives.
- Decide on the authorization (approve / partial approve / reject / return for missing information) with details per service, after viewing the request and the eligibility status.
- Review the provider's reply and the attached documents.
- Cancel an authorization.
- Review additional-services requests and decide on them.
- Decide on a provider's request to return a sub-request (accept or reject).

## 2. Users on the Seha platform (screens)

| User | Permissions |
|---|---|
| Provider user (مستخدم مزود الخدمة) | View its facility's authorizations, their statuses and the TPA decisions. Update the authorization with the beneficiary's discharge from the platform screen. Print the coverage decision form for the full authorization. |
| TPA user (مستخدم شركة ادارة المطالبات) | View the authorizations submitted by the providers assigned to it, their statuses and decisions. |
| CNHI user – Admin (مستخدم المركز – مدير) | View all authorizations, statuses and TPA decisions. Reopen authorizations after closure. Cancel an authorization. View the operations log and the delivery status of automatic notifications to the parties. |
| CNHI user – General (مستخدم المركز – عام) | View all authorizations, statuses and TPA decisions. |

## 3. Action × authorized party matrix

| Action | Authorized party | Channel | Use case |
|---|---|---|---|
| Submit a medical authorization request | Provider system; TPA system (advanced authorization) | API | UC001 |
| Stand-alone eligibility inquiry | Provider system; TPA system | API | UC002 |
| Decide on a sub-request | TPA system | API | UC003 |
| Reply to missing information | Provider system | API | UC004 |
| Submit an additional sub-request | Provider system | API | UC005 |
| Request a sub-request return / reject the return request | Provider system (request); TPA system (reject, return, or decide) | API | UC006, UC003 |
| Record beneficiary discharge | Provider system (API); provider user (screen) | API / Screen | UC007, UC008 |
| Cancel the authorization | CNHI Admin user (screen); TPA system (API) | Screen / API | UC010 |
| Reopen the authorization | CNHI Admin user | Screen | UC011 |
| Reassign the sub-request to the TPA | CNHI Admin user | Screen | UC012 |
| Print the coverage decision form | Provider user, TPA user, CNHI users | Screen | UC013 |
| View, search and action log | All three user types, each within its scope (CNHI Viewer user is view only) | Screen | UC014, UC015 |
| System inquiry | Provider system; TPA system | API | UC016 |

## Notes for planning

- The permissions list in section 2 and the matrix in section 3 do not fully agree: the matrix lets the TPA user and **all** CNHI users print (UC013) and lets the CNHI Admin reassign (UC012), while section 2 lists the TPA user and CNHI General as view only and does not list reassign for the Admin. The out-of-scope section also says provider users are view only, but they can record a discharge (UC008) and print (UC013). Logged as open questions; see `docs/plans/sprint-0/brd-context-and-business-gaps.md`.
- The matrix names a "CNHI Viewer" user; section 2 calls the same role "CNHI user – General".
