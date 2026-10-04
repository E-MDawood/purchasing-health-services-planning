# Sprint 0 Business Questions

## PALJ-2 - UC_001 Submit Medical Authorization Request

- **Q1. Provider to TPA routing.** How is the TPA for a provider decided, and who maintains this link? We are assuming each provider has exactly one assigned TPA, set up as reference data by the technical team, with no maintenance screen in this phase. If a provider can have more than one TPA (for example by region or service type), please share the rule.
- **Q2. Admission fields.** The reference list in the requirements matches the create request ("Access Type": Referral, Direct Admission, Already Admitted; "Access Mode": DA01, DA, DA02, DA07, DA08, DA12, DA13, DA14), so we are assuming this is the current set. Still open:
  - The request sent to the TPA and the provider reply still use "Admission Type" and "Direct Admission Reason" with the old codes DA01 to DA13. We are assuming they should change to the new field names and codes. Is that correct?
  - What do the codes "DA" (new referral) and DA14 (no financial coverage) mean, and why is a referral code in the list of direct admission reasons?
  - "Access Mode" is mandatory for every access type. Should it also be sent when the access type is Referral?
  - The user permissions section and the glossary mention only Referral and Direct Admission for providers, and list "Already Admitted" only for the TPA's advanced authorization. Can a provider send "Already Admitted", and what does it mean?
- **Q3. First request for a newborn or an unidentified patient.** The patient ID number is mandatory, but these patients have no CNHI ID yet on their first request. What should the provider send in that field? We are assuming the provider sends its own file number, and the system returns the new CNHI ID in the response.
- **Q4. Visit fields.**
  - "Visit Class" (outpatient, emergency, inpatient and so on) and "Emergency Department Disposition" are sent to the TPA, but they are not in the create request. The triage rule also depends on Visit Class being "Emergency". Should the provider send both fields in the create request?
  - What does the new "Visit Date" field mean, and how is it different from the admission date?
- **Q5. Total amount fields.** The total amount has different names in different tables (for example "TPA Amount" in the create response, and "Net Amount" holding the after-tax total in the request sent to the TPA). Can we use one consistent set of names everywhere: total before tax, total tax, total after tax?
- **Q6. VAT rate.** Which VAT rate applies, and how is a service marked as tax-exempt? Is this information part of the price list?
- **Q7. Price list.** Prices come from a CNHI list by provider category. Where does this list come from, how are new versions published, and which version applies to a request: the one valid on the request date, or on the admission date?
- **Q8. Admission date check.** Besides "admission date cannot be in the future", should the system also reject an admission date that is before the referral date?
- **Q9. Verification result notifications.** When a delayed Yaqeen, eligibility or referral check finishes, the provider and the TPA are notified. What information should this notification contain?
- **Q10. Failed Yaqeen check.** When the Yaqeen check ends as "not verified", the requirement says CNHI takes the appropriate action. What is that action, and does the request continue as normal in the meantime?
- **Q11. Patient nationality.** The create request marks "Patient Nationality" as mandatory, but the note on the same field says it is not required when the patient's identity is unknown. We are assuming it is mandatory for all ID types except "Identity Number for Unknowns". Should it also be optional for a newborn?
- **Q12. Referral date.** The create request marks "Referral Date" as mandatory from the provider, but the referral rules say the date from the referral distribution system is used when the two differ. We are assuming the provider's date is kept only as a fallback while the referral check is pending, and is replaced by the referral system's date once the check succeeds. Is that correct, and should the provider still be required to send it?
- **Q13. Business process diagram.** The business process diagram does not match the use cases and the status lists:
  - It moves the main authorization to "Pending claim submission" at month end and to "Claim processed by the TPA" after the claim. The status list has only Active, Completed and Cancelled, and says the authorization stays Active until its claim is accepted.
  - It shows the provider filling in the referral data, while the use case retrieves it from the referral distribution system.
  - It shows the Yaqeen and eligibility checks as steps that must finish, while the use case continues without waiting for them.

  We are assuming the use cases and the status lists and transitions are current and the diagram is out of date. Can the diagram be updated?

## PALJ-18 - UC_014 Action Log

- **Q14. Actions to record.** The list of recorded actions does not include:
  - a Yaqeen, eligibility or referral verification finishing
  - a difference between the provider's patient data and Yaqeen's data
  - a notification that failed to deliver

  Other parts of the requirements say some of these should be logged. Should all of them be added to the list?
