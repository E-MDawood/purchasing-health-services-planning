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
  - "Visit Class" (outpatient, emergency, inpatient and so on) and "Emergency Department Disposition" are sent to the TPA, and Visit Class is mandatory there, but neither is in the create request. The provider can correct them in its reply to a return, and the triage rule depends on Visit Class being "Emergency". We are assuming both should be added to the create request, Visit Class as mandatory and the disposition as optional. Is that correct?
  - What does "Visit Date" mean, and how is it different from the admission date? Based on the coverage form rules, we are assuming it is the date the patient first arrived (which can fall in an earlier month), while the admission date is when the patient was admitted as an inpatient.
  - "Visit Date" is mandatory in the create request but is not sent to the TPA and is not returned by the authorization inquiry. Should it be included in both?
- **Q5. Total amount fields.** The total after tax has three different names: "TPA Amount" in the create response, "Net Amount" in the requests sent to the TPA, the screens and the inquiry, and "Net Requested Amount" in the provider reply and the add sub-request response. "Net" means before tax on each service line but after tax in these totals. The total before tax appears only in the create response, and the total tax only in the create response and the request sent to the TPA. Can we use one consistent set of names everywhere: total before tax, total tax, total after tax?
  - "Patient Share Amount" appears only in the add sub-request request, with no description, and nowhere else. What is it, and should it also be in the create request, sent to the TPA and shown on screens?
- **Q6. VAT rate and unit price.** The provider sends the net, tax and after-tax amount for each service, and the system checks the calculation.
  - The requirements say the tax is calculated at the approved rate and is zero for exempt services, and that the system checks the calculation, but the list of validations does not include the tax rate. Should the system also reject a request whose tax amount does not match the VAT rate? If so, which rate applies, and how is a service marked as tax-exempt (for example in the price list)? Until this is confirmed, we are assuming the system only checks that the after-tax amount equals the net amount plus tax.
  - The unit price is mandatory in the request, but is described as filled automatically when the service is in the price list. If the provider sends a unit price that differs from the price list, we are assuming the system rejects the request with an error naming the service. Is that correct, or should the system replace it with the price list value?
- **Q7. Price list.** Prices come from a CNHI list by provider category.
  - Where does this list come from (a file, another CNHI system, or data we maintain), and how are new versions published?
  - What are the provider categories, and where is each provider's category kept? The request does not include it, so we are assuming the system holds it for each provider.
  - Providers must send the unit price and amounts for each service, so they also need the price list for their category. How do providers get it? If they do not have it, should the system fill in the price and amounts for services in the price list, and require them from the provider only for Unlisted services?
  - Which version applies: the one valid on the request date or on the admission date? We are assuming the version valid on the date each sub-request is submitted, including later sub-requests and provider replies that change services. Is that correct?
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

- **Q14. Ehalati referral number.** The Ehalati referral number is optional in the create request, and the referral distribution system says it does not always exist, but the request sent to the TPA marks it as mandatory. We are assuming it stays optional everywhere and is left empty in the request to the TPA when there is none. Is that correct?
- **Q15. Patient ID type codes.** The ID types are written with different codes in different places. The create request and the reference list use "National ID", "IQAMA", "Border Number", "Visa Number", "Passport", "Identity Number for Unknowns" and "Newborn". The stand-alone eligibility inquiry uses "NID", "IQAMA", "BORDER", "VISA", "PASSPORT" and "CNHI_ID", and has no "Newborn" or "Identity Number for Unknowns". We are assuming the reference list is the one set of codes for every request providers and TPAs send us, and that a patient who already has a CNHI ID is sent with the "Identity Number for Unknowns" or "Newborn" type and the CNHI ID as the number. Is that correct?
- **Q16. Requested by.** The notification of an advanced authorization sets "Requested By" to "ADVANCED", while the authorization inquiry returns "PROVIDER" or "TPA", and the printed form shows "Pre" or "Advanced". We are assuming the system stores who submitted the request (provider or TPA), shows "ADVANCED" in that notification and "Pre" or "Advanced" on the form. Can one set of values be used everywhere?

## PALJ-18 - UC_014 Action Log

- **Q17. Actions to record.** The list of recorded actions does not include:
  - a Yaqeen, eligibility or referral verification finishing
  - a difference between the provider's patient data and Yaqeen's data
  - a notification that failed to deliver

  Other parts of the requirements say some of these should be logged. Should all of them be added to the list?
