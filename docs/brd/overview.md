# BRD Overview

Source: BRD v1.1, section "نظرة عامة على الوثيقة".

## Document history

| Prepared by | Date | Version | Notes |
|---|---|---|---|
| عمر السكيتي | 30/8/2026 | 1.0 | Document created |
| عمر السكيتي | 28/9/2026 | 1.1 | Updated per the meeting with the business sector; technical team's comments reflected |

**Approvals** (signature and date left blank in the document):

| Name | Position |
|---|---|
| شهد الدخيّل | Payment & Costing Technologies Senior Manager |
| سلطان المطيري | Private Health Services Purchasing Senior Director |

## Purpose

Define the requirements for automating the management of in-Kingdom treatment authorizations and claims on the **Seha platform**, for the **medical authorizations** scope, giving the development team a clear and complete understanding of the system's features so it can build a solution that meets the needs of CNHI, the service providers and the TPA.

## In scope

| Scope item | Definition |
|---|---|
| Technical integration services | Integration services that let providers and the TPA run every step of the authorization cycle from their own internal systems, with the data sent, data returned and error messages defined for each operation. |
| Submit an authorization request | The provider submits a medical authorization request from its internal system via integration. |
| Submit an advanced authorization request | The TPA submits a medical authorization request from its internal system via integration. |
| Retrieve beneficiary data | For direct admission with a National ID or Iqama, integrate with Yaqeen to retrieve the beneficiary data and return it to the provider system; other ID types are handled by a mechanism CNHI defines. |
| Eligibility check | Via integration with the eligibility system, classify the beneficiary as Eligible / Partially Eligible / Not Eligible / Unidentified / Pending Verification. |
| Send the request to the TPA | Send the provider's request to the TPA for review. |
| TPA decision | Receive the TPA decision (approve / partial approve / reject / return for missing information) from its internal system via integration, with the decision detailed per service. |
| Completing requirements | When the TPA returns a request for missing information, the provider replies via integration with a note and documents, can change service quantities, replace, delete or add services on the sub-request, and can correct referral, admission and diagnosis data on the main authorization **on the first return only**. |
| Add new services during care | The provider adds new services to an existing authorization via integration. |
| Discharge update | The provider updates the authorization with the beneficiary's discharge and discharge date, via integration **or from the platform screen by a provider user**. |
| Print the coverage letter | Print the coverage letter form for medical authorizations. |
| End of validity | On discharge or end of the authorization month, the system records the validity end date and starts the claim-submission deadline, **without changing the status**; the authorization stays Active until its claim is accepted. |
| Notifications and alerts | Automatically notify the provider system and the TPA system on every decision and status change. |
| Request inquiry | The provider system inquires about its own requests; the TPA system inquires about the requests assigned to it. |
| View screens for provider and TPA | Screens on Seha that let provider and TPA users view their requests and statuses and perform any actions described in the workflow. |
| CNHI screens | Screens on Seha for CNHI users to view and perform any actions described in the workflow. |
| Stand-alone eligibility inquiry | A separate integration service that lets the provider and TPA systems check eligibility before submitting a request. |
| Request return of a sub-request from the TPA | The provider asks for a sub-request that is still pending TPA review to be returned to it to complete anything missing; the TPA must agree before it is returned. |
| CNHI_ID | Generate a CNHI identifier for an unidentified patient or a newborn when the authorization is created, used in all later operations. |
| Referral distribution integration | For a referral admission, integrate with the referral distribution system to retrieve the referral data. |

## Out of scope

| Scope item | Definition |
|---|---|
| Performing operations from Seha screens for provider and TPA users | The platform does not let provider or TPA users submit requests, issue decisions or update them from screens; their role on the platform is **view only**, and operations are done from their internal systems via integration. |
| Internal systems of external parties | Building or changing the internal systems and screens of providers or the TPA is each party's responsibility. |
| Claims, consolidated invoice and objection window | Submitting the claim linked to the authorization and its review, issuing the GSS consolidated invoice, and the claim objection window. |
| Payments and financial reconciliation, and the Etimad platform | Financial closing form, review completion form, CNHI review chain, completion certificate, and integration with Etimad. |
| Healthcare outside CNHI coverage | Care given after a final rejection is outside CNHI coverage and not tracked; there is no objection to a rejection in this phase (the provider may submit a new sub-request with more justification). |
| Automatic retrieval for other ID types | Yaqeen integration is limited to National ID and Iqama; no automatic retrieval for other ID types. |

## Glossary

| Term | Definition |
|---|---|
| CNHI (المركز) | National Center for Health Insurance, the owner of the system. |
| In-Kingdom treatment authorizations and claims system | The custom-built solution for managing the authorization cycle, claims, payments and financial reconciliation on Etimad. |
| Healthcare facility (المؤسسة الصحية) | Any private healthcare facility in the Kingdom. |
| TPA (شركة إدارة المطالبات) | The party CNHI assigns to a provider to review its authorization requests and decide on them. |
| Technical integration (التكامل التقني) | Direct API link between the CNHI system and the external party's internal system to exchange requests and decisions. |
| Linked party (الجهة المرتبطة) | A provider or TPA whose internal system is registered with CNHI and allowed to perform operations via integration. |
| Medical authorization (الموافقة الطبية) | A request the provider submits before giving care, containing the admission type, beneficiary identity and requested services; the TPA decides on it; it covers one admission until discharge or the end of the current month, whichever comes first. |
| Admission type (نوع الدخول) | Classification of the beneficiary's entry to the provider as referral or direct admission; determines where the request data is retrieved from. |
| Referral (الإحالة) | Admission based on a referral registered in the referral distribution system. |
| Direct admission (الدخول المباشر) | Admission without a referral. |
| Referral distribution system (نظام توزيع الحالات) | The system the purchasing system integrates with to retrieve referral data for a referral admission. |
| Yaqeen (يقين) | The identity verification service used to retrieve beneficiary data for National ID or Iqama holders. |
| Beneficiary eligibility (اهلية المستفيد) | Classification of the beneficiary's entitlement to CNHI coverage at submission: Eligible, Partially Eligible, Not Eligible or Unidentified. |
| Completed authorization (الموافقة المكتملة) | An authorization whose linked claim was accepted; accepts no action except reopen, view and print. |
| End of authorization validity (انتهاء فترة سريان الموافقة) | Discharge date or end of the calendar month, whichever comes first; a date on the active authorization that shows until when sub-requests can be submitted on it. |
| CNHI_ID (معرف المركز) | An ID the system generates for an unidentified patient or newborn, format `CNHI` + `YY` + `NNNNNNNN` (e.g. `CNHI2664412975`), by an approved algorithm; used as the beneficiary ID in all authorization and claim operations. |
| Yaqeen verification flag (مؤشر التحقق من يقين) | Flag on the authorization showing the status of the beneficiary data verification with Yaqeen. |
| Sub-request return request (طلب إعادة الطلب الفرعي) | An action the provider raises on a sub-request still pending TPA review, so that it is returned to the provider to complete anything not provided the first time. |

## Assumptions and dependencies

1. Yaqeen integration is ready.
2. Referral distribution system integration is ready.
3. Eligibility engine integration is ready.
4. Users and roles are ready on the Seha platform.
5. Provider and TPA internal systems are ready to build the integration and to send and receive requests.

## Risks

1. Yaqeen, the referral distribution system or the eligibility engine integration is not ready.
2. **Request stuck in the reply cycle:** repeated back-and-forth between provider and TPA with no limit or SLA blocks the request and delays care.
3. **Discharge not updated:** if the provider system does not send the discharge update via integration, authorizations stay open until the period ends, which affects how accurately claims are linked to them.
4. **External parties late:** providers or the TPA are late building their integration with the CNHI system.
