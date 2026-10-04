# Business Process Flow

Source: BRD v1.1, section "إجراء العمل". BPMN diagram with three swimlanes inside the pool **"نظام شراء الخدمات الصحية في الداخل"** (in-Kingdom health services purchasing system): **Provider (مزود الخدمة)**, **System (النظام)**, **TPA (شركة إدارة المطالبات)**.

Diagram files: [flows/business-process-flow.png](flows/business-process-flow.png) (rendered), [flows/business-process-flow.vsdx](flows/business-process-flow.vsdx) (original Visio, embedded in the BRD), [flows/business-process-flow.drawio](flows/business-process-flow.drawio) (editable).

> ⚠️ This diagram is older than the use cases. It leaves out the referral integration, the asynchronous Yaqeen, eligibility and referral checks, "Already Admitted", UC006, discharge, cancel, reopen and reassign, and it uses main-authorization statuses that are not in the status list. See "Differences from the use cases" below. For behavior, the use cases and [reference-data.md](reference-data.md) take precedence.

## Flow as text

### 1. Submission
1. **Provider:** start → *Submit a medical authorization request* (رفع طلب موافقة طبية).
2. **Provider, gateway "Admission type" (نوع الدخول):**
   - *Referral* → **Provider:** *Fill in the referral information* (تعبئة معلومات الاحالة) → step 3.
   - *Direct admission* → step 3.
3. **System, gateway "Beneficiary identity" (هوية المستفيد):**
   - *National ID or Iqama* → **System:** *Integrate with Yaqeen to retrieve the beneficiary data* (sub-process) → step 4.
   - *Any ID type other than National ID or Iqama* → step 4.
4. **System:** *Integrate with the eligibility engine and add the beneficiary's eligibility to the authorization request* (sub-process).
5. **System:** *Create a main authorization with status "Active" and a sub-request with status "Under review – Pending TPA review"*.
   Annotation: main status **Active**; sub-request status **Under review – Pending TPA review**.

### 2. TPA review cycle
6. **TPA:** *View the request* (الاطلاع على الطلب).
7. **TPA, gateway "TPA decision" (قرار شركة إدارة المطالبات):**
   - *Return the request to the provider for information* (إعادة الطلب لمزود الخدمات للإفادة) → **System:** *Notify the provider that the authorization was responded to*.
     Annotation: main **Active**; sub-request **Under review – Pending provider response**.
     → **Provider:** *Reply to the TPA* (الرد على شركة إدارة المطالبات), with a **7-day timer** on the task:
       - reply sent → back to step 6;
       - 7 days pass with no reply → **System:** *Update the request status to "Cancelled"* → end.
   - *Approve, partial approve, or reject* (القبول، القبول الجزئي، الرفض) → **System:** *Notify the provider that the authorization was decided and update the request status to "Approved", "Partially approved" or "Rejected"*.
     Annotation: main **Active**; sub-request **Approved, Partially approved or Rejected** → step 8.

### 3. Care and additional services
8. **Provider:** *Provide healthcare* (sub-process).
9. **Provider, gateway "Are additional services needed during care?":**
   - *Yes* → **Provider:** *Submit a new sub-request to the TPA for review* → **System:** *Create the sub-request with status "Under review – Pending TPA review" and submit it to the TPA*.
     Annotation: main **Active**; new sub-request **Under review – Pending TPA review** → back to step 6.
   - *No* → timer event **"End of the current month"** (اكتمال الشهر) → step 10.

### 4. Closure
10. **System:** *Update the main request status*. Annotation: main status **"Pending claim submission"** (بإنتظار رفع المطالبة المالية).
11. **System, event-based gateway:**
    - *90 days pass with no claim submitted* → **System:** *Update the request status to "Cancelled"* → end.
    - *Claim submitted and completion certificate issued* (رفع مطالبة مالية وصدور شهادة الإنجاز) → **System:** *Update the request status to "Claim processed by the TPA"* (تم معالجة المطالبة المالية من شركة إدارة المطالبات) → **System:** *Cancel any sub-request still under review* → end.

## Differences from the use cases (logged as gaps)

| Diagram | Use cases / appendices |
|---|---|
| Main status changes to "Pending claim submission" at month end | Main status stays **ACTIVE**; only the validity-end flag is set (UC009, main status transitions) |
| Main status "Claim processed by the TPA" | Main status **COMPLETED** when the claim is **accepted** (UC009) |
| Provider fills in the referral information | Referral data is retrieved from the referral distribution system (UC001 table 001-06, BR013) |
| Yaqeen and eligibility are synchronous steps | Both are asynchronous with a `PENDING_VERIFICATION` flag and retries (UC001 ALT006, ALT007) |
| Only Referral and Direct admission | Access types also include **Already Admitted** |
| Not shown | Advanced authorization by the TPA, UC006 return request, discharge (UC007/UC008), cancel, reopen, reassign (UC010 to UC012), 20-cycle return limit |
