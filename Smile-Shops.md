SmileShop data is populated into the database from our shared google spreadsheet by using pyrunner -xf 0009_stores.py. 

This worked fairly well for getting the original stores up and running quickly. It has become unwieldy when trying to manage all the new stores. Hopefully SAMS (Store Appointment Management System) will help fix many of the issues. 

Issues with the current system: 
* Very little flexibility for changing store, chair, day hours (which happens a lot at SDC)
* Hard to set start and end dates for chairs and days.
* Requires an engineer to change times
* Many other issues that I can't think of

The store appointment system is setup on the concept of generating set appointment slots that get booked by staff and customers. Once slots are generated, it is challenging to change them.

Gotchas: 
* If you are adding a new chair to a store, the nightly task will generate slots for that chair. So you need to be careful about updating the store hours for the future and adding a new chair at the same time. It would be best to add the new chair with the same hours but a LOCKED status. Make sure those slots are generated for all the days before new hours start. The update the hours for the chair and the store in the spreadsheet and run the new hours into the database. This way the nightly task will not generate slots for the new chair with new hours before you're expecting them. 



