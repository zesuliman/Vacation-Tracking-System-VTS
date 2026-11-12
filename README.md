# 🌴 Vacation Tracking System (VTS)
 
## This repository provides an implementation example for a dynamic Vacation Tracking System designed for a single organization with multiple departments, locations and managers, where each employee reports to exactly one manager configured in the system.
 
The system enables employees to manage their leave and time-off requests while providing managers with a centralized view to approve and monitor the leave of their direct reports. Organization-wide HR staff maintain employee leave data and leave policies, and use the system's reports.
 
## ✨ Key Features
* **Rules-Based Validation:** A flexible, configurable rules system validates every leave request. The System Admin configures, per leave category, minimum and maximum request length, minimum notice period, blackout periods and retrospective-request windows, plus one working calendar (with time zone) per location, so working days, weekends and public holidays are calculated for each employee's work location.
* **Self-Service Leave:** Employees create and edit drafts, submit requests, edit or withdraw pending requests, and cancel approved leave before it starts. Balances distinguish available, pending, reserved and consumed leave.
* **Single-Manager Approval Workflow:** Requests are approved or denied by the employee's assigned manager (a denial requires a comment). Approval can be configured per employee, so some employees are auto-approved, and requests from employees with no manager are routed to HR.
* **Delegation and Escalation:** Managers can schedule, replace and revoke a delegate for exceptional cases. HR can manage a delegation when the manager is unavailable, and a System Admin can revoke one only in emergency cases. Only one approver decides a request, and pending requests trigger reminders and then escalation to HR or a System Admin.
* **Manager Awards:** Managers can directly award personal leave to their direct reports as approved leave on specific dates, within system-set limits.
* **Extended Timeline:** Provides access to requests for the previous calendar year, and allows requests to be made up to 18 months in the future.
* **Automated Email Alerts:** Email notifications ask the approver to act and notify employees of every status change. Sensitive medical details are never included in emails.
* **Audit Trails:** Keeps comprehensive activity logs for all transactions.
* **HR Management:** HR personnel securely enter and update employee leave data, make logged balance adjustments, and handle employment terminations. Accrual, annual reset and carry-over are driven by configurable parameters.
* **Admin Overrides:** HR and System Admin personnel can override actions restricted by rules, with a mandatory written justification and a full audit record.
* **Attachments and Privacy:** Supporting documents (PDF, DOCX, JPG/JPEG, PNG) are stored privately, and medical details are visible only to the employee's direct manager and HR.
* **System Integration:** A read-only, authenticated web service API lets other internal systems retrieve employee vacation request information and updates. Employee profile data is imported daily from the HR source system.
* **Reporting and Statistics:** HR personnel can view balances, usage, request status, absence trends and override history by employee, department and leave category, with CSV and PDF export.
* **Authentication:** Users sign in through an OpenID Connect (OIDC) identity provider. This project includes a mock identity provider with seeded test users, and a real provider can replace it through configuration only.
## ⚙️ Core Engineering Challenges
* **Business Logic:** Validating vacation request dates against working calendars per location, balances, pending holds, overlaps, notice periods and blackout periods, while keeping balance updates consistent under concurrent requests and decisions.
* **Data Modeling:** Building an efficient relational data model to accurately store the employee-manager hierarchy (one manager per employee), delegations, request lifecycles and balance records.
* **Security:** Implementing robust, role-based access control to ensure strict separation between employee, manager, HR and System Admin permissions, including restricted access to sensitive medical information.
## 📄 Documentation
The full requirements, business rules, data model, flow charts, sequence diagrams, pseudocode, acceptance criteria and traceability matrix are in the [Software Requirements Specification (SRS)](./VTS_SRS_v2_6.md).
 
## 🚀 Getting Started
 
Setup instructions will be added as the implementation is completed. Authentication in this project uses a mock OIDC identity provider with seeded test users for each role.
   
## High-Level System Design

<img width="4542" height="4003" alt="image" src="https://github.com/user-attachments/assets/76e02d8c-53d6-40a1-b9aa-c1c0c28e214f" />

💡 How to Tweak This Project for Your Own Uses
Since this is an example project, I encourage you to clone and rename this repository to use for your own purposes. It is a great starter boilerplate!

🐛 Find a Bug?
If you find an issue or would like to submit an improvement to this project, please submit an issue using the Issues tab above. If you would like to submit a Pull Request (PR) with a fix, please reference the issue you created!

🚧 Known Issues (Work in Progress)
This project is still ongoing. The user interface and part of the business logic have not been completed yet. This is coming soon!

☕ Like this project?
If you find this project helpful and are feeling generous,  [buy me a coffee!](https://buymeacoffee.com/zeinab.ibrahim?new=1) 
