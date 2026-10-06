# Business gaps

1. The create request uses ACCESS_MODE (DA01, DA, DA02, DA07, DA08, DA12, DA13, DA14) but the request sent to the TPA uses DIRECT_ADMISSION_REASON (DA01–DA13). Which list is current? What do "DA" (new referral), DA14 (no financial coverage) and "Already admitted" mean for processing? Should ACCESS_MODE also be sent for a Referral, and can providers send "Already admitted" or only TPAs in an advanced authorization (BRD permissions: "لغرض الموافقة المتقدمة")? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

2. Which VAT rate applies, and how is a service marked as tax-exempt (the tax is defined "وفق النسبة المعتمدة، وتساوي صفرا للخدمات المعفاة")? Is this part of the price list? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

3. Prices come from "قائمة معرفة لدى المركز بأسعار الخدمات بما يتقاطع مع فئة المزود" (UC001 BR011). Where does the list come from, how are new versions published, and which version applies: the one valid on the request date or on the admission date? Where is each provider's category kept, how do providers get the price list to fill in UNIT_PRICE, and if their unit price differs from the list (described as "تلقائي اذا الرمز من قائمة الأسعار"), should the request be rejected or the price replaced? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

4. Please confirm 5 MB per file, 10 files per action and PDF / JPG / PNG. Attachments travel as Base64 (10 × 5 MB ≈ 67 MB in one message), including in notifications and UC016 downloads. Is a total size per message acceptable? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

5. When the Yaqeen check ends as NOT_VERIFIED, the story says "ليتخذ المركز الإجراء المناسب" (UC001 ALT006). What is that action, does the request continue as normal meanwhile, and does the Center need a work list of these cases? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

6. When a delayed Yaqeen, eligibility or referral check finishes, the parties are notified, but no data table defines this notification. What should it contain? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

7. How long do we keep retrying Yaqeen, eligibility and the referral system before the result becomes NOT_VERIFIED (the story says "وفق جدولة يحددها الفريق التقني")? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

8. Can we get the referral distribution system's interface description and test environment? Is PATIENT_MOBILE on the coverage form taken only from referral data (013-01: "ويترك فارغا إن لم يتوفر"), so empty for direct admissions? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

9. REFERRAL_DATE is mandatory from the provider, but the referral system's date replaces it when they differ (UC001 BR013). Is the provider's date only a fallback while the referral check is pending? And the Ehalati referral number is optional in the create request and "لا يوجد دائما" from the referral system, but mandatory in the request sent to the TPA: which is correct? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

10. VISIT_DATE is mandatory in the create request, but it is not sent to the TPA or returned by the authorization inquiry; it only appears on the coverage form. Should the TPA and the inquiry also receive it? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

11. PATIENT_NATIONALITY is mandatory, but its note says "غير الزامي في حال ان المستفيد مجهول الهوية". Is it also optional for a newborn, and which nationality code list should be used? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

12. The same values have different codes in different tables: ID types ("National ID", "Border Number" in 001-01 vs "NID", "BORDER", "CNHI_ID" in 002-01, which has no Newborn or Unknowns type), Requested by ("ADVANCED" in 001-05 vs "PROVIDER" / "TPA" in 016-01 vs "Pre" / "Advanced" on the form), and partial approval ("PARTIAL", "PARTIALLY APPROVED", "PARTIALLY_APPROVED"). Can one set of codes be used for each? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-5](https://leansa.atlassian.net/browse/PALJ-5), UC_002; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

13. What is the TPA decision service level (how many days or hours), and what happens when it is breached? ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003)

14. RETURN_REQUESTED is defined in UC006 but missing from the status lists in table 001-01, UC003 MSG001 and the status filters of UC012, UC015 and UC016. Is it a valid sub-request status everywhere? ([PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006; [PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-19](https://leansa.atlassian.net/browse/PALJ-19), UC_015; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

15. Can main-authorization data be edited on "الإعادة الأولى للطلب الفرعي" (UC001 BR003) or only on "الإعادة الأولى للطلب الفرعي الأول فقط" (UC004 BR006)? And UC005 lets the provider send diagnosis codes with a new sub-request that update the main authorization. Which rule applies? ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-8](https://leansa.atlassian.net/browse/PALJ-8), UC_004; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

16. The TPA sends both DECISION for the sub-request and SERVICE_DECISION per service, while the status is calculated from the services ("وتحدد حالة الطلب الفرعي بناء على قرارات الخدمات"). What happens when they disagree? Also in 003-01: DOCTOR_NOTES is mandatory but described as needed only on a return; SERVICE_DECISION_NOTE is mandatory for every service but BR004 requires it only for partial approval; APPROVED_QUANTITY is mandatory even for rejected services; and the note is SERVICE_DECISION_NOTE in 003-01 but DOCTOR_DECISION_ON_SERVICE in 003-02. Which is correct? ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003)

17. The rejection (RJ), cancellation (CR), reopen (RO), reassignment (RA) and return-request (RR) reasons are fixed lists. Can CNHI change them after go-live, and how? ([PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011; [PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006)

18. PATIENT_SHARE_AMOUNT appears in the add-sub-request input but is not used anywhere else. What is it for, and should it be validated or shown? ([PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005)

19. What does UC012 BR006 mean: "لا يمكن رفع طلب فرعي جديد حتى لو تم إعادة الطلب لشركة ادراة المطالبات من المركز"? ([PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012)

20. Claim events only include CLAIM_SUBMITTED and CLAIM_ACCEPTED, but cancelling and restarting the claim deadline depend on a rejected or cancelled claim. Can the Claims Service also send CLAIM_REJECTED and CLAIM_CANCELLED? And does a PARTIALLY_APPROVED claim count as accepted and complete the authorization? ([PALJ-13](https://leansa.atlassian.net/browse/PALJ-13), UC_009; [PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

21. Is the Claims Service the future Claims module of this platform or an existing service, who owns it, and when can we get the interface for its events (009-01) and the linked-claims lookup (011-02), described as "واجهة متفق عليها بين الخدمتين"? ([PALJ-13](https://leansa.atlassian.net/browse/PALJ-13), UC_009; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

22. UC011 BR008 says "لا يوجد حد اقصى لاعادة فتح الموافقة من المركز", but ALT003, ALT007, MSG004 and MSG007 refer to a maximum period, a maximum count and a count above which a document is mandatory. Which applies, and what are the values? ([PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

23. Before reopening, any other non-cancelled authorization for the same beneficiary, provider and month blocks the reopen (UC011 BR007), which is stricter than the duplicate rule at submission. Is that intended? ([PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011)

24. Providers cannot cancel. Do they ask the Center (support) to cancel, and does that request need to be recorded in the system? ([PALJ-14](https://leansa.atlassian.net/browse/PALJ-14), UC_010)

25. UC016 BR004 says reopen and reassign are not notified, while UC011 BR010 and UC012 BR005 say "ويرسل النظام إشعار لمزود الخدمة وشركة إدارة المطالبات". Which is correct? ([PALJ-16](https://leansa.atlassian.net/browse/PALJ-16), UC_012; [PALJ-15](https://leansa.atlassian.net/browse/PALJ-15), UC_011; [PALJ-20](https://leansa.atlassian.net/browse/PALJ-20), UC_016)

26. Please confirm the complete event → recipient list (which events notify the provider, the TPA, or both), including the delayed verification results in question 6. ([PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001; [PALJ-7](https://leansa.atlassian.net/browse/PALJ-7), UC_003; [PALJ-8](https://leansa.atlassian.net/browse/PALJ-8), UC_004; [PALJ-9](https://leansa.atlassian.net/browse/PALJ-9), UC_005; [PALJ-10](https://leansa.atlassian.net/browse/PALJ-10), UC_006)

27. The action log list does not include a delayed verification finishing, a difference between the provider's and Yaqeen's patient data, or a failed notification delivery, although UC001 ALT006 says the Yaqeen result is logged ("ويسجل ذلك في سجل النشاطات"). Should all of them be added? ([PALJ-18](https://leansa.atlassian.net/browse/PALJ-18), UC_014; [PALJ-2](https://leansa.atlassian.net/browse/PALJ-2), UC_001)

28. The coverage decision form has fields still marked "يحدد من قطاع الأعمال": Transaction Type, Episode ID, Period From / To and Adjudication Reason. Can you provide them and sign off the authorization and sub-request layouts in Arabic and English? ([PALJ-17](https://leansa.atlassian.net/browse/PALJ-17), UC_013)

29. Is permanent retention of authorizations, the action log and attachments confirmed, and is there a legal maximum retention period? ([PALJ-18](https://leansa.atlassian.net/browse/PALJ-18), UC_014)
