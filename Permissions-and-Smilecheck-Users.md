We use Django auth.

**Username**: Their firstname.lastname@gmail.com
* If they have more than 2 names, please consult with whoever submitted the ticket for the appropriate username. 
**Password**: In prod, it should be set as smilecheck by the system. They will be required to reset on first login to the staff portal. In other environments, it should default to letmein. 

Generally, engineering users are made superusers

For SmileShop/Retail User:
* auth_group - Patient Care Specialist
* employee_title - Smile Technician (must be this title because it ties into other functionality)
* employee_department - Scanning Services
* Further action will need to be taken. In the admin page, in locations/scan_markets, click on add scan market. Select the smile technician that was just added. Enter the zipcode of the store they are working. You'll have to add your email or the system email to created by and updated by. Hit save. This adds that smile technician to the drop down in the staff portal for completed appointments. 

For Contact Center User (customer care):
* auth_group - Patient Care Specialist
* employee_title - Customer Care Specialist
* employee_department - Customer Care

For Contact Center User (scheduling):
* auth_group - Patient Care Specialist
* employee_title - Scheduler
* employee_department - Scanning Services

For New Lab User:
* auth_group - Lab
* employee_title - Lab Assistant (if nothing is provided with the description)
* employee_department - Lab

For New Lab User (Fulfillment):
* auth_group - Lab, Fulfillment Specialist
* employee_title - Fulfillment Specialist
* employee_department - Lab

For Technology:
* auth_group - Engineering
* employee_title - whatever is provided
* employee_department - Engineering
* Usually a superuser
* If a software developer/devops, we will need to setup other accounts as well

You can consult the employee spreadsheet for further titles, departments, and auth group matchups. https://docs.google.com/spreadsheets/d/16NMYJVj9v324hXZ6vsaTSjtK6pJUDlburNhRgB24a9I/edit#gid=0

If someone submits a request for access to the reports page, assign them to the Reports-Super auth_group. 
If someone submits a request for access to the operations, shipping batch page, assign them the one-off permission of 'can view shipment batch'

Need to add further permission information...





