# Project Description

A dynamic Vacation Tracking System designed for multi-manager organizational structures. This system enables employees to manage their leave and time-off requests while providing managers with a centralized view to track and schedule leaves across multiple projects and cross-functional teams.

## Vision
To improve the organization internal processes in terms of vacation management and make it faster and cost-efficient.

## Goal
A Vacation Tracking System (VTS) will provide individual employees with the capability to manage their own vacation time, sick leave, and personal time off, without having to be an expert in company policy or the local facility's leave policies. In addition, the automated system will assist managers to approve/deny the vacation time requests introduced by their subordinates and some high-level employees may not even require approval. The HR personnel will no longer be responsible of requesting and validating each request.

## Functional Requirements

1. Implements a flexible rules-based system for validating and verifying leave time requests.
2. Enables employees to manage their vacation time.
3. Enables manager approval (optional).
4. Provides access to requests for the previous calendar year, and allows requests to be made up to a year and a half in the future.
5. Uses e-mail notification to request manager approval and notify employees of request status changes.
6. Uses existing hardware and middleware.
7. Is implemented as an extension to the existing intranet portal system, and uses the portal's single-sign-on mechanisms for all authentication.
8. Keeps activity logs for all transactions.
9. Enables HR personnel to enter and update employee vacation data in the system.
10. Enables the HR and system administration personnel to override all actions restricted by rules, with logging of those overrides.
11. Allows managers to directly award personal leave time (with system-set limits).
12. Provides a Web service interface for other internal systems to query any
given employee’s vacation request summary.
13. Interfaces with the HR department legacy systems to retrieve required
employee information and changes

## Non-Functional Requirements

1. Usability: easy to use.
2. Accessiblity: the system must be acessible therough a browser.
3. Security: the system must utilize the existing intranet single-sign on capability.
4. Interoperability: the system must be able to communicate with existing legacy systems.

## Constraints

1. The system must be web-based and accessible to users via a web browser.
2. The system must be an extension to the existing portal system.
3. The system must use the existing sign-on mechanism for all authentication.

## Domain Problem
Many organizations face the problem of tracking their employees vacation time. Managers face the difficuilty of managing and monitoring their subordinates vacation time due to the limited casual interactions. Also, employees at the other hand need to be responsible of managing their own time-off. The system is designed and developed for both employees and managers to help them coordinate granting and scheduling leave time. At the other hand the HR department will benefit by minimizing their intervention in this aspect and focusing on core business activites.
Moreover, organizations and large enterprieses more often have their own running systems that lack this feature. So the Vacation Tracking system must be built in consideration with the existing other systems in the enterprieses' space.

## Actors
1. Employee.
2. Manager.
3. HR Clerk.
4. System Admin.

   
## For - Manage Time - use case
1. Entities (Data Model)
2. Flow Chart
3. Sequence Diagram
4. Pseudocode

```pseudocode
INPUT req_employee_id, selected_vacation_category

CONNECT TO database "VTSDatabase"

CREATE OBJECT Employee employee
READ FROM Employees WHERE employee_id = req_employee_id INTO employee

READ FROM Vacation_Time_Balance 
WHERE employee_id = req_employee_id AND vacation_category_id = selected_vacation_category 
INTO remaining_balance_value

IF remaining_balance_value > 0 THEN
    
    INPUT vacation_request_date, vacation_request_time
    INPUT title, description
    
    IF validate(vacation_request_date) = true AND
       validate(vacation_request_time) = true AND
       validate(title) = true AND
       validate(description) = true THEN
        
        CREATE OBJECT VacationRequest vacationRequest
        
        SET vacationRequest.vacation_request_date = vacation_request_date
        SET vacationRequest.vacation_request_time = vacation_request_time
        SET vacationRequest.title = title
        SET vacationRequest.description = description
        SET vacationRequest.status = "pending"
        
        IF employee.hasManager = true THEN
            CALL send_email(
                to = employee.manager_email, 
                subject = "Vacation Request", 
                body = vacationRequest
            )
        END IF
        
        OUTPUT "Vacation request submitted successfully"
        
    ELSE
        OUTPUT "Incomplete or Incorrect Information"
    END IF
    
ELSE
    OUTPUT "Insufficient balance"
END IF

DISCONNECT
```
