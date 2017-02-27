SmileShop data is populated into the database from our shared google spreadsheet by using pyrunner -xf 0009_stores.py. 

This worked fairly well for getting the original stores up and running quickly. It has become unwieldy when trying to manage all the new stores. Hopefully SAMS (Store Appointment Management System) will help fix many of the issues. The current store appointment system is setup on the concept of generating set appointment slots that get booked by staff and customers. Once slots are generated, it is challenging to change them. 

Appointment slots have 3 statuses OPEN, LOCKED, BOOKED. 
OPEN - it can be booked 
LOCKED - cannot book (staff can override this in the staff portal) 
BOOKED - appointment is booked 

ISSUES WITH CURRENT SYSTEM: 
* Very little flexibility for changing store, chair, day hours (which happens a lot at SDC)
* Hard to set start and end dates for chairs and days.
* Requires an engineer to change times
* Many other issues that I can't think of


If you see, find, or create any better ways to do the following, please update!

**ADDING NEW STORES**:
We have a store spreadsheet. It is not environment restricted. So if you change anything and pyrunner is run for the store script (0009_stores.py), then it will impact whatever environment it is run on. When I say run pyrunner in this file, I'm referring specifically to 0009_stores.py. 

1. Wrangle the data provided. Make sure you have all the information necessary information. (store name, address, chairs, chair hours, store instructions, active zipcodes). Add it to the spreadsheet following the existing format.
2. Run pyrunner -xf 0009_stores.py locally. The store will only show up in the staff portal if the date_start is less than or equal to the day you're running it. You may need to adjust this accordingly. Usually you will need to set the store start date earlier than the actual store start date because they will want to start scheduling a week or two before. Generate slots locally. Make sure they look like what you're expecting. 
3. Once ready, run on staging. Get Jessica, Jordan, and QA to review. If you want to be fancy, you'll need to block out the first week of slots. Generally you are making the store go live a week before they want anyone booked. Slots will get generated for that week in-between so you will want to close them out (can be done from the shell). Another option is change the store hours so lunch blocks out all the slots, run pyrunner. Generate slots for the next week. Change the hours back to what is requested, run pyrunner, generate slots for the go live date and following weeks. 
4. Slots will not be bookable from the ecommerce website until the zipcodes are active. 
5. Once you get the thumbs up, the data can be run into prod. You will need to complete some of the pyrunner or data manipulation mentioned above. It is always good to test one zipcode live before adding all of them. Usually Jessica or Jordan are willing to book an appointment in prod to test and see the email. 

**CHANGING HOURS**:

Extending Hours:
* If the store hours are extending (staying open later or starting earlier), there is not a good way to just tack on a few extra slots. You'll need to change the times in the spreadsheet and run pyrunner. Test locally and in staging. This will work for slots that have not been generated yet. The only way that I know of to change that times of slots already generated is to logically delete the existing slots and generate new slots. This becomes problematic when there are already appointments booked in the slots which is why it is very nice to have 3 weeks notice for this (not that you'll get it). You'll need to keep track of the current appointments on each day and reschedule them in the new slots. Potentially, if the slots start earlier, the appointment times may start at different times which means that the scheduling department should call the customers. 

Reducing Hours:
* This is a little easier to manage than adding hours. You'll need to change the times in the spreadsheet and run pyrunner. Test locally and in staging. This will work for slots that have not been generated yet. If slots have been generated and you need to change those times, you will need to shell in, grab the slots you need to close, and change the status to LOCKED. 


Removing Chairs:
* Unfortunately, there is not a date_end for a chair. If you logically delete a chair, it will disappear from the calendar immediately. You'll need to make sure all the appointment slots previously generated are locked or removed. You'll also need to make sure there are no booked appointments on that chair. You'll also need to remove the chair from the spreadsheet. 
* What I usually try to do is to phase out the chair. To do this, I leave the chair in the spreadsheet but change the lunch time to complete lock that chair. Run pyrunner. This should lock all new slots generated for that chair.
* You can lock open time slots for that chair from the shell. Once there are no more open/booked time slots for that chair, you can date_remove it safely.  

Adding Chairs:
* When you add a chair to the spreadsheet and run pyrunner, the nightly task will generate slots for that chair starting the next day even if the rest of that day's appointment times were already generated. If they want the chair to open immediately, this isn't a problem but usually they want the chair to start 1-2 weeks out. (Would be great to have a chair start and end date)
* You'll need to be careful about updating the store hours (for the future) and adding a new chair at the same time. It would be best to add the new chair with the same hours but a LOCKED status. Make sure those slots are generated for all the days before new hours start. Then update the hours for the chair and the store in the spreadsheet and run the new hours into the database. This way the nightly task will not generate slots for the new chair with new hours before you're expecting them. (This is what happened when adding a 3rd chair to the Nashville store as well as updating the hours to start an hour earlier)


THINGS TO KNOW:
* We have a nightly task (DailySchedulingSlotsTask) that runs and generates slots for the next 18 days for all the slots. Sometimes this has unexpected consequences if adding new hours, stores, or chairs. Task is located in core/locations/tasks.py  
 

HANDY CODE SNIPPETS (probably could be made better):
    
    Example for grabbing appointment slots
    slots = StoreAppointmentSlot.objects.filter(slot_month=9, slot_day__in=[17], slot_year=2016, slot_hour__in=[12], slot_minute=30,  store_calendar__store_id=4)

    slots = StoreAppointmentSlot.objects.filter(slot_month=9, slot_day__in=[17], slot_year=2016, slot_hour__in=[19], store_calendar__store_id=4, store_calendar__name__in=['Chair 4', 'Chair 5', 'Chair 6'],current_status_id='OPEN')
    
    Example Locking slots
    for slot in slots:
      slot.set_status(‘LOCKED’)   #deleting slots is similar but slot.delete()
      slot.save()

    Example If you have added a chair and want to generate the slots for that chair
    store = Store.objects.filter(id=4)
    num_days = 1  (however many days of slots you want to generate from the date_begin)
    date_begin = '2017-03-16'
    for s in store:
      store.create_slots(date_begin=date_begin, num_days=num_days)


