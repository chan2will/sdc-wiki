#  Using the inContact Admin Panel to create new Automated Tasks.


## ListBuilder
_All automated tasks should use the SMS Endpoint in the ListBuilder._

**Task name** will be used as the descriptor for email notification to business as well as a portion of the name of the file.

**Skill ID** should be provided by business. It is the skill id associated with inContact's system and relates to the template used for the task.

**Query text** is a direct SQL statement used to generate the report used in building the list. Most of these queries can be found on Periscope and will just require modification for our purpose. Build the query out in the QA Dashboard. Remove all comments and run the query thru a multi-line to single line tools such as http://tools.knowledgewalls.com/onlinemultilinetosinglelineconverter. This will ensure that unwanted characters such as tabs are not being stored and condenses the size of the string in the varchar field.

**Custom Mapping** has a one-2-one relationship with inContacts column mapping. When the column has 'Column' as the key it will be mapped to a standard column used in their system. A column without 'column' in the key will be added to the custom mapping that business has setup for the template being used. Each key value pair should be on its' own line. Delimiter used to separate the key values is a colon. Ex:
thierColumn:our_field
customer_mapping:to_field
Field names used are created by the alias in the SQL query.

## Run Schedules
Sending times are in EST using the 24 hour clock.

Interval task will cause the selected task to be run at an interval no more than the specified limit in minutes.

**Specify EITHER the time OR interval for sending** 

**Active** selection will allow the task to be run regardless of mode.

**DEBUG Mode** _DOES NOT upload a file_ to the inContact system for sending. It generated the file and sends it to the specified email address fro the schedule for review.

**Listening Email** is a single email address to send success, fail, and debugging messages to. Business has set, centerofexcellence@smiledirectclub.com,  as a group email that the needed individuals have access to. This should be the default used for production.