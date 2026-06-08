# HR Offer Letter & Intern Onboarding Automation System

A Google Workspace-based HR workflow automation prototype built using Google Forms, Google Sheets, Google Apps Script, Google Docs, Google Drive, and Gmail.

## Overview

This project automates the internship onboarding workflow from probation-form submission to offer-letter generation, email delivery, and signed-offer tracking.

The system was designed to reduce repetitive HR work, protect the Intern Master from incorrect or duplicate updates, standardize offer-letter generation, and maintain a clear status history for every important action.

## Problem Statement

A manual onboarding process may require HR to:

* Copy candidate information between forms and sheets
* Check whether a candidate already exists
* Send probation and offer-related emails
* Generate unique intern IDs
* Calculate internship end dates
* Create offer letters from templates
* Convert documents into PDF
* Send offer letters by email
* Track signed-offer submissions
* Investigate incorrect or incomplete records

Handling these steps manually can lead to duplicate records, inconsistent documents, repeated emails, missing status updates, and difficulty tracing past actions.

This project brings these steps together in one controlled automation workflow.

## End-to-End Workflow

1. Candidate submits the probation form.
2. The response is stored in the `Probation_Responses` sheet.
3. Apps Script validates and synchronizes the response.
4. A valid new candidate is added to `Intern_Master`.
5. A duplicate email is recorded for HR review without overwriting existing master data.
6. The system sends the probation welcome email.
7. HR sends a pre-filled offer-confirmation form.
8. The candidate submits the offer response.
9. The system checks the respondent email against the registered email.
10. HR approves the offer or uses override approval after manual review.
11. The system generates the MID.
12. The internship end date is calculated.
13. Candidate data is inserted into a Google Docs offer-letter template.
14. The document is converted into PDF and stored in Google Drive.
15. The offer letter is emailed with a pre-filled signed-offer submission link.
16. The signed offer is submitted and logged.
17. HR verifies or rejects the signed offer.
18. Every major action and exception is recorded in `Status_Log`.

## Core Features

* Candidate data capture through Google Forms
* Probation-response synchronization
* Clean Intern Master creation
* Duplicate probation email detection
* Existing master-record protection
* Probation welcome email automation
* Pre-filled offer-confirmation forms
* Respondent email mismatch detection
* HR approval and override approval
* Unique MID generation
* Internship end-date calculation
* Google Docs template population
* PDF offer-letter generation
* Google Drive document storage
* Gmail delivery with PDF attachment
* Pre-filled signed-offer submission links
* Signed-offer submission tracking
* HR verification and rejection tracking
* Custom HR Automation menu
* Detailed status and exception logging

## Technology Stack

* Google Forms — candidate and offer responses
* Google Sheets — operational data and workflow tracking
* Google Apps Script — validation and automation logic
* Google Docs — offer-letter template
* Google Drive — generated document and PDF storage
* Gmail — automated email delivery

## MID Generation Logic

MID format:

```text
ROLE_CODE/NAME_CODE/SEQUENCE
```

Example:

```text
AU/AS/001
```

### Name-code rules

* When the candidate has a first name and surname, the system uses the first letter of both.
* When the candidate has no surname, the system uses the first two letters of the available name.

### Sequence rule

The sequence increases only when both the role code and name code match an existing MID.

Examples:

| Candidate     | Role Code | Name Code | Generated MID |
| ------------- | --------- | --------- | ------------- |
| Aarav Sharma  | AU        | AS        | AU/AS/001     |
| Aditya Sharad | AU        | AS        | AU/AS/002     |
| Riya Patel    | AU        | RP        | AU/RP/001     |
| Karan         | HR        | KA        | HR/KA/001     |

Riya Patel starts from `001` because `AU/RP` is a different combination from `AU/AS`.

## Important Business Rules

* A probation-form submission does not directly generate an offer letter.
* Only valid new candidates are synchronized into `Intern_Master`.
* A duplicate probation email does not overwrite existing master data.
* HR approval is required before final offer-letter generation.
* MID is generated according to role code, name code, and matching sequence.
* Internship end date is calculated from the approved start date and duration.
* Existing documents are not blindly regenerated.
* Email mismatch cases are logged for HR review.
* HR can use override approval after confirming a blocked mismatch case.
* Signed-offer verification is tracked separately from offer-letter delivery.
* All important actions and exceptions are recorded in `Status_Log`.

## Edge Cases Handled

### Duplicate Probation Response

When a new probation response uses an email already present in `Intern_Master`:

* The response remains available in `Probation_Responses`.
* The existing Intern Master record is not overwritten.
* A `DUPLICATE RESPONSE - REVIEW REQUIRED` entry is created in `Status_Log`.
* HR can manually review the response if a correction is required.

### Offer Response Email Mismatch

When the offer-confirmation form is submitted from a different signed-in email:

* The registered and respondent emails are compared.
* Automatic processing is blocked.
* The case is marked `BLOCKED - RESPONDENT EMAIL MISMATCH`.
* The mismatch details are recorded for HR review.

### Override Approval

When HR verifies that a blocked response belongs to the correct candidate:

* HR can choose `OVERRIDE APPROVED`.
* The workflow can continue under a controlled manual exception.
* The approval decision remains visible in the system.

### Incomplete Processing Review

When the offer-letter document or PDF already exists but the offer status is not clearly marked as sent:

* The system does not automatically create another document.
* It records `REVIEW - DOCUMENT EXISTS BUT STATUS INCOMPLETE`.
* HR must review the record before further processing.

### Signed Offer Submitted from Another Email

When a signed offer is uploaded using an alternate Google account:

* The submission is not automatically discarded.
* The alternate email is recorded in `Status_Log`.
* The MID and registered candidate email remain available for comparison.
* HR makes the final verification decision.

### Signed Offer Rejection

When HR rejects a signed offer:

* The rejection event is recorded in `Status_Log`.
* Verification date and verifier fields are cleared.
* The status can be marked as `REJECTED - RESUBMISSION REQUIRED`.

Full automated resubmission-email handling is planned as a future improvement.

## Main Sheets and Their Purpose

| Sheet                    | Purpose                                              |
| ------------------------ | ---------------------------------------------------- |
| `Intern_Master`          | Central operational record of candidates and interns |
| `Probation_Responses`    | Raw probation-form submissions                       |
| `Offer_Letter_Responses` | Candidate offer-confirmation responses               |
| `Signed_Offer_Responses` | Signed-offer form submissions                        |
| `MID_Register`           | Generated MID records and sequence tracking          |
| `Role_Code_Master`       | Mapping between internship roles and role codes      |
| `Status_Log`             | Audit history of actions, exceptions, and decisions  |
| `Portfolio_View`         | Simplified presentation of important test scenarios  |

## HR Automation Controls

The custom `HR Automation` menu allows authorized users to perform workflow actions without opening the Apps Script editor.

Actions include functions such as:

* Synchronizing probation responses
* Sending probation welcome emails
* Sending offer-confirmation forms
* Processing offer approvals
* Generating final offer letters
* Processing signed-offer HR decisions

## Screenshots

### 1. Workflow Diagram

![Workflow Diagram](screenshots/01-workflow-diagram.png.png)

### 2. Intern Master Offer Tracking

![Intern Master Offer Tracking](screenshots/02-intern-master-offer-tracking.PNG)

### 3. Status Log Automation History

![Status Log Automation History](screenshots/03-status-log-automation-history.png.PNG)

### 4. Offer Email with PDF and Pre-filled Link

![Offer Email](screenshots/04-offer-email-with-pdf-and-prefilled-link.PNG)

### 5. Offer Response Email Mismatch

![Email Mismatch](screenshots/05-offer-response-email-mismatch.PNG)

### 6. Signed Offer Tracking and HR Decision

![Signed Offer Tracking](screenshots/06-signed-offer-tracking.PNG)

### 7. Portfolio Summary View

![Portfolio Summary View](screenshots/07-portfolio-summary-view.PNG)

### 8. HR Automation Controls

![HR Automation Controls](screenshots/08-hr-automation-controls.PNG)

## Current Prototype Scope

The implemented prototype covers:

* Probation response synchronization
* Duplicate-record protection
* Welcome-email automation
* Offer-confirmation workflow
* Email mismatch detection
* HR approval and override approval
* MID generation
* Internship date calculation
* Offer-letter document and PDF generation
* Email delivery
* Signed-offer submission and verification tracking
* Status logging and exception handling

This project should be described as an HR onboarding automation prototype rather than a complete HR management system.

## Future Improvements

* Automatic signed-offer resubmission emails
* Signed-offer rejection history
* Automated reminder emails
* Role-based portal access
* Performance-cycle management
* Leave and internship-end-date adjustment
* Referral and points management
* Certificate and LOR eligibility
* Internship extension and exit workflows
* Dashboard and reporting
* Migration to Django and PostgreSQL

## Privacy and Demo Data

The public repository should use dummy or anonymized candidate information.

Before publishing any code or screenshots, remove or replace:

* Real candidate names
* Personal email addresses
* Phone numbers
* Google Form IDs
* Spreadsheet IDs
* Google Docs template IDs
* Drive folder IDs
* Private organization links
* Internal email addresses

## Author

**Arjun S Choudhary**

Focused on workflow automation, business-process automation, operations systems, Google Workspace automation, and AI-assisted workflows.
