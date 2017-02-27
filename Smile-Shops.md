SmileShop data is populated into the database from our shared google spreadsheet by using pyrunner -xf 0009_stores.py. 

This worked fairly well for getting the original stores up and running quickly. It has become unwieldy when trying to manage all the new stores. Hopefully SAMS (Store Appointment Management System) will help fix many of the issues. The current store appointment system is setup on the concept of generating set appointment slots that get booked by staff and customers. Once slots are generated, it is challenging to change them.

ISSUES WITH CURRENT SYSTEM: 
* Very little flexibility for changing store, chair, day hours (which happens a lot at SDC)
* Hard to set start and end dates for chairs and days.
* Requires an engineer to change times
* Many other issues that I can't think of


ADDING NEW STORES:
We have a store spreadsheet. It is not environment restricted. So if you change anything and pyrunner is run for the store script (0009_stores.py), then it will impact whatever environment it is run on. When I say run pyrunner in this file, I'm referring specifically to 0009_stores.py. 

1. Wrangle the data provided. Make sure you have all the information necessary information. (store name, address, chairs, chair hours, store instructions, active zipcodes). Add it to the spreadsheet following the existing format.
2. Run pyrunner -xf 0009_stores.py locally. The store will only show up in the staff portal if the date_start is less than or equal to the day you're running it. You may need to adjust this accordingly. Usually you will need to set the store start date earlier than the actual store start date because they will want to start scheduling a week or two before. Generate slots locally. Make sure they look like what you're expecting. 
3. Once ready, run on staging. Get Jessica, Jordan, and QA to review. If you want to be fancy, you'll need to block out the first week of slots. Generally you are making the store go live a week before they want anyone booked. Slots will get generated for that week in-between so you will want to close them out (can be done from the shell). Another option is change the store hours so lunch blocks out all the slots, run pyrunner. Generate slots for the next week. Change the hours back to what is requested, run pyrunner, generate slots for the go live date and following weeks. 
4. Slots will not be bookable from the ecommerce website until the zipcodes are active. 
5. Once you get the thumbs up, the data can be run into prod. You will need to complete some of the pyrunner or data manipulation mentioned above. It is always good to test one zipcode live before adding all of them. Usually Jessica or Jordan are willing to book an appointment in prod to test and see the email. 

CHANGING HOURS:
Extending Hours:

Reducing Hours:

Removing Chairs:


THINGS TO KNOW:
* We have a nightly task (DailySchedulingSlotsTask) that runs and generates slots for the next 18 days for all the slots. Sometimes this has unexpected consequences if adding new hours, stores, or chairs. Task is located in core/locations/tasks.py  
* If you are adding a new chair to a store, the nightly task will generate slots for that chair. So you need to be careful about updating the store hours for the future and adding a new chair at the same time. It would be best to add the new chair with the same hours but a LOCKED status. Make sure those slots are generated for all the days before new hours start. Then update the hours for the chair and the store in the spreadsheet and run the new hours into the database. This way the nightly task will not generate slots for the new chair with new hours before you're expecting them. 
* If the store hours are extending (staying open later or earlier), there is not a goo



