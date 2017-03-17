SmileShop data is populated into the database from our shared google spreadsheet by using pyrunner -xf 0009_stores.py. 
https://docs.google.com/spreadsheets/d/1kRBPu6h9rrOpAFwNweXxEOwzwP1bR72LZz4d5XEXzUc/edit#gid=0

This worked fairly well for getting the original stores up and running quickly. It has become unwieldy when trying to manage all the new stores. Hopefully SAMS (Store Appointment Management System) will help fix many of the issues. The current store appointment system is setup on the concept of generating set appointment slots that get booked by staff and customers. Once slots are generated, it is challenging to change them. 

StoreAppointment Model: 
* Contains the customers appointment status
* Has an FK to a specific StoreAppointmentSlot

StoreAppointmentSlot:
* Contains the date and time of the appointment
* Has a FK to the StoreCalendar (specific store chair)

Appointment slots have 3 statuses OPEN, LOCKED, BOOKED. 
OPEN - it can be booked 
LOCKED - cannot book (staff can override this in the staff portal) 
BOOKED - appointment is booked 

StoreCalendar:
* These are essentially the chairs in the store


ISSUES WITH CURRENT SYSTEM: 
* Very little flexibility for changing store, chair, day hours (which happens a lot at SDC)
* Hard to set start and end dates for chairs and days.
* Requires an engineer to change times
* Many other issues that I can't think of


If you see, find, or create any better ways to do the following, please update!

**THE SPREADSHEET**:
We have a store spreadsheet. It is not environment restricted. So if you change anything and pyrunner is run for the store script (0009_stores.py), then it will impact whatever environment it is run on. When I say run pyrunner in this file, I'm referring specifically to 0009_stores.py. 

The spreadsheet has four sub-sheets which all serve a different purpose. They are *Calendars*, *Holidays*, *ServiceAreas*, and *Zipcodes*. While the changes described in the following section might not require modification of all of these, general knowledge of them should ease the process significantly.

* Calendars:
    
    This sheet is the main sheet where most of the tasks required for creating and maintaining the stores are done. The fields in the spreadsheet are as follows from left to right:
    * num
    
        This is the store number. At this time the store number is determined by the engineer doing the store creation. There are no hard and fast rules however the standard is each different city or metro area is represented by the hundreds/thousands place of the number and each of the shops with-in that area is represented by the ones/tens place. For example, take the store number for the store at 1920 McKinney Ave in Dallas. The number for this store is 701, the 7 representing the city/area the store is in (Dallas) and the 1 representing that this store is the first store opened in that area. If another store were to be opened in Dallas the number of that store would be 702.
    * store__name

        This is the name that the store is normally called. Usually, this is just the name of the city in which the store is located. This practice is acceptable so long as there is only one store in that city. Should there be more than one store located in the same area a more creative naming scheme might need to be devised.
    * store__contact_name

        This is more or less the same as the above.
    * store__stree1

        The street address of the store. For example, 1920 McKinney Ave
    * store__street2

        Additional address information for the store, the floor of the building in which the store is located, for instance.
    * store__locality

        The city in which the store is located.
    * store__region
    
        The two character code for the state in which the store is located.
    * store__postal_code

        The postal code at the store's address. Note: google sheets will strip leading 0's off of addresses which have them, this is ok as the script which brings the number into the database will add a leading 0 to codes which are only 4 numbers long.
    * store__country_id

        The country in which the store is currently located. At present this field is always US.
    * store__phone

        The phone number of the store appointment schedualing team: 800-688-4010
    * store__date_open

        The date after which the store will appear and be available for appointments on both the ecommerce and staff portal sites. This value is usually set to the day during which the store is being created. This is to allow for testing locally and in staging. The actual launch of the store slots for scheduling is accomplished by a different process.
    * store__date_close

        Date of store closure. Not needed for store creation.
    * calendar__kind

        This value simply indicates that the row cells following this one are chairs in the store or not. The only value used here is 'Chair'.
    * calendar__name

        The name of the chair to be used in the system. Values follow the naming scheme: Chair 1, Chair 2, Chair 3, etc.
    * weekday lunch

        This field takes time inputs and causes sections of time to not be available for scheduling. Note: the name of this field is misleading, it does not define lunch break times it is instead used to block off times as not available by the engineer entering the information. This is most often used when certain chairs are required to have slot discontinuities during the day when the others have available slots during the entirety of the store's operating hours.
    * Monday ... Friday

        These fields determine the hours available for scheduling during the day.
    * weekend lunch

        Like weekday lunch above, but for the weekend.
    * Saturday ... Sunday

        Like the weekday fields but for the other days.
    * duration

        The length of the appointment in minutes. This is usually left blank and will default to 30 minutes if no other value is given.
    * weekday instructions

        Customer facing information supplied by the business. This information should be on the jira ticket.
    * weekend instructions

        See above.

* Holidays:

    This sheet describes holidays so that slots will not be mistakenly generated for those days. The dates included here will need to be updated annually for as long as this system is used.

* ServiceAreas:

    The information shown on this sheet is which zipcodes are within the service range of our stores. These values are used to identify which customers can be directed to the stores during account registration. This sheet is the one which will probably see the most direct action after the Calendars sheet. The fields for this sheet are described below:

    * store_name:

        This field parallels the store_name field on the Calendars sheet. Usage should be consistent to ensure the correct zipcodes are paired with the appropriate stores.

    * postal_code:

        Self explanatory, these are the zipcodes in the areas serviced by the stores. These values are generated using a third-party tool and then manually entered into the sheet. The process of generating zipcodes is explained in a later section.

    * primary_store:

        This field seems to determine whether or not a store is the primary store for the area. At this time it is always set to True.

    * latitude:

        The latitude of the zipcode's location, determined from that zipcode automatically using formulas on the sheet.

    * longitude:

        see latitude.

    * date_begin:

        This field parallels the store__date_open field from the Calendars sheet and should be the same value as the one used for that store on the Calendars page.

    * date_end:

        Similar to date_begin but parallels store__date_closed

*Zipcodes:

    This is an automatically generated sheet which is essentially a massive lookup table of zipcodes and their latitudes and longitudes. This sheet should not need to be edited.


**ADDING NEW STORES**:
1. The first step in adding a new store is to collect all of the necessary data needed to populate all of the required fields on the first page of the spreadsheet. Normally all of this information is included in the jira ticket requesting the store. 
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

    Example slot_creation
    store = Store.objects.active().get(id=3) 
    x = store.create_slots(date_begin='2017-03-28', num_days=1)
