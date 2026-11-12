# 🌴 Vacation Tracking System (VTS)

## An implementation example of a leave-management system for a single organization with multiple departments, locations and managers. Each employee reports to exactly one manager configured in the system.

Employees manage their own leave, managers approve and monitor the leave of their direct reports, and HR maintains leave data and policies and uses the system's reports.

## ✨ Key Features
* **Configurable Validation:** Every request is checked against admin-configured rules (request length, notice period, blackout periods, retrospective window, and the employee's working calendar).
* **Self-Service Leave:** Employees create drafts, submit requests, edit or withdraw pending requests, and cancel approved leave before it starts. Balances show available, pending, reserved and consumed leave.
* **Single-Manager Approval:** The employee's assigned manager approves or denies each request. HR can configure approval as not required for specific employees.
* **Delegation and Escalation:** Managers can schedule, replace or revoke a delegate. Overdue requests trigger reminders, then escalation to HR or a System Admin.
* **Manager Awards:** Managers can award personal leave to their direct reports within system-set limits.
* **Extended Timeline:** View requests from the previous calendar year and request leave up to 18 months ahead.
* **Email Notifications:** Approvers and employees are notified of requests and status changes.
* **Audit Trail:** All transactions are logged, including rule overrides, which require a written justification.
* **HR Role:** HR maintains employee leave data, approval settings, balance corrections and terminations, handles requests with no assigned manager, and can override rules.
* **Attachments and Privacy:** Supporting documents (PDF, DOCX, JPG/JPEG, PNG) which are stored privately. Medical details are visible only to the employee's direct manager and HR.
* **Integration:** A read-only, authenticated web service API for other internal systems, and a daily employee-data import from the HR source system.
* **Reporting:** HR and System Admins can view and export reports by employee, department and leave category.
* **Authentication:** Athentication uses OpenID Connect (OIDC). This project includes a mock identity provider with seeded test users, and a real provider can replace it through configuration only.

## ⚙️ Core Engineering Challenges
* **Business Logic:** Validating dates against location calendars, balances, pending holds, overlaps, notice periods and blackout periods, while keeping balances consistent under concurrent requests and decisions.
* **Data Modeling:** Storing the employee-manager hierarchy, delegations, request lifecycles and balance records in an efficient relational model.
* **Security:** Role-based access control with strict separation between employee, manager, HR and System Admin permissions, including restricted access to medical information.

## 📄 Documentation
Requirements, business rules, data model, diagrams, pseudocode, acceptance criteria and traceability matrix are in the [Software Requirements Specification (SRS)](./VTS_SRS_v2_6.md).

## 🚀 Getting Started

Setup instructions will be added as the implementation is completed. Authentication uses a mock OIDC identity provider with seeded test users for each role.

## High-Level System Design

<img width="4542" height="4003" alt="image" src="https://github.com/user-attachments/assets/76e02d8c-53d6-40a1-b9aa-c1c0c28e214f" />

## 💡 How to Tweak This Project for Your Own Uses
Since this is an example project, I encourage you to clone and rename this repository to use for your own purposes. It is a great starter boilerplate!

## 🐛 Find a Bug?
If you find an issue or would like to submit an improvement to this project, please submit an issue using the Issues tab above. If you would like to submit a Pull Request (PR) with a fix, please reference the issue you created!

## 🚧 Known Issues (Work in Progress)
This project is still ongoing. The user interface and part of the business logic have not been completed yet. This is coming soon!

## ☕ Like this project?
If you find this project helpful and are feeling generous,  [buy me a coffee!](https://buymeacoffee.com/zeinab.ibrahim?new=1)
