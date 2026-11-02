# 🌴 Vacation Tracking System (VTS)

## This repository provides an implementation example for a dynamic Vacation Tracking System designed for institutions with multi-manager organizational structures. 

The system enables employees to manage their leave and time-off requests while providing managers with a centralized view to track and schedule leaves across multiple projects and cross-functional teams.

## ✨ Key Features
* **Rules-Based Validation:** Implements a flexible rules-based system for validating and verifying leave time requests.
* **Self-Service Leave:** Enables employees to seamlessly manage their own vacation time.
* **Manager Approval and Award Workflows:** Supports optional manager approval. Also allows managers to directly award personal leave time (within system-set limits).
* **Extended Timeline:** Provides access to requests for the previous calendar year, and allows requests to be made up to 18 months in the future.
* **Automated Email Alerts:** Uses email notifications to request manager approvals and notify employees of request status changes.
* **Audit Trails:** Keeps comprehensive activity logs for all transactions.
* **HR Management:** Enables HR personnel to securely enter and update employee vacation data in the system.
* **Admin Overrides:** Enables HR and system administration personnel to override actions restricted by rules.
* **System Integration:** Provides an API interface to allow other internal systems to retrieve employee vacation request information and updates.
* **Reporting and Statistics** provide a variety of reports for HR Personnel to display.

## ⚙️ Core Engineering Challenges
* **Business Logic:** Handling the complex logic required to validate vacation request dates against employee balances and calendar constraints.
* **Data Modeling:** Building an efficient relational data model to accurately store user hierarchies and vacation requests.
* **Security:** Implementing robust, role-based access control to ensure strict separation between employee, manager, and HR permissions.


## 🚀 Getting Started
   
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
