# UI Mockups and Printed Forms

Source: BRD v1.1, the "صور توضيحية للنظام Prototypes و نماذج مطبوعة Forms" section of each use case. Captions are translated from the BRD. Use cases not listed here (UC_001 to UC_007, UC_009, UC_016) have no mockups ("لايوجد"); they are API-only or system jobs.

## UC_008 – Discharge from the platform screen (PALJ-12)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-authorization-page-with-discharge-button.png](UC_008-discharge-from-screen/1-authorization-page-with-discharge-button.png) | Main authorization page for the provider user, with the "Record beneficiary discharge" button |
| Screen 2 | [2-discharge-dialog.png](UC_008-discharge-from-screen/2-discharge-dialog.png) | Discharge dialog with discharge date and time, discharge disposition and notes |
| Screen 3 | [3-after-discharge-success.png](UC_008-discharge-from-screen/3-after-discharge-success.png) | The authorization after discharge, with the success message, discharge data and the claim submission deadline |

## UC_010 – Cancel authorization (PALJ-14)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-search-from-side-menu.png](UC_010-cancel-authorization/1-search-from-side-menu.png) | Searching for authorizations from the side menu, CNHI user |
| Screen 2 | [2-search-results-by-patient-id.png](UC_010-cancel-authorization/2-search-results-by-patient-id.png) | Search results by beneficiary ID, with each authorization's status |
| Screen 3 | [3-authorization-details-with-cancel-button.png](UC_010-cancel-authorization/3-authorization-details-with-cancel-button.png) | Authorization details with the "Cancel authorization" button for the CNHI Admin |
| Screen 4 | [4-cancel-confirmation-dialog.png](UC_010-cancel-authorization/4-cancel-confirmation-dialog.png) | Cancel confirmation dialog with the reasons list, and a reason field when "Other" is chosen |
| Screen 5 | [5-after-cancel-success.png](UC_010-cancel-authorization/5-after-cancel-success.png) | The authorization after cancellation, with the success message, cancellation data and the action log |

## UC_011 – Reopen authorization (PALJ-15)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-search-filtered-by-cancelled.png](UC_011-reopen-authorization/1-search-filtered-by-cancelled.png) | Searching with results filtered by status "Cancelled" |
| Screen 2 | [2-search-results-mixed-statuses.png](UC_011-reopen-authorization/2-search-results-mixed-statuses.png) | Search results by beneficiary ID showing cancelled, processed and active authorizations |
| Screen 3 | [3-cancelled-authorization-with-reopen-button.png](UC_011-reopen-authorization/3-cancelled-authorization-with-reopen-button.png) | Details of an authorization cancelled automatically more than a year ago, with linked claims and the "Reopen authorization" button |
| Screen 4 | [4-reopen-confirmation-dialog.png](UC_011-reopen-authorization/4-reopen-confirmation-dialog.png) | Reopen confirmation dialog with the claims summary, resulting status, a warning that sub-requests are not restored, the reason and a document |
| Screen 5 | [5-after-reopen-success.png](UC_011-reopen-authorization/5-after-reopen-success.png) | The authorization after reopening, with the success message, a "Reopened" indicator, the cancelled sub-request kept as is, and the action log |

## UC_012 – Reassign a returned sub-request (PALJ-16)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-search-filtered-by-pending-provider-response.png](UC_012-reassign-sub-request/1-search-filtered-by-pending-provider-response.png) | Searching with the sub-request status filter "Pending provider response" |
| Screen 2 | [2-search-results-with-remaining-time.png](UC_012-reassign-sub-request/2-search-results-with-remaining-time.png) | Search results with the returned sub-request and the remaining reply time for each result |
| Screen 3 | [3-authorization-details-with-reassign-button.png](UC_012-reassign-sub-request/3-authorization-details-with-reassign-button.png) | Authorization details with the sub-request's return details and the "Reassign request to the TPA" button |
| Screen 4 | [4-reassign-confirmation-dialog.png](UC_012-reassign-sub-request/4-reassign-confirmation-dialog.png) | Reassign confirmation dialog with what the TPA asked for, the status after reassignment, the reason, a document and an impact warning |
| Screen 5 | [5-after-reassign-success.png](UC_012-reassign-sub-request/5-after-reassign-success.png) | The sub-request after reassignment, status "Pending TPA review", with the success message, reassignment data and the action log |

## UC_013 – Print the coverage decision form (PALJ-17)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-authorization-details-with-print-button.png](UC_013-print-coverage-form/1-authorization-details-with-print-button.png) | Authorization details for the provider user, with the "Print coverage decision form" button in the main authorization header |
| Screen 2 | [2-print-dialog-language-choice.png](UC_013-print-coverage-form/2-print-dialog-language-choice.png) | Print dialog with the form language as the only choice |
| Form 1 | [form-1-coverage-form-arabic.png](UC_013-print-coverage-form/form-1-coverage-form-arabic.png) | Arabic coverage decision form in CNHI's approved design for the full authorization; shows sub-requests 01 and 02 (decided) and leaves out 03 (under review) |
| Form 2 | [form-2-coverage-form-english.png](UC_013-print-coverage-form/form-2-coverage-form-english.png) | English coverage decision form for the same authorization |

## UC_014 – Action log (PALJ-18)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-authorization-page-action-log-section.png](UC_014-action-log/1-authorization-page-action-log-section.png) | Main authorization page for the provider user, with the action log section for the main authorization |
| Screen 2 | [2-sub-request-page-action-log-section.png](UC_014-action-log/2-sub-request-page-action-log-section.png) | Sub-request page with the action log section for the sub-request |
| Screen 3 | [3-activity-details-dialog.png](UC_014-action-log/3-activity-details-dialog.png) | Activity details dialog with the status before and after, the full notes and the attachments |

## UC_015 – View screens (PALJ-19)
| # | File | Caption |
|---|---|---|
| Screen 1 | [1-tpa-user-search-screen.png](UC_015-view-screens/1-tpa-user-search-screen.png) | Search screen for the TPA user, with the search criteria and filters shown for that user type |
| Screen 2 | [2-search-results-paginated.png](UC_015-view-screens/2-search-results-paginated.png) | Search results within the user's scope, with pagination |
| Screen 3 | [3-authorization-page-read-only.png](UC_015-view-screens/3-authorization-page-read-only.png) | Read-only main authorization page with sub-requests, linked claims, the action log and only the print button |
| Screen 4 | [4-sub-request-page.png](UC_015-view-screens/4-sub-request-page.png) | Sub-request page with services, return and reply cycles, attachments and the action log |
