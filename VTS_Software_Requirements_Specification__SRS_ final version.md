# Software Requirements Specification (SRS)


## Vacation Tracking System (VTS)


| Field | Value |
| --- | --- |
| Author | Zeinab Suliman Mohamed Osman |
| Document version | 2.7 |
| Last updated | 11/11/2026 |




## 1. Introduction


### 1.1 Purpose

This document defines the functional and non-functional requirements for the Vacation Tracking System (VTS). It is intended for anyone interested in understanding, evaluating, or contributing to the project, including potential users, developers, collaborators, and recruiters.

### 1.2 Project Description

VTS is a web-based leave-management system for a single organization with multiple departments, locations, and managers. Employees submit and track their leave requests, and managers review the requests from their direct reports. Organization-wide HR staff maintain employee leave data and leave policies, and use the system's reports.


### 1.3 Vision

Improve the organization's leave-management process, efficiency and cost-effectiveness.


### 1.4 Goals

- Enable employees to manage vacation, sick leave and other supported leave categories on their own.
- Enable managers to review requests from direct reports and monitor team leave.
- Reduce ongoing HR involvement in tracking individual requests through configured workflows.
- Provide HR reporting and a reliable audit trail.
- Apply working calendars, weekends and public holidays to leave calculations.


### 1.5 Scope

In scope: employee leave requests and balances; location calendars; configurable validation; approvals and delegation; notifications; audit logging; employee-data synchronization; department reporting; and internal-system integration.

Out of scope: payroll, recruitment, performance management, attendance, shift scheduling, leave-pay calculation and multi-organization operation.


### 1.6 Definitions and Abbreviations


| Term | Definition |
| --- | --- |
| VTS | Vacation Tracking System |
| SSO | Single sign-on: users log in through an external identity provider instead of a VTS-specific password |
| IdP | Identity provider: the service that authenticates users. This project uses a mock IdP |
| OIDC | OpenID Connect, the standard protocol VTS uses to authenticate users through an IdP |
| Manager | The single person configured in the system as an employee's direct manager and approver in a normal flow |
| Leave category | A type of time off (e.g. vacation, sick, personal) |
| Award | Leave granted directly by a manager, without an employee request, by recording specific dates as approved leave (category Personal) for an employee who reports to them |
| Override | An action by HR or a System Admin that bypasses a rule. |
| Working day | A day on which the employee is scheduled to work according to the working calendar for the employee's effective work location, excluding configured non-working days and public holidays. |
| Working calendar | The configured schedule of working days, non-working days and public holidays maintained for an organizational location, associated with that location's time zone. Employees at that location use the same effective calendar and time zone. |
| Minimum request length | The smallest number of working days a single request may cover for a leave category. |
| Maximum request length | The largest number of consecutive working days a single request may cover for a leave category. |
| Minimum notice period | The minimum number of working days between the submission date and the first scheduled working day of leave. The submission date and the leave start date are excluded from the count. Categories such as sick leave may have a zero-day notice requirement. |
| Blackout period | A date range, set by the System Admin, during which leave of a given category cannot be requested unless an authorized override is recorded. |
| Leave entitlement | The total amount of leave an employee is granted for a defined leave year or period, according to the organization's leave policy. |
| Leave balance | The employee's current leave position after accounting for leave usage, approved leave, carry-over, awards and adjustments. It is referred to simply as “balance” in this document. |
| Leave accrual | The process of accumulating leave entitlement over a defined period according to configured rules. It defines how entitlement is earned over time. |
| Leave carry-over | Unused leave transferred from a previous period. |

### 1.7 References

- Project README (high-level system design diagram)
- Figures 1 to 8 in Section 6 of this document


## 2. Overall Description


### 2.1 Domain Problem

VTS helps employees manage leave, managers monitor team absence and HR administer policies, exceptions and reports. It complements existing HR systems and does not replace payroll or other HR functions.


### 2.2 Actors and User Classes


| Actor | Description |
| --- | --- |
| Employee | Submits, edits, withdraws and cancels own leave requests; views own balances and history. |
| Manager | Approves or denies requests from the employees who report to them (each employee has exactly one manager); views team calendar; awards personal leave within system-set limits. A manager is also an employee and submits own requests. |
| HR | The single organization-wide HR role; manages leave data, policies and location calendars, handles HR-routed approvals, runs reports and performs authorized overrides. |
| System Admin | Configures rules, categories, limits and working calendars; manages system roles and integrations; may perform authorized overrides. |
| External system | Internal system consuming the web service interface (non-human actor). |
| HR source system | Source of employee and organizational data (non-human actor). |


### 2.3 Operating Environment

A browser-based application deployed on the organization’s agreed infrastructure.


### 2.4 Constraints

1. The system must be web-based and accessible through a web browser.
2. The system must be an extension of any existing HR system, if one exists.
3. The system must authenticate all users through an OpenID Connect (OIDC) identity provider and must not store user passwords. This project uses a mock identity provider.
4. The system must run on existing hardware and middleware.
5. The system does not verify attendance.


### 2.5 Assumptions and Dependencies

1. The identity provider (the mock IdP in this project) supplies a stable, unique user identifier that VTS maps to an employee record.
2. The HR source system provides the employee profile fields: name, birth date, gender, department, manager, contact information, address and work location. These fields are synchronized daily.
3. VTS maintains leave balances, approval settings, policies and delegation records.
4. A mail service (SMTP or equivalent) is available for notifications.
5. HR or the System Admin maintains one effective working calendar per location. The employee’s current work location determines the calendar used.
6. Each employee has one direct manager, except top-level employees who may have none.
7. The organization has one central HR department, so there is one organization-wide HR application role. Users assigned that role handle requests requiring HR approval across all departments and locations.



## 3. Functional Requirements

Priority: M = must have, S = should have, C = could have.


### 3.1 Leave Request Management


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-1.1 | The system shall allow an employee to submit a leave request specifying category, start date, end date, title and description. | M |
| FR-1.2 | The system shall calculate the number of working days in the selected date range using the working calendar associated with the employee's effective work location and display the result before submission. | M |
| FR-1.3 | The system shall check that mandatory fields such as the title and description are filled in before submission and alert the user if any are missing. | M |
| FR-1.4 | The system shall allow an employee to edit a request after submission while it is pending. After each edit, the system shall re-run applicable validation rules. | M |
| FR-1.5 | The system shall allow an employee to withdraw a request while it is in pending state. Withdrawal shall release the request's temporary balance hold and shall not post a balance change. | M |
| FR-1.6 | The system shall permit employees to cancel their approved leave requests before midnight (00:00) at the start of the first scheduled working day of the leave period. | M |
| FR-1.7 | The system shall restrict the cancellation of approved leave on or after the first scheduled working day exclusively to users with HR or System Admin roles. | M |
| FR-1.8 | The system shall require a mandatory written justification field to be completed for every leave cancellation action, regardless of the user role. | M |
| FR-1.9 | The system shall display each employee's balance by leave category, distinguishing available balance, pending requests, approved-but-not-yet-taken (reserved) leave, and approved leave whose scheduled working days have passed (consumed leave). | M |
| FR-1.10 | The system shall allow each employee to view their own requests for the previous calendar year. | M |
| FR-1.11 | The system shall permit leave requests up to 18 months in the future, but shall reject any request with an end date exceeding this 18-month limit. | M |
| FR-1.12 | The system shall reject a date range containing zero working days as soon as the employee enters or selects the dates, and shall explain why the range is invalid. | M |
| FR-1.13 | The system shall support full working days only. | M |
| FR-1.14 | The system shall allow an employee to view the status and outcome of any leave request submission, withdrawal or cancellation action affecting their leave balance. | M |
| FR-1.15 | The system shall allow attachments only for leave categories that require or permit them. | C |
| FR-1.16 | The accepted formats for attachments are PDF, DOCX, JPG/JPEG and PNG files. | C |


### 3.2 Leave Rules-Based Validation


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-2.1 | The System Admin shall be able to configure applicable leave rules without code changes. | M |
| FR-2.2 | The System Admin shall be able to configure, per leave category, minimum and maximum request length in working days, minimum notice period in working days, and blackout periods. | S |
| FR-2.3 | The system shall validate every leave request against the applicable configured rules before accepting it. | M |
| FR-2.4 | The system shall reject a leave request that violates a configured leave rule. | M |
| FR-2.5 | The system shall reject a request whose start date is after its end date. | M |
| FR-2.6 | The system shall reject a request when it overlaps another pending or approved request for at least one working day under the working calendar applicable to the employee's effective work location on that date. | M |
| FR-2.7 | The system shall verify that the requested days do not exceed the employee's available entitlement, taking other pending requests into account as temporary holds. | M |
| FR-2.8 | The System Admin shall be able to enable or disable retrospective requests. When enabled, the maximum past-request window shall be configurable per leave category. When disabled, all retrospective requests shall be rejected. | M |
| FR-2.9 | The system shall report all detected validation failures in one response, with a clear reason for each failure. | S |


### 3.3 Approval Workflow


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-3.1 | A valid request from an employee who requires approval and has an assigned manager shall be set to Pending Approval and routed to that manager. Pending requests are not yet posted as approved in the balance ledger. | M |
| FR-3.2 | When a leave request requires approval and a manager is assigned, the assigned manager or authorized active delegate shall be able to approve or deny a request. A denial shall require a comment. | M |
| FR-3.3 | When a leave request requires approval and no manager is assigned, the system shall handle routing as follows: Auto-Approve, if the employee profile is explicitly configured to bypass approval; HR Escalation, if the employee requires approval, by routing the request to the organization-wide HR role. | M |
| FR-3.4 | If the request is withdrawn by the employee or denied, the pending request shall not cause a balance debit or credit. | M |
| FR-3.5 | The system shall re-validate the available balance, overlap and applicable rules at the moment of approval and shall commit the decision and balance allocation atomically. | M |
| FR-3.6 | Upon approving a request, the system shall commit the requested working days against the employee's available entitlement and record them as approved-but-not-yet-taken (reserved) leave. | M |
| FR-3.7 | A manager shall be able to schedule a delegate for all direct reports by setting a start and end date/time before the delegation begins. The delegate may act only during the effective period. | M |
| FR-3.8 | A scheduled delegation shall activate and expire automatically according to the set timeframe. | M |
| FR-3.9 | The manager may replace an active delegate by recording a replacement and effective date/time. At that time, authority transfers to the replacement for all direct reports and pending requests. | M |
| FR-3.10 | The manager may revoke a delegation before the end of the delegation timeframe. A System Admin may revoke a delegation only in emergency cases. | M |
| FR-3.11 | HR may schedule a delegation, amend it, or revoke it if the manager is unavailable. | M |
| FR-3.12 | If a request remains pending beyond a configurable period, the system shall remind the active approver. If it remains pending beyond a second configurable period, the system shall escalate it to HR or a System Admin. | S |
| FR-3.13 | If an employee's assigned manager changes while a request is pending, the system shall route the request to the new assigned manager, unless a valid delegation or configured HR authority applies. | S |
| FR-3.14 | A request shall have no more than one effective approver at a time. If two authorized people act concurrently, the first valid decision shall be saved and the second shall be rejected with a message that the request has already been decided. | M |


### 3.4 Leave Request Lifecycle


Request states are Draft, Pending Approval, Approved, Denied, Withdrawn, Cancelled, Partially Cancelled and Completed.

| From | To | Triggered by |
| --- | --- | --- |
| (new) | Draft | Employee creates a leave request and has not yet submitted it; the employee can edit the draft |
| Draft | Pending Approval | Employee submits and approval is required |
| Draft | Approved | Employee submits and configuration says approval is not required |
| (new) | Approved | Manager awards leave |
| Pending Approval | Approved / Denied | Assigned manager, active delegate, or configured HR role / authorized override |
| Pending Approval | Withdrawn | Employee |
| Approved | Cancelled | Employee before the first scheduled working day (FR-1.6), or HR/System Admin (FR-1.7) |
| Approved | Partially Cancelled | HR/System Admin cancels future, not-yet-elapsed working days after the leave has started |
| Approved | Completed | System, after all scheduled leave dates have passed |


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-4.1 | A leave request that has been created but not yet submitted shall be in Draft state, and the employee shall be able to edit the draft. | M |
| FR-4.2 | At 00:00 on each scheduled working day in the employee’s location time zone, that day becomes consumed. | M |
| FR-4.3 | Cancellation by an employee before the first leave day returns all reserved days. | M |
| FR-4.4 | After leave starts, HR or a System Admin may cancel future unelapsed days only; elapsed days remain consumed. | M |
| FR-4.5 | The system shall automatically change a leave request's status from Approved to Completed when all scheduled working days included in the request have elapsed. | M |


### 3.5 Manager Features


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-5.1 | The system shall show a manager all pending requests belonging to their direct reports, including requests currently actionable by an active delegate. | M |
| FR-5.2 | The system shall provide a consolidated calendar showing detailed approved and pending leave for the manager's direct reports and aggregated department-level absence/capacity information. It shall not expose identifiable leave details for employees outside the manager's reporting line. | M |
| FR-5.3 | The system shall highlight department-capacity conflicts when a manager reviews a request, using configurable absence thresholds. | S |
| FR-5.4 | A manager shall be able to award personal leave directly to a direct report within system-set limits, including maximum days per award and per period. | M |
| FR-5.5 | The system shall record awarded leave as approved Personal leave for the selected working dates, notify the employee, and deduct the leave days from their available balance. The leave days shall remain reserved until their scheduled dates are consumed. | M |
| FR-5.6 | The system shall prevent managers from approving, or awarding leave for themselves. Cancelation is only applicable before the first leave day| M |


### 3.6 Notifications


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-6.1 | The system shall notify the assigned manager, active delegate or organization-wide HR role, as applicable, when a request requires approval. The notification shall include recipient-appropriate request details and a link to act on it. | M |
| FR-6.2 | The system shall notify the employee of every status change (approved, denied, withdrawn, cancelled, partially cancelled, awarded). Notifications shall not contain sensitive medical details or attachments. | M |
| FR-6.3 | The system shall email on reminders and escalations (FR-3.12). | S |
| FR-6.4 | Failed email deliveries shall be retried and logged, and shall not block the underlying transaction. | M |
| FR-6.5 | Email templates shall be configurable by the System Admin. | C |


### 3.7 HR Management and System Administration


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-7.1 | HR personnel shall be able to enter and update employee leave data, including entitlements, balances, carry-over and adjustments. | M |
| FR-7.2 | Manual balance adjustments shall be restricted to authorized HR personnel, require a reason and shall be recorded in the audit log. They are correction transactions and shall not be used to reverse any change in request state. | M |
| FR-7.3 | If an employee’s manager is missing or inactive, the system shall alert HR. Pending requests shall remain pending and may be handled by a valid delegate or an authorized HR/System Admin action. | M |
| FR-7.4 | The system shall allow HR to specify whether manager approval is required for each employee's leave requests. | M |
| FR-7.5 | When HR records an employment termination in VTS, the system shall withdraw the employee's pending requests, cancel approved requests that start after the end date, release the corresponding provisional holds or future reserved days, and notify the relevant manager and HR. | S |
| FR-7.6 | Approved requests that start on or before the employment end date but include working days after it shall be flagged for HR review. HR shall decide whether future days should be cancelled; elapsed days shall remain consumed. | S |
| FR-7.7 | The system shall calculate a final balance per leave category as of the employment end date. Payment for unused leave remains out of scope. | S |
| FR-7.8 | After the employment end date, the former employee shall be denied access and unable to submit requests, while their historical requests, balances and audit records shall be retained according to the organization's retention policy. | S |
| FR-7.9 | The System Admin shall be able to set one effective working calendar per location, including its time zone. | M |
| FR-7.10 | The system shall support accrual, annual reset and carry-over through parameters configured by the System Admin. The configuration shall include the applicable leave category, entitlement/accrual amount, accrual frequency or method, leave-year/reset date, carry-over limit and carry-over expiry where applicable. | M |
| FR-7.11 | The System Admin shall be able to configure the parameters used for accrual, annual reset and carry-over without code changes. Invalid or incomplete policy configurations shall not be activated. | M |


### 3.8 Administrative Overrides


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-8.1 | HR and System Admin personnel shall be able to override any action restricted by rules (e.g. approve beyond balance). | M |
| FR-8.2 | Every override shall require a written justification and shall be logged with actor, time, rule bypassed and affected record. | M |
| FR-8.3 | Overrides shall be reportable to HR if executed by System Admin. | S |


### 3.9 Audit Logging


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-9.1 | The system shall log all transactions: request creation, edit, state change, approval decisions, awards, balance changes, overrides, data imports, configuration changes and role changes. | M |
| FR-9.2 | Each entry shall record who, what, when, the previous and new values, and the source (UI, API, import). | M |
| FR-9.3 | The system shall retain audit logs for 12 months. | M |
| FR-9.4 | Authorized users (HR, System Admin) shall be able to search and filter the audit log. | S |


### 3.10 Reporting and Statistics


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-10.1 | HR shall be able to view balances and usage by employee, department and leave category over a time period. | M |
| FR-10.2 | HR shall be able to view pending, approved and denied requests over a time period. | M |
| FR-10.3 | HR shall be able to view absence trends, including peak periods, average approval time and override history. | S |
| FR-10.4 | Reports shall be exportable (CSV and PDF). | S |
| FR-10.5 | Report access shall be restricted by HR or System Admin role. | M |


### 3.11 Integration and Web Service Interface


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-11.1 | The system shall provide a web service interface for other internal systems to query any given employee's vacation request summary. | M |
| FR-11.2 | The interface shall authenticate callers and authorize them. | M |
| FR-11.3 | The interface shall support retrieval of requests by employee, date range, status and category, and retrieval of updates since a given timestamp. | M |
| FR-11.4 | The interface shall be read-only. | M |
| FR-11.5 | The system shall import employee profile fields (name, birth date, gender, department, manager, contact information, address and work location) from the HR source system once daily at the beginning of the organization’s configured working day. | M |
| FR-11.6 | The system shall successfully synchronize employee profile data and make data updates available to relevant system functions immediately after processing. | M |
| FR-11.7 | Imported manager, department and work-location changes shall take effect immediately and be recorded. | M |
| FR-11.8 | Duplicate matching and conflict resolution that may occur after synchronization shall be identified in the low-level design and handled in implementation. | M |
| FR-11.9 | Employee data import failures shall be logged, shall not corrupt existing data, and shall alert the System Admin. | M |


### 3.12 Authentication and Authorization


| ID | Requirement | Pri |
| --- | --- | --- |
| FR-12.1 | The system shall authenticate all users through an OpenID Connect (OIDC) identity provider and shall not store user passwords. | M |
| FR-12.2 | This project shall include a mock identity provider that implements the OIDC login flow, with seeded test users. It is for development and testing only and shall not be used in production. | M |
| FR-12.3 | The identity provider settings (issuer URL, client ID, client secret) shall be configuration only, so the mock can be replaced by a real provider without code changes. | M |
| FR-12.4 | A user who authenticates successfully but has no active employee record in VTS shall be denied access. | M |
| FR-12.5 | The system shall enforce role-based access control with separate Employee, Manager, HR and System Admin roles. | M |
| FR-12.6 | The system shall provide a single HR role with organization-wide access to HR functions. Multiple HR users may be assigned this role, and all shall have the same permissions. Separate HR sub-roles such as HR Clerk or HR Approver shall not be supported in this release. | M |
| FR-12.7 | Employees may view their own data. Managers may view their direct reports’ leave information and medical details. HR has organization-wide access. System Admin access is limited to administrative functions. | M |
| FR-12.8 | A user holding several roles (e.g. manager and employee) shall have the combined permissions with no self-approval. | M |
| FR-12.9 | Roles shall be assigned and stored in VTS, not taken from the identity provider. | M |


### 3.13 Permissions Matrix


| Capability | Employee | Manager | HR | System Admin |
| --- | --- | --- | --- | --- |
| View leave categories | ✔ | ✔ | ✔ | ✔ |
| Submit / edit / withdraw own requests | ✔ | ✔ | ✔ | ✔ |
| Cancel own approved request before its start date | ✔ | ✔ | ✔ | ✔ |
| Process cancellation after leave has started |  |  | ✔* | ✔ |
| View own balance and history | ✔ | ✔ | ✔ | ✔ |
| Approve / deny direct reports' requests |  | ✔ |  |  |
| Approve requests as configured HR authority |  |  | ✔* |  |
| Create / revoke delegation for own direct reports |  | ✔ |  |  |
| Configure delegation when manager is unavailable |  |  | ✔* |  |
| Revoke delegation in emergency cases |  |  |✔ | ✔ |
| View detailed leave for direct reports |  | ✔ |  |  |
| View aggregated department capacity |  | ✔ | ✔ | ✔ |
| Award leave to direct reports (within limits) |  | ✔ |  |  |
| Enter / update employee leave data |  |  | ✔ |  |
| Maintain employee approval settings |  |  | ✔ |  |
| Run HR reports |  |  | ✔ | ✔ |
| Override rules (logged) |  |  | ✔ | ✔ |
| Configure rules, categories, location calendars and limits |  |  |  | ✔ |
| Manage roles and integrations |  |  |  | ✔ |
| View/search audit log |  |  | ✔ | ✔ |
| Access medical details and supporting attachments |  | ✔ | ✔* |  |



\* The HR role has organization-wide scope. Medical details and supporting medical attachments may be accessed only by the employee’s direct manager or HR. Manager access is limited to direct reports. System Admin status alone does not grant access to medical details. Sensitive data access and post-start cancellations are audited.


## 4. Non-Functional Requirements


| ID | Category | Requirement |
| --- | --- | --- |
| NFR-1 | Usability | A first-time employee shall be able to submit a valid request without formal training. Applicable policy guidance and validation explanations shall be displayed inline. Usability shall be evaluated using representative employee tasks. |
| NFR-2 | Accessibility | The system shall target WCAG 2.1 Level AA for supported employee, manager, HR and System Admin workflows. At minimum, all core functions shall be keyboard-operable, have visible focus, programmatic labels, sufficient contrast and understandable error messages. |
| NFR-3 | Security | Authentication shall use OIDC through the configured identity provider. All traffic shall use TLS. Authorization shall be enforced server-side on every protected request. The system shall protect against common web application vulnerabilities, validate identity tokens and securely manage client secrets and credentials. |
| NFR-4 | Privacy | Protect employee sensitive data, leave reasons and attachments with role-based access and encryption in transit and at rest. Managers may access medical details only for their direct reports; HR may access them organization-wide. Medical details or attachments should not be included in email notifications. |
| NFR-5 | Interoperability | The system shall communicate with the agreed HR source system interface and expose documented, versioned interfaces to authorized internal systems. Integration contracts and field mappings shall be documented. |
| NFR-6 | Performance | At 200 employees and 150 concurrent users, at least 95.5% of measured operations shall meet these targets: page loads under 3 seconds and normal request processing under 2 seconds. |
| NFR-7 | Scalability | The system shall support 200 employee records and 150 concurrent users without breaching NFR-6 performance targets. |
| NFR-8 | Availability | The system shall target 99.5% monthly availability during the organization's defined service window, excluding announced scheduled maintenance. The service window and availability calculation shall be documented. |
| NFR-9 | Reliability and integrity | Balance checks, allocations, cancellations and status changes shall be atomic. Concurrent operations shall not exceed available balance or create duplicate decisions. |
| NFR-10 | Backup and recovery | Back up data daily and retain backups for no more than 30 days. Define and test RPO and RTO before production use. |
| NFR-11 | Maintainability | Leave rules, calendars and accrual parameters shall be modular and configurable. Automated tests shall cover date calculations, validation, request lifecycle, approvals, denials, delegation, withdrawals, cancellations and authorization. |
| NFR-12 | Deployment | The system shall run on the organization's agreed hardware and middleware environment. Deployment configuration shall be documented. |
| NFR-13 | Localization | Use the employee’s location calendar and time zone for working-day calculations and the 00:00 consumed-leave cutoff. |
| NFR-14 | Observability | The system shall provide application logs, security/audit events, integration-failure logs and health monitoring. Logs shall support diagnosis without recording passwords, tokens, medical details or attachment contents. |
| NFR-15 | Attachment security | Store attachments privately and encrypt them in transit and at rest. Allow only PDF, DOCX, JPG/JPEG and PNG; verify file signatures, enforce size limits and reject executable, macro-enabled and archive files. Malware scanning is excluded from this release. |
| NFR-16 | Attachment delivery and retention | Require authorization for every download and audit access. Retain attachments for 12 months after the request reaches a terminal state, then delete them from active storage. Backup copies expire under the 30-day backup schedule. |
| NFR-17 | Audit integrity | Audit records for approvals, denials, cancellations, awards, balance adjustments, overrides, delegation changes, calendar changes and sensitive-document access shall be append-only from the application and protected against unauthorized modification or deletion. |




## 5. Data and Business Rules


### 5.1 Key Business Rules

- BR-1: Leave request days shall be calculated using the employee's work location calendar, including the start and end dates of the request.
- BR-2: Pending requests create provisional holds for validation; they are not posted to the approved balance ledger.
- BR-3: Requests overlap only when they share at least one working day.
- BR-4: Retrospective requests are controlled by a System Admin enable/disable setting. If enabled, the past-request window is configurable per category.
- BR-5: A manager may not approve, cancel or award leave for themselves (self-action is prohibited).
- BR-6: Manager awards are subject to System Admin-configured limits and are deducted against the recipient's available balance when awarded.
- BR-7: Only authorized HR or System Admin users may perform rule overrides. Each override requires a justification and is audited.
- BR-8: Half-day leave is not supported.
- BR-9: A request with zero working days is rejected at date entry/selection.
- BR-10: Minimum and maximum request lengths are measured in working days.
- BR-11: The System Admin maintains one effective working calendar per location. The employee’s work location determines the applicable calendar; department- and employee-specific calendars are not supported.
- BR-12: Requests requiring approval but lacking an assigned manager route to the single organization-wide HR role, unless the employee profile is explicitly configured to bypass approval. Absence of an assigned manager shall not, by itself, cause automatic approval.
- BR-13: A delegation covers all direct reports and must be scheduled before its start. It is valid only during its configured period.
- BR-14: Delegations are activated and expired automatically. The manager may revoke or replace a delegation; HR may manage it if the manager is unavailable, and a System Admin may revoke it only in emergency cases.
- BR-15: Once a leave request is approved, the leave days are reserved. Each scheduled working day is counted as consumed at midnight according to the employee's local time zone, without deducting it again from the balance. Consumed days cannot be restored after midnight.
- BR-16: A pending provisional hold is released when the request ceases to be pending. Denial or withdrawal of a pending request causes no balance debit or credit. 
- BR-17: Employees may cancel before the first scheduled working day. From 00:00 on that day, only HR or a System Admin may cancel. A written reason is required; one authorized action is sufficient. Restore future unelapsed days only.
- BR-18: Manager-awarded Personal leave is approved at the time of award and follows the same calendar, overlap, entitlement, reserved/consumed and cancellation rules as approved employee-requested leave.
- BR-19: Accrual, annual reset and carry-over are controlled by policy parameters configured by the System Admin and entered/maintained by authorized HR personnel as appropriate.
- BR-20: Reporting in this release is based on employees, departments and leave categories. Project-based reporting is out of scope.
- BR-21: Import employee profile fields daily. Changes apply immediately after successful processing. Duplicate matching and conflict resolution are specified in the low-level design.
- BR-22: HR may make a separately authorized, reasoned and audited balance correction. A correction is not a substitute for reversing a denied or withdrawn request; approved allocations are released through the cancellation process.
- BR-23: Exclude the submission date and first scheduled leave date from minimum-notice calculations. Count only working days strictly between them.
- BR-24: Accept PDF, DOCX, JPG/JPEG and PNG attachments. Malware scanning is excluded from this release.
- BR-25: Retain supporting attachments for 12 months after the request reaches a terminal state, then delete them from active storage.


### 5.2 Data Entities

The data model shall include Employee; Reporting Relationship; Role and HR approval routing; Leave Category and Policy; Balance Ledger; Location and Location Working Calendar; Public Holiday; Leave Request; Approval Decision; Delegation; Award; Attachment Metadata; Audit Log; Notification; Department; and HR Import Record. It shall distinguish provisional holds, reserved leave and consumed leave.


### 5.3 Data Retention Schedule


| Data | Retention period | Action |
| --- | --- | --- |
| Employee profile | During employment + 2 years | Delete or anonymize, subject to legal requirements |
| Leave requests, decisions and balance history | 3 years after the relevant leave year ends | Delete or anonymize |
| Medical/supporting attachments | 12 months after the request reaches a terminal state | Securely delete from active storage |
| Audit logs | 12 months | Securely delete |
| Application and error logs | 30 days | Securely delete |
| HR import logs | 90 days | Securely delete |
| Notification delivery logs | 90 days | Securely delete |
| Temporary import/upload processing files | After processing; within 24 hours if interrupted | Securely delete |
| Backups | Maximum 30 days | Expire automatically |




## 6. Domain Models


### 6.1 Entity Diagram

- **Figure 1:** Entity Diagram showing the data model


### 6.2 Flowcharts

- **Figure 2:** Flowchart A - Manage Time Use Case
- **Figure 3:** Flowchart B - Edit Pending Request
- **Figure 4:** Flowchart C - Withdraw Pending Request
- **Figure 5:** Flowchart D - Cancel Request


### 6.3 Sequence Diagrams

- **Figure 6:** Sequence Diagram - Manage Time for Employee
- **Figure 7:** Sequence Diagram - Manage Time for Manager


### 6.4 State Machine Diagram

- **Figure 8:** Vacation Request State Machine


### 6.5 Pseudocode: Create, Edit, Submit and Withdraw Vacation Request


```text
// ---------- Shared helpers ----------
FUNCTION RESOLVE_ACTIVE_EMPLOYEE(authenticated_user)
    employee = FIND_ACTIVE_EMPLOYEE_BY_IDENTITY(authenticated_user.subject)
    IF employee IS NULL THEN
        REJECT("No active employee record is linked to this account")          // FR-12.4
    END IF
    RETURN employee
END FUNCTION

FUNCTION PREVIEW_WORKING_DAYS(employee, start_date, end_date)                    // FR-1.2, FR-1.12, FR-1.13
    // Whole days only; half-day leave is not supported
    IF start_date > end_date THEN
        RETURN ERROR("End date must be on or after start date")                  // FR-2.5
    END IF
    calendar_segments = GET_LOCATION_CALENDAR_SEGMENTS_FOR_EMPLOYEE_EFFECTIVE_LOCATIONS(employee.id, start_date, end_date)
    IF calendar_segments DOES NOT COVER THE ENTIRE REQUEST PERIOD THEN
        RETURN ERROR("No working calendar is configured for the employee's work location for the selected period")
    END IF
    working_dates = GET_WORKING_DATES_USING_EFFECTIVE_LOCATION_CALENDARS(start_date, end_date, calendar_segments)
    IF COUNT(working_dates) = 0 THEN
        RETURN ERROR("The selected period contains no working days")             // rejected as soon as dates are entered or selected
    END IF
    RETURN working_dates, COUNT(working_dates), calendar_segments                // result is displayed before submission
END FUNCTION

FUNCTION VALIDATE_REQUEST(employee, request, submission_date, excluded_request_id)
    // Collects every failure so that all of them are reported in one response (FR-2.9)
    failures = EMPTY LIST
    category_rules = GET_CONFIGURED_RULES(request.category)                      // FR-2.1, FR-2.2

    IF request.title IS EMPTY OR request.description IS EMPTY THEN               // FR-1.3
        ADD(failures, "Title and description are required")
    END IF

    preview = PREVIEW_WORKING_DAYS(employee, request.start_date, request.end_date)
    IF preview IS ERROR THEN
        ADD(failures, preview.message)
        RETURN failures                    // the checks below need working dates
    END IF
    working_dates = preview.working_dates
    requested_days = preview.count

    IF request.end_date > submission_date + 18 MONTHS THEN                       // FR-1.11
        ADD(failures, "Requests cannot end more than 18 months in the future")
    END IF

    // Retrospective requests (FR-2.8)
    IF request.start_date < submission_date THEN
        IF retrospective_requests_enabled = false THEN
            ADD(failures, "Retrospective requests are not allowed")
        ELSE IF request.start_date < submission_date - category_rules.past_request_window THEN
            ADD(failures, "The request is outside the permitted past-request window for this leave category")
        END IF
    END IF

    // Length, notice and blackout rules, all measured in working days (FR-2.2)
    IF requested_days < category_rules.min_length OR requested_days > category_rules.max_length THEN
        ADD(failures, "The request length is outside the permitted range for this leave category")
    END IF
    // Count working days strictly after the submission date and strictly before the first leave working date
    notice_days = COUNT_WORKING_DAYS_STRICTLY_BETWEEN(submission_date, FIRST_DATE_IN(working_dates), preview.calendar_segments)
    IF notice_days < category_rules.min_notice_working_days THEN
        ADD(failures, "The minimum notice period for this leave category is not met")
    END IF
    IF ANY(working_dates) FALLS_IN category_rules.blackout_periods THEN
        ADD(failures, "The request falls in a blackout period for this leave category")
    END IF

    // Attachments (FR-1.15, FR-1.16); malware scanning is not included in this release
    IF request.attachments IS NOT EMPTY AND category_rules.attachments NOT IN ("required", "permitted") THEN
        ADD(failures, "Attachments are not allowed for this leave category")
    END IF
    IF category_rules.attachments = "required" AND request.attachments IS EMPTY THEN
        ADD(failures, "A supporting attachment is required for this leave category")
    END IF
    IF NOT ATTACHMENTS_HAVE_ALLOWED_TYPE_SIGNATURE_AND_SIZE(request.attachments) THEN   // PDF, DOCX, JPG/JPEG, PNG
        ADD(failures, "Unsupported, oversized or file-signature-mismatched attachment")
    END IF

    // Overlap compares working dates only (FR-2.6)
    IF EXISTS_PENDING_OR_APPROVED_REQUEST_WITH_INTERSECTING_WORKING_DATES(employee.id, working_dates, excluded_request_id) THEN
        ADD(failures, "The request overlaps another pending or approved request on a working day")
    END IF

    // Entitlement, taking other pending requests into account as temporary holds (FR-2.7)
    available_entitlement = GET_AVAILABLE_ENTITLEMENT(employee.id, request.category)
    other_pending_holds = SUM_PENDING_PROVISIONAL_HOLDS(employee.id, request.category, excluded_request_id)
    IF requested_days > available_entitlement - other_pending_holds THEN
        ADD(failures, "Requested days exceed the available entitlement after pending requests")
    END IF

    APPEND(failures, EVALUATE_OTHER_CONFIGURED_RULES(employee, request))         // FR-2.3, FR-2.4
    RETURN failures
END FUNCTION

FUNCTION RESOLVE_APPROVER_TARGETS(employee, assigned_manager)
    // FR-3.1, FR-3.3, FR-6.1, FR-7.3; never auto-approves when no approver is available
    IF assigned_manager IS NOT NULL THEN
        IF assigned_manager.is_active = false THEN
            ALERT_HR("The assigned manager is inactive", employee.id)            // FR-7.3
        END IF
        delegate = FIND_ACTIVE_DELEGATE(assigned_manager.id)
        IF delegate IS NOT NULL THEN RETURN delegate END IF
        IF assigned_manager.is_active = true THEN RETURN assigned_manager END IF
    ELSE
        ALERT_HR("The employee has no assigned manager", employee.id)            // FR-7.3
    END IF
    hr_users = GET_ACTIVE_USERS_WITH_ROLE("HR")                                  // organization-wide HR role (FR-3.3)
    IF hr_users IS NOT EMPTY THEN RETURN hr_users END IF
    RETURN CONFIGURED_ESCALATION_PATH()                                          // no HR user available: do not auto-approve
END FUNCTION

// ---------- Create and edit a draft (FR-4.1) ----------
FUNCTION SAVE_DRAFT(authenticated_user, draft_id, category, start_date, end_date, title, description, attachments)
    employee = RESOLVE_ACTIVE_EMPLOYEE(authenticated_user)
    IF draft_id IS NULL THEN
        draft = CREATE_REQUEST(employee.id, status = "Draft")                    // created but not yet submitted
    ELSE
        draft = LOAD_REQUEST_FOR_UPDATE(draft_id)
        IF draft.employee_id != employee.id THEN
            REJECT("You may edit only your own requests")
        END IF
        IF draft.status != "Draft" THEN
            REJECT("Only a draft can be edited this way")
        END IF
    END IF
    SET_REQUEST_FIELDS(draft, category, start_date, end_date, title, description, attachments)
    SAVE(draft)                            // a draft is not routed for approval until it is submitted
    WRITE_AUDIT_EVENT("REQUEST_DRAFT_SAVED", employee, draft.id)
    RETURN draft.id
END FUNCTION

// ---------- Submit a draft (FR-1.1) ----------
FUNCTION SUBMIT_REQUEST(authenticated_user, draft_id)
    employee = RESOLVE_ACTIVE_EMPLOYEE(authenticated_user)
    request = LOAD_REQUEST(draft_id)
    IF request.employee_id != employee.id THEN
        REJECT("You may submit requests only for yourself")
    END IF
    IF request.status != "Draft" THEN
        REJECT("Only a draft can be submitted")
    END IF

    failures = VALIDATE_REQUEST(employee, request, TODAY, NULL)
    IF failures IS NOT EMPTY THEN
        REJECT_WITH_ALL_REASONS(failures)                                        // FR-2.4, FR-2.9
    END IF

    BEGIN TRANSACTION
        LOCK_EMPLOYEE_CATEGORY_BALANCE(employee.id, request.category)
        LOCK_EMPLOYEE_REQUEST_SCOPE(employee.id)

        // Re-validate inside the transaction so that concurrent submissions cannot both succeed
        failures = VALIDATE_REQUEST(employee, request, TODAY, NULL)
        IF failures IS NOT EMPTY THEN
            ROLLBACK
            REJECT_WITH_ALL_REASONS(failures)
        END IF

        approval_required = GET_APPROVAL_REQUIRED_CONFIGURATION(employee.id)     // FR-7.4
        assigned_manager = GET_ASSIGNED_MANAGER(employee.id)

        IF approval_required = false THEN
            // Auto-approval is allowed only by explicit configuration (FR-3.3)
            ALLOCATE_AVAILABLE_ENTITLEMENT_TO_RESERVED_LEAVE(employee.id, request.category, request.requested_days)
            SET_REQUEST_STATUS(request, "Approved")
            WRITE_AUDIT_EVENT("REQUEST_AUTO_APPROVED", employee, request.id)
            notification_targets = employee
        ELSE
            // The pending request is only a provisional hold; no balance ledger debit is posted (FR-3.1, FR-3.4)
            SET_REQUEST_STATUS(request, "Pending Approval")
            notification_targets = RESOLVE_APPROVER_TARGETS(employee, assigned_manager)
        END IF

        WRITE_AUDIT_EVENT("REQUEST_SUBMITTED", employee, request.id)             // FR-9.1, FR-9.2
    COMMIT TRANSACTION

    QUEUE_NOTIFICATION_WITH_RETRY(notification_targets, request.id)             // FR-6.1, FR-6.4; no medical details
    OUTPUT("Request submitted successfully")
END FUNCTION

// ---------- Edit a pending request (FR-1.4) ----------
FUNCTION EDIT_PENDING_REQUEST(authenticated_user, request_id, changes)
    employee = RESOLVE_ACTIVE_EMPLOYEE(authenticated_user)
    BEGIN TRANSACTION
        request = LOAD_REQUEST_FOR_UPDATE(request_id)
        IF request.employee_id != employee.id THEN
            ROLLBACK
            REJECT("You may edit only your own requests")
        END IF
        IF request.status != "Pending Approval" THEN
            ROLLBACK
            REJECT("Only a draft or a pending request can be edited")
        END IF
        LOCK_EMPLOYEE_CATEGORY_BALANCE(employee.id, request.category)
        LOCK_EMPLOYEE_REQUEST_SCOPE(employee.id)

        updated_request = APPLY_CHANGES_TO_COPY(request, changes)
        // Re-run the applicable validation rules; this request's own hold and dates are excluded from the checks
        failures = VALIDATE_REQUEST(employee, updated_request, TODAY, request.id)
        IF failures IS NOT EMPTY THEN
            ROLLBACK
            REJECT_WITH_ALL_REASONS(failures)
        END IF
        SAVE_WITH_PREVIOUS_VALUES(request, updated_request)                      // provisional hold follows the new requested days
        WRITE_AUDIT_EVENT("REQUEST_EDITED", employee, request.id)
    COMMIT TRANSACTION
END FUNCTION

// ---------- Withdraw a pending request (FR-1.5) ----------
FUNCTION WITHDRAW_REQUEST(authenticated_user, request_id)
    employee = RESOLVE_ACTIVE_EMPLOYEE(authenticated_user)
    BEGIN TRANSACTION
        request = LOAD_REQUEST_FOR_UPDATE(request_id)
        IF request.employee_id != employee.id THEN
            ROLLBACK
            REJECT("You may withdraw only your own requests")
        END IF
        IF request.status != "Pending Approval" THEN
            ROLLBACK
            REJECT("Only a pending request can be withdrawn")
        END IF
        RELEASE_PROVISIONAL_HOLD(request)                                        // no balance debit or credit (FR-3.4)
        SET_REQUEST_STATUS(request, "Withdrawn")
        WRITE_AUDIT_EVENT("REQUEST_WITHDRAWN", employee, request.id)
    COMMIT TRANSACTION
    QUEUE_NOTIFICATION_WITH_RETRY(employee, request.id)                          // FR-6.2
END FUNCTION
```


### 6.6 Pseudocode: Approve, Deny or Cancel Vacation Request


```text
FUNCTION DECIDE_REQUEST(authenticated_actor, request_id, decision, comment, override_reason)
    BEGIN TRANSACTION
        request = LOAD_REQUEST_FOR_UPDATE(request_id)
        // Only one decision can be saved; a concurrent second decision is rejected (FR-3.14)
        IF request.status != "Pending Approval" THEN
            ROLLBACK
            REJECT("The request has already been decided or is no longer pending")
        END IF

        // Self-approval is not allowed, including through an override (FR-5.6, FR-12.8)
        IF authenticated_actor.employee_id = request.employee_id THEN
            ROLLBACK
            REJECT("You cannot decide your own request")
        END IF

        assigned_manager = GET_ASSIGNED_MANAGER(request.employee_id)
        authorized = IS_ASSIGNED_MANAGER(authenticated_actor, assigned_manager)                 // FR-3.2
                  OR IS_ACTIVE_DELEGATE(authenticated_actor, assigned_manager)                  // FR-3.2, FR-3.7
                  OR (assigned_manager IS NULL AND HAS_ROLE(authenticated_actor, "HR"))         // FR-3.3
                  OR (request.escalated = true
                      AND HAS_ROLE_IN(authenticated_actor, ["HR", "System Admin"]))             // FR-3.12
        override_used = false
        IF NOT authorized THEN
            // Only HR or a System Admin may override, and a written justification is mandatory (FR-8.1, FR-8.2)
            IF HAS_OVERRIDE_PERMISSION(authenticated_actor) AND override_reason IS NOT EMPTY THEN
                override_used = true
            ELSE
                ROLLBACK
                REJECT("You are not authorized to decide this request")
            END IF
        END IF

        IF decision = "Deny" AND comment IS EMPTY THEN                                         // FR-3.2
            ROLLBACK
            REJECT("A comment is required when denying a request")
        END IF

        previous_status = request.status
        IF decision = "Approve" THEN
            // Re-validate balance, overlap and applicable rules at the moment of approval (FR-3.5).
            // This request's own provisional hold is excluded. The notice period and the past-request window
            // are evaluated against the submission date, not the approval date.
            failures = REVALIDATE_AT_APPROVAL(request)
            IF failures IS NOT EMPTY THEN
                IF HAS_OVERRIDE_PERMISSION(authenticated_actor) AND override_reason IS NOT EMPTY THEN
                    FOR EACH failure IN failures
                        WRITE_AUDIT_EVENT("RULE_OVERRIDDEN", authenticated_actor, request_id,
                                          failure.rule, override_reason)                       // actor, time, rule bypassed, record (FR-8.2)
                    END FOR
                    override_used = true
                ELSE
                    ROLLBACK
                    REJECT_WITH_ALL_REASONS(failures)
                END IF
            END IF
            // Commit the decision and the balance allocation atomically (FR-3.5, FR-3.6)
            ALLOCATE_AVAILABLE_ENTITLEMENT_TO_RESERVED_LEAVE(request.employee_id, request.category, request.working_days)
            SET_REQUEST_STATUS(request, "Approved")
        ELSE IF decision = "Deny" THEN
            RELEASE_PROVISIONAL_HOLD(request)                                                  // no balance debit or credit (FR-3.4)
            SET_REQUEST_STATUS(request, "Denied")
        END IF

        SAVE_DECISION_AND_COMMENT(request, authenticated_actor, comment)
        WRITE_AUDIT_EVENT("REQUEST_DECIDED", authenticated_actor, request_id,
                          previous_status, request.status, override_used)                      // FR-9.1, FR-9.2
    COMMIT TRANSACTION
    QUEUE_NOTIFICATION_WITH_RETRY(request.employee, request_id)                                // FR-6.2; no medical details
END FUNCTION


FUNCTION CANCEL_APPROVED_LEAVE(authenticated_actor, request_id, cancellation_reason)
    // A written reason is mandatory for every cancellation, whatever the role (FR-1.8)
    IF cancellation_reason IS EMPTY THEN
        REJECT("A written cancellation reason is required")
    END IF

    BEGIN TRANSACTION
        request = LOAD_APPROVED_REQUEST_FOR_UPDATE(request_id)
        IF request IS NULL THEN
            ROLLBACK
            REJECT("Only an approved request can be cancelled")
        END IF

        // A scheduled working day becomes consumed at 00:00 in the employee's location time zone (FR-4.2)
        location_now = NOW_IN_TIME_ZONE(request.employee.location.time_zone)
        leave_started = location_now >= START_OF_DAY(request.first_scheduled_working_date)

        IF IS_REQUESTING_EMPLOYEE(authenticated_actor, request) AND leave_started = false THEN
            // Employees may cancel before 00:00 at the start of the first scheduled working day (FR-1.6)
            PROCEED
        ELSE IF HAS_ROLE_IN(authenticated_actor, ["HR", "System Admin"]) THEN
            // On or after the first scheduled working day only HR or a System Admin may cancel (FR-1.7).
            // One authorized action is sufficient; no second-person approval is required.
            PROCEED
        ELSE
            ROLLBACK
            REJECT("You are not authorized to cancel this leave")
        END IF

        elapsed_days = COUNT_SCHEDULED_WORKING_DAYS_WITH_LOCAL_START_AT_OR_BEFORE_NOW(request, location_now)
        future_days = request.total_working_days - elapsed_days
        IF future_days = 0 THEN
            ROLLBACK
            REJECT("No future working days remain to cancel")
        END IF

        SAVE_CANCELLATION_REASON(request_id, cancellation_reason)
        RELEASE_RESERVED_FUTURE_DAYS_TO_AVAILABLE_BALANCE(request, future_days)                // FR-4.3, FR-4.4
        KEEP_ELAPSED_DAYS_AS_CONSUMED(request, elapsed_days)
        IF elapsed_days = 0 THEN
            SET_REQUEST_STATUS(request, "Cancelled")
        ELSE
            SET_REQUEST_STATUS(request, "Partially Cancelled")
        END IF
        WRITE_AUDIT_EVENT("REQUEST_CANCELLED", authenticated_actor, request_id, cancellation_reason)
    COMMIT TRANSACTION
    QUEUE_NOTIFICATION_WITH_RETRY(request.employee, request_id)                                // FR-6.2
END FUNCTION
```


### 6.7 Pseudocode: Manage Delegation


```text
FUNCTION SCHEDULE_DELEGATION(authenticated_actor, manager_id, delegate_id, start_datetime, end_datetime)
    // A manager schedules the delegation (FR-3.7); HR may do so only if the manager is unavailable (FR-3.11).
    // A System Admin cannot create a delegation.
    IF NOT (authenticated_actor.employee_id = manager_id
            OR (HAS_ROLE(authenticated_actor, "HR") AND IS_MANAGER_UNAVAILABLE(manager_id))) THEN
        REJECT("You are not authorized to schedule this delegation")
    END IF
    IF start_datetime <= NOW OR end_datetime <= start_datetime THEN
        REJECT("A delegation must be scheduled before it begins and must end after it starts")
    END IF
    IF delegate_id = manager_id OR NOT IS_ACTIVE_EMPLOYEE(delegate_id) THEN
        REJECT("The delegate must be another active employee")
    END IF
    IF EXISTS_DELEGATION_OVERLAPPING_PERIOD(manager_id, start_datetime, end_datetime) THEN
        REJECT("Only one delegate may be authorized for a manager at a time; use a replacement instead")
    END IF
    BEGIN TRANSACTION
        delegation = CREATE_DELEGATION(manager_id, delegate_id, start_datetime, end_datetime, status = "Scheduled")
        WRITE_AUDIT_EVENT("DELEGATION_SCHEDULED", authenticated_actor, delegation.id)
    COMMIT TRANSACTION
    // The delegation covers all direct reports of the manager and pending requests
END FUNCTION

FUNCTION REPLACE_DELEGATE(authenticated_actor, delegation_id, new_delegate_id, effective_datetime)
    delegation = LOAD_DELEGATION_FOR_UPDATE(delegation_id)
    IF NOT (authenticated_actor.employee_id = delegation.manager_id
            OR (HAS_ROLE(authenticated_actor, "HR") AND IS_MANAGER_UNAVAILABLE(delegation.manager_id))) THEN
        REJECT("You are not authorized to replace this delegate")
    END IF
    IF effective_datetime <= NOW OR new_delegate_id = delegation.manager_id OR NOT IS_ACTIVE_EMPLOYEE(new_delegate_id) THEN
        REJECT("Invalid replacement")
    END IF
    BEGIN TRANSACTION
        // At the effective time the previous delegate loses authority and the replacement gains it for all
        // direct reports and pending requests; decisions already made are not changed (FR-3.9)
        SCHEDULE_REPLACEMENT(delegation, new_delegate_id, effective_datetime)
        WRITE_AUDIT_EVENT("DELEGATION_REPLACED", authenticated_actor, delegation_id)
    COMMIT TRANSACTION
END FUNCTION

FUNCTION REVOKE_DELEGATION(authenticated_actor, delegation_id, emergency_confirmed)
    delegation = LOAD_DELEGATION_FOR_UPDATE(delegation_id)
    IF authenticated_actor.employee_id = delegation.manager_id THEN                    // FR-3.10
        PROCEED
    ELSE IF HAS_ROLE(authenticated_actor, "HR") AND IS_MANAGER_UNAVAILABLE(delegation.manager_id) THEN   // FR-3.11
        PROCEED
    ELSE IF HAS_ROLE(authenticated_actor, "System Admin") AND emergency_confirmed = true THEN
        PROCEED                            // a System Admin may revoke only in emergency cases
    ELSE
        REJECT("You are not authorized to revoke this delegation")
    END IF
    BEGIN TRANSACTION
        SET_DELEGATION_STATUS(delegation, "Revoked", effective = NOW)
        WRITE_AUDIT_EVENT("DELEGATION_REVOKED", authenticated_actor, delegation_id, emergency_confirmed)
    COMMIT TRANSACTION
    // Pending requests stay actionable by the assigned manager, a current delegate or the HR escalation route
END FUNCTION

FUNCTION IS_ACTIVE_DELEGATE(actor, assigned_manager)
    // Authority exists only inside the configured period and only while the delegation is not revoked or replaced
    delegation = FIND_DELEGATION(assigned_manager.id, actor.employee_id)
    RETURN delegation IS NOT NULL
       AND delegation.status NOT IN ("Revoked", "Replaced")
       AND delegation.start_datetime <= NOW AND NOW < delegation.end_datetime
END FUNCTION
```


### 6.8 Pseudocode: Scheduled Lifecycle Jobs


```text
// Runs at 00:00 in each location's time zone (FR-4.2, FR-4.5)
FUNCTION DAILY_CONSUMPTION_AND_COMPLETION(location)
    FOR EACH request IN GET_APPROVED_OR_PARTIALLY_CANCELLED_REQUESTS_AT_LOCATION(location)
        FOR EACH working_day IN request.reserved_working_days
            IF working_day.date <= TODAY_IN_TIME_ZONE(location.time_zone) THEN
                RECLASSIFY_RESERVED_DAY_AS_CONSUMED(request, working_day)              // no second deduction
            END IF
        END FOR
        IF request.status = "Approved" AND ALL_SCHEDULED_WORKING_DAYS_HAVE_ELAPSED(request) THEN
            SET_REQUEST_STATUS(request, "Completed")
            WRITE_AUDIT_EVENT("REQUEST_COMPLETED", SYSTEM, request.id)
        END IF
    END FOR
END FUNCTION

// Delegations activate and expire automatically (FR-3.8)
FUNCTION DELEGATION_ACTIVATION_AND_EXPIRY()
    FOR EACH delegation IN GET_SCHEDULED_OR_ACTIVE_DELEGATIONS()
        IF delegation.status = "Scheduled" AND NOW >= delegation.start_datetime THEN
            SET_DELEGATION_STATUS(delegation, "Active")
        ELSE IF delegation.status = "Active" AND NOW >= delegation.end_datetime THEN
            SET_DELEGATION_STATUS(delegation, "Expired")
        END IF
    END FOR
END FUNCTION

// Reminder and escalation of requests that stay pending (FR-3.12, FR-6.3)
FUNCTION REMIND_AND_ESCALATE_PENDING_REQUESTS()
    FOR EACH request IN GET_PENDING_REQUESTS()
        pending_period = NOW - request.pending_since
        IF pending_period >= second_configured_period AND request.escalated = false THEN
            request.escalated = true                                                   // HR or a System Admin may now decide
            WRITE_AUDIT_EVENT("REQUEST_ESCALATED", SYSTEM, request.id)
            QUEUE_NOTIFICATION_WITH_RETRY(HR_OR_SYSTEM_ADMIN, request.id)
        ELSE IF pending_period >= first_configured_period AND request.reminder_sent = false THEN
            QUEUE_NOTIFICATION_WITH_RETRY(CURRENT_ACTIVE_APPROVER(request), request.id)
            request.reminder_sent = true
        END IF
    END FOR
END FUNCTION

// Runs when an imported change replaces an employee's assigned manager (FR-3.13)
FUNCTION ON_ASSIGNED_MANAGER_CHANGED(employee_id, new_manager)
    FOR EACH request IN GET_PENDING_REQUESTS_OF(employee_id)
        // A valid delegation or configured HR authority takes precedence over the new manager
        QUEUE_NOTIFICATION_WITH_RETRY(RESOLVE_APPROVER_TARGETS(request.employee, new_manager), request.id)
        WRITE_AUDIT_EVENT("REQUEST_REROUTED", SYSTEM, request.id)
    END FOR
END FUNCTION
```


## 7. External Interface Requirements


### 7.1 User Interface

- Provide role-based dashboards: employees see balances and requests; managers see approvals and team leave; HR and System Admin see their authorized administration, reporting and audit functions.


### 7.2 Software Interfaces


| Interface | Purpose | Notes |
| --- | --- | --- |
| OIDC identity provider (mock IdP in this project) | Authentication and identity | Mandatory; replaceable through configuration |
| HR source system | Employee profile data | Synchronized daily via FR-11.5 to FR-11.8 |
| Mail service | Notifications | Retry on failure |
| Internal consumer systems | Query vacation summaries | Via web service interface |




### 7.3 Web Service Interface 

- Retrieve an employee’s leave summary, including balances and requests by status.
- Support filters for employee, date range, status and category, plus retrieval of changes since a timestamp.
- The API shall be authenticated, authorized per consumer, versioned, documented and read-only. It shall return standard errors and only authorized fields.


## 8. Error Handling

- Return clear validation errors without changing request or balance data.
- Log and alert on HR data import failures. Failed imports shall not corrupt valid employee data.
- Retry and log failed email delivery without rolling back a saved request or decision.
- If approval is required and no manager is assigned, route the request to HR. If no HR user is available, do not auto-approve; follow the configured escalation path.
- Delegates may act only during their active period. Replacements take over at the configured time. Escalate overdue pending requests to HR or the System Admin.
- Reject unsupported, oversized or file-signature-mismatched attachments.

## 9. Acceptance Criteria


| ID | Acceptance criterion |
| --- | --- |
| AC-01 | An employee selects a date range and sees the number of working days calculated from the calendar configured for their effective work location before submission. |
| AC-02 | A request whose start date is after its end date is rejected with a clear validation message. |
| AC-03 | When an employee selects a date range containing only non-working days and public holidays, the system rejects it immediately and does not allow submission. |
| AC-04 | A request that contains at least one working day can proceed to the remaining validations. |
| AC-05 | The system does not offer or accept half-day leave. |
| AC-06 | A leave request that has been created but not yet submitted is in Draft state. The employee can edit the draft, and it is not routed for approval until it is submitted. |
| AC-07 | Minimum notice and request-length limits use working days. Exclude the submission date and first scheduled leave date from the notice count. Example: when a request is submitted on Monday and the applicable calendar marks Tuesday, Wednesday and Thursday as working days before a Friday leave start, the minimum-notice count is three working days; the Monday submission date and Friday leave start date are excluded, and non-working dates are not counted. |
| AC-08 | When retrospective requests are disabled, the system rejects all requests containing past leave dates. When enabled, it accepts a retrospective request only when its dates fall within the configured past-request window for its leave category. A historical view of the previous calendar year does not grant permission to submit a request for that entire period. |
| AC-09 | Two requests that share only non-working dates are not treated as overlapping. Two requests sharing at least one working date are treated as overlapping and the later request is rejected while the conflicting request is pending or approved. |
| AC-10 | A pending request reduces the amount available for validating a subsequent request as a provisional hold, but is not posted as approved reserved leave in the balance ledger. |
| AC-11 | When a pending request is withdrawn or denied, its provisional hold is released and no balance debit or credit is posted. |
| AC-12 | Two simultaneous submissions whose combined requested days exceed the employee's entitlement cannot both be accepted. |
| AC-13 | An employee explicitly configured as not requiring approval receives an auto-approved request, and the approval and balance allocation are recorded atomically. |
| AC-14 | An employee configured as requiring approval is routed to their assigned manager. |
| AC-15 | An employee configured as requiring approval but having no assigned manager is routed to the single organization-wide HR role, regardless of the employee's department or location, and is not automatically approved. |
| AC-16 | A request cannot be approved by a user who is not the assigned manager, the active delegate, a member of the organization-wide HR role or an authorized override user. |
| AC-17 | A denial cannot be saved without a comment. |
| AC-18 | On approval, the request's working days are committed against available entitlement and appear as approved-but-not-yet-taken (reserved) leave. |
| AC-19 | As scheduled working days pass, they are reclassified from reserved to consumed without a second deduction from available entitlement. At 00:00 on each scheduled working day in the employee’s location time zone, that day changes from reserved to consumed. The system does not claim to verify actual attendance. |
| AC-20 | An employee may cancel an approved request before its first scheduled working day. The cancellation is completed without HR authorization and all reserved working days are returned to available entitlement. |
| AC-21 | After the first scheduled working day begins, employees cannot cancel. HR or a System Admin may cancel future unelapsed days with a written reason; no second-person approval is required. |
| AC-22 | If a request is partially cancelled after its start date, the request is marked Partially Cancelled, future reserved working days are returned to available entitlement, and elapsed working days remain consumed. Example: if cancellation occurs after Tuesday 00:00 for Monday–Wednesday leave, Monday and Tuesday remain consumed and Wednesday is restored. |
| AC-23 | A cancellation cannot be completed without a written reason. |
| AC-24 | A manager can create a Personal leave award for a direct report only within configured award limits. The awarded days are committed against the recipient's entitlement and follow the reserved-to-consumed lifecycle. |
| AC-25 | A manager cannot approve, cancel or award their own leave. |
| AC-26 | A delegation applies to all direct reports of the delegating manager, is recorded before its configured start date/time, becomes active automatically at that time, and authorizes the delegate only until the configured end date/time, when its authority is removed automatically. A delegation cannot be used before its scheduled start or after its scheduled end. |
| AC-27 | An expired, revoked or replaced delegate cannot approve or deny requests after the relevant end/replacement effective time. Pending requests remain actionable by the current delegate, assigned manager or organization-wide HR escalation route, as applicable. |
| AC-28 | When an active delegation is replaced, the previous delegate loses authority and the replacement delegate gains authority for all direct reports and pending requests at the recorded replacement effective date/time. No two delegates are authorized for the same delegating manager at the same time, previous decisions remain unchanged, and the replacement is audited. |
| AC-29 | The delegating manager can schedule or revoke a delegation; HR may create, amend or replace it if the manager is unavailable; and a System Admin can revoke it only in emergency cases. Each change is audited. |
| AC-30 | If an assigned manager and an active delegate attempt to decide the same request concurrently, only the first valid decision is committed; the second operation is rejected. |
| AC-31 | If a request remains pending beyond configured reminder and escalation thresholds, the system sends the reminder and escalates it to HR/System Admin. |
| AC-32 | HR or the System Admin can maintain one effective working calendar per location. The system selects it from the employee’s work location. |
| AC-33 | A location change received through a successful HR import takes effect immediately. New calculations use the new location calendar; historical approved leave is not recalculated. |
| AC-34 | The System Admin can configure accrual, annual reset and carry-over parameters. Invalid or incomplete configurations cannot be activated. |
| AC-35 | The HR import runs once daily at the beginning of the organization’s configured working day and updates profile fields as specified in FR-11.5 to FR-11.8. Changes apply immediately after successful processing; pending requests use the updated manager assignment. |
| AC-36 | An HR import failure is logged and alerted without corrupting previously valid employee data. |
| AC-37 | An employee cannot view another employee's requests or attachments. A manager can access only information for their direct reports that is necessary for the manager's role. |
| AC-38 | Only the employee’s direct manager and HR can view medical details and supporting medical documents. Manager access is limited to direct reports; HR access is organization-wide. Access is audited. |
| AC-39 | Email notifications about sick leave or other sensitive leave categories do not include medical details or attachments; they link to the authenticated application. |
| AC-40 | Only PDF, DOCX, JPG/JPEG and PNG attachments within the configured size limit are accepted. Malware scanning is not performed or claimed in this release. |
| AC-41 | Attachments are stored privately, and a user who is not authorized cannot retrieve a file by guessing or reusing its URL. Authorized downloads are audited. |
| AC-42 | The system applies the retention schedule in Section 5.3, deletes expired active records as specified and allows backup copies to expire within 30 days. Attachments are deleted from active storage 12 months after the request reaches a terminal state; this rule does not determine retention for request records or audit logs. |
| AC-43 | Overrides, balance adjustments, awards, approval decisions, cancellations, delegation changes, calendar changes and sensitive-document access are recorded with actor, timestamp, action and affected record. |
| AC-44 | The HR report supports employee, department and leave-category filters and does not require project-based reporting. |
| AC-45 | The web service authenticates and authorizes each consumer and returns only fields permitted for that consumer. |
| AC-46 | A backup restoration test demonstrates that the documented RPO and RTO can be met once those values are approved. |
| AC-47 | Core employee, manager, HR and administration workflows are tested for keyboard operation, visible focus, labels, contrast and readable validation errors against the agreed WCAG 2.1 AA target. |
| AC-48 | Under a test load of 200 employees and 150 concurrent users, at least 95.5% of page loads complete within 3 seconds and normal request-processing operations complete within 2 seconds. |




## 10. Risks


| Risk | Mitigation |
| --- | --- |
| Inaccurate or delayed HR employee profile data | Validate imported fields, retain import diagnostics, alert on failures and allow authorized HR correction with an audit trail. |
| Conflicting rules across leave categories or location working calendars | Central configurable rule engine, effective-dated location calendars and automated boundary tests. |
| Race conditions on balances or simultaneous decisions | Transactional balance ledger, row locking or equivalent concurrency control, and re-validation at approval. |
| Email delivery failures | Retry and log delivery; preserve in-app request and approval status independently of email. |
| Sensitive leave information disclosed to unauthorized users | Least-privilege access, privacy-aware notifications, access auditing and defined HR permissions. |
| Malicious or unsafe uploaded attachments | Malware scanning is excluded from this release. Apply a strict file allowlist, signature validation, size limits, private storage and non-execution of uploaded content. This reduces some risks but does not detect malware; real sensitive documents should not be stored in a production deployment until an appropriate scanning or equivalent organizational control is introduced. |
| Unavailable manager or expired delegation | Configured HR approval authority, delegation expiry checks and pending-request escalation. |
| Incorrect location/calendar mapping | Effective-dated employee work-location records, location-calendar coverage checks, change audit and regression tests for date calculations. |
| Incorrect accrual or carry-over configuration | Configuration validation, preview/test calculations and approval of policy parameters before activation. |




## 11. Current Implementation Status

The UI and some business logic are still in progress. The data access level has been completed.


## 12. Requirements Traceability Matrix

The matrix links requirement groups to business rules, specification sections and acceptance criteria. It can be expanded to individual requirements during implementation.


| Requirement group | Related business rules | Supporting sections / design artifacts | Acceptance criteria |
| --- | --- | --- | --- |
| FR-1.1 to FR-1.16 - Leave request management | BR-1, BR-8, BR-9, BR-17, BR-24, BR-25 | Sections 3.1, 5.1, 6.5; Manage Time flowchart; employee sequence diagram | AC-01, AC-03 to AC-06, AC-20 to AC-23, AC-40, AC-41 |
| FR-2.1 to FR-2.9 - Leave rules-based validation | BR-1, BR-3, BR-4, BR-9, BR-10, BR-23 | Sections 3.2, 5.1; date-validation pseudocode | AC-02 to AC-10, AC-12 |
| FR-3.1 to FR-3.14 - Approval and delegation | BR-2, BR-12 to BR-14, BR-16 | Sections 3.3, 5.1, 6.6 to 6.8; request state machine; delegation lifecycle; manager sequence diagram | AC-11, AC-13 to AC-18, AC-26 to AC-31 |
| FR-4.1 to FR-4.5 - Leave request lifecycle | BR-15 to BR-17 | Sections 3.4, 5.1, 6.4, 6.6, 6.8; request state machine | AC-06, AC-19 to AC-22 |
| FR-5.1 to FR-5.6 - Manager features and awards | BR-5, BR-6, BR-18 | Sections 3.5, 5.1; manager dashboard and manager sequence diagram | AC-24, AC-25 |
| FR-6.1 to FR-6.5 - Notifications | No specific business rule | Sections 3.6, 7.2, 8; notification interface | AC-31, AC-39 |
| FR-7.1 to FR-7.11 - HR management, working calendars and policy configuration | BR-11, BR-12, BR-19, BR-22 | Sections 3.7, 5.1, 5.2, 7.2; entity diagram; HR configuration screens | AC-13, AC-15, AC-32, AC-33, AC-34 |
| FR-8.1 to FR-8.3 - Administrative overrides | BR-7, BR-22 | Sections 3.8, 5.1; audit workflow | AC-43 |
| FR-9.1 to FR-9.4 - Audit logging | BR-7, BR-14, BR-17 | Sections 3.9, 4, 5.2, 5.3; audit-log entity | AC-29, AC-42, AC-43 |
| FR-10.1 to FR-10.5 - Reporting | BR-20 | Sections 3.10, 5.1, 5.2; HR reporting interface | AC-44 |
| FR-11.1 to FR-11.9 - Integration and web services | BR-21 | Sections 3.11, 7.2, 7.3, 8; API contract and HR import flow | AC-33, AC-35, AC-36, AC-45 |
| FR-12.1 to FR-12.9 - Authentication and authorization | BR-5, BR-7 | Sections 3.12, 3.13, 4, 7.2; OIDC login and role model | AC-16, AC-25, AC-37, AC-38 |
| NFR-1 and NFR-2 - Usability and accessibility | Not applicable; quality constraints | Section 4; user-interface design and accessibility test evidence | AC-01 to AC-05, AC-47 |
| NFR-3 to NFR-5 - Security, privacy and interoperability | BR-7, BR-21 | Sections 4, 7, 8; security design and role/permission matrix | AC-37 to AC-39, AC-45 |
| NFR-6 to NFR-9 - Performance, scalability, availability and integrity | BR-2, BR-15 | Sections 4, 5.1, 6.5, 6.6; performance and concurrency test results | AC-10 to AC-12, AC-18 to AC-22, AC-30, AC-48 |
| NFR-10 to NFR-14, NFR-17 - Recovery, maintainability, deployment, localization, observability and audit integrity | BR-1, BR-7, BR-11, BR-19 | Sections 4, 5, 8, 10; deployment, backup and audit plans | AC-32 to AC-35, AC-42, AC-43, AC-46 |
| NFR-15 and NFR-16 - Attachment security, delivery and retention | BR-24, BR-25 | Sections 4, 5.3, 7, 8; secure file-storage design | AC-37, AC-38, AC-40 to AC-42 |




