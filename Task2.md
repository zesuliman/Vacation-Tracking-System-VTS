**Q1: Imagine the UI of Requests displayed to the Manager or Employee. What if we need to have in the future another status like HR_Pending, HR_Approval with minimum change?**

**A1:** To ensure the UI for Managers and Employees can accomadate the new statueses with minimal code change, we can imporve the exisitng database architecture. According to the database design, there is a table called Vacation_Status. The table holds data about the different statuses of a vacation request. We can utilize this table to modifiy the application logic. The HR_Pending and HR_Approval statuses can be added to the Vacation_Status table. A column called Status_Sequnce is responsible of defining the progression of the request status. By updating the Status_Sequence values, we can determine the order of operations ensuring which status follows the other one.
**Q2: Write the pseudo-code for the two cases above**
**A2:** 
**Q3: Draw the state machine of the request. [ch5]**
**Q4: Draw the sequence diagram of the request. [ch5]**
**Q6: Complete the rest of the use-case of this chapter [Cancel - Edit Pending Request] Flowchart, sequence diagram - check database change**

